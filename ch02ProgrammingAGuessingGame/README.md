# Programming a Guessing Game

- 一緒に guessing game の作成をしましょう
- 実際のプログラミングを通していくつかの一般的な Rust のコンセプトを伝えます
- let, match, 関数, 関連機能, 外部 crates(パッケージ)の使い方などを学ぶことができます
- guessing game のルール
  - 1 ~ 100 の数字をランダムに生成
  - ユーザに入力を求め、生成した値に対し大きすぎるか小さすぎるかを表示します
  - 入力した値が一致したら、お祝いメッセージを表示して終了します

## links

- [Programming a Guessing Game \- The Rust Programming Language](https://doc.rust-lang.org/book/ch02-00-guessing-game-tutorial.html)

## setting up

### プロジェクトの作成

```bash
cargo new guessing_game
cd guessing_game
cargo run
```

- note: Cargo.toml を開いて、authors やそれ以外のパラメータを確認し。間違っていたら修正しましょう
- note: cargo コマンドについては chapter1 で説明しています

## processing a guess

f: src/main.rs

```rust
// use: standard library 内の io ライブラリをスコープに入れる
//
// rustはすべてのプログラムに std::prelude がスコープに入っていますが、
// それ以外のライブラリが必要な場合は use ステートメントで取り込む必要があります
//
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    // let: 変数の生成。 default では immutable(変更不可)
    // mut: mutable(可変) にする
    let mut guess = String::new();

    io::stdin()
        // 入力待ち
        .read_line(&mut guess)
        // expect(): `io::Result` の関数でエラー処理を行う
        // read_line() は、`io::Result`を返却する
        // 記載しない場合コンパイルエラーとなる
        .expect("Failed to read line");

    // {} はプレースホルダー、引数と一致する
    println!("You guessed: {}", guess);
}
```

```bash
cargo run
```

### 解説

- `use std::io;` によって　 standard library 内の io ライブラリをスコープに入れています
  - rust は、すべてのプログラムに `prelude` ライブラリがスコープに入っていますが、これに含まれないライブラリが必要な場合は use ステートメントで取り込む必要があります
- prelude モジュール
  - すべての rust プログラムのスコープに入っている module
  - 今回のプログラムで利用されている `String` など基礎的な type を export している
  - [std::prelude \- Rust](https://doc.rust-lang.org/std/prelude/index.html)
- library, module, type の使い分け
  - library: `std::io`
  - module: library と module の境界は曖昧？実際に利用するときは library, 仕様記載のときには module というのかも
    - `prelude`のドキュメント、_Module std::prelude_ というタイトルで記載されている
    - `std::io` のドキュメント _Module std::io_ というタイトルが記載
  - type: `String`
- `let mut guess = String::new();`
  - `let`は変数の生成に使用されます。　(ex `let foo = bar;`)
  - rust の変数は default では immutable(変更不可)です
    - この扱いについては chapter3 で説明しています。 [Variables and Mutability \- The Rust Programming Language](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html#variables-and-mutability)
  - `mut`を変数の前に仕様することによって mutable(可変)にすることが可能です(ex: `let mut bar = 5`)
  - `String::new()`: 新しい String のインスタンスを返却する関数
    - 標準ライブラリで提供される、拡張可能な文字列型(utf-8 エンコード)
    - new は空の文字列を返却します。また new 関数は他のタイプでもインスタンス生成用の関数として見ることが多いでしょう
  - `::` は特定のタイプの関連を表現します
    - 本例では, new は String の関連関数です
    - いくつかの言語では、static method ということもあります
- `io::stdin().read_line(&mut guess)`
  - 先頭行に`use std::io`を記載しなかった場合、`std::io::stdin` と記載することで動作します
  - `read_line()`によって入力待ちの状態になる
  - `$mut guess`は guess のリファレンスを渡しています
    - リファれウンスは複雑なため詳細は chapter4 で説明しますが、今のところは以下の部分を抑えておいてください
    - default では変数は imutable なのでこの記載が必要
    - `$guess`ではなく`$mut guess`と記載
    - この記載によって、入力を複数のメモリ上にコピーすることなく一つの変数で扱えるようになっている
- `.expect("Failed to read line");`
  - `.foo()`のような構文では１行に記載できますが、長くなり読みづらくなるので、改行をするようにしましょう。
  - `read_line()`は、`io::Result`を返却します.
    - Result タイプは enum 型です(詳細は chapter6 にて)
    - Result は _Ok_ または _Err_ の値を持っていて、Ok の場合は値の生成が成功したことを、Err の場合はオペレーションが失敗したこととその理由を保持しています。
    - `io::Result`は`expect`関数を持っていて、Err の場合には expect に渡された文字列をスクリーンに表示し、プログラムを停止します。
      - Ok の場合には、受け取った値をそのまま返却します(今回の場合は byte 数)
  - `expect`を記載しなかった場合コンパイル時に warning が発生します
    - プログラム上考えられるエラーをハンドリングしていないからです
  - 正しい対処としては、処理が継続できるような何らかのエラーハンドリングを記載すべきですが、今回は問題があったときにクラッシュしてほしいだけなので、expect 関数の記載で大丈夫です
    - エラーハンドリングについては chapter9 で説明します
- `println!("You guessed: {}", guess);`
  - 入力された値を用事しています
  - _{}_ は placeholder です
    - 値を表現する小さなカニのハサミだと考えてください(カニは rust 特有の表現)
    - _{}_ は複数記載できて、１つ目の _{}_ は１つ目の引数,２つ目の _{}_ は２つ目の引数と合致します

### 実験

- `use std::io;` を `use std::io::stdin` にしても動作するか？ > する
  - `io::stdin()` は　`stdin()` に修正する必要あり

## Generating a Secret Number

毎回変化する 1~100 のランダム値を生成し、ゲームを面白くしましょう  
standard library にはまだ含まれていないので、Rust チームが提供する rand crate(library) を利用します

f: Cargo.toml

```toml
[dependencies]
rand = "0.8.3"
```

```bash
$ cargo build
    Updating crates.io index
  Downloaded getrandom v0.2.3
  Downloaded ppv-lite86 v0.2.10
  Downloaded rand v0.8.3
  Downloaded rand_chacha v0.3.0
  Downloaded rand_core v0.6.2
  Downloaded 5 crates (167.3 KB) in 2.70s
   Compiling cfg-if v1.0.0
   Compiling ppv-lite86 v0.2.10
   Compiling getrandom v0.2.3
   Compiling rand_core v0.6.2
   Compiling rand_chacha v0.3.0
   Compiling rand v0.8.3
   Compiling guessing_game v0.1.0 (D:\develop\study\my-rust-study\ch02ProgrammingAGuessingGame\guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 14.30s
```

### 解説

rand crate が取得されました

- dependencies セクション
  - プロジェクトでどの外部ライブラリのどのバージョンが必要かを記載します
  - バージョンは標準的な Semantic Versioning を解釈します
    - [Semantic Versioning 2\.0\.0 \| Semantic Versioning](https://semver.org/)
    - 例: `^0.8.3` と記載することで 0.8.3 以上 0.9 未満で最新のバージョンを表現します
  - この記載により利用されるバージョンの Api が、0.8.3 の public Api と互換性があるとみなします
- crate は[crates\.io: Rust Package Registry](https://crates.io/) から入手されます
  - Crates.io は、Rust のエコシステムで他のユーザが利用可能な形で、open source の Rust project が post されます
- ここでは rand のみを記載しましたが、cargo は rand が依存している crates をチェックしダウンロード及びコンパイルしています
- もしサイド `cargo build`を実行しても先程のような Download や Compile の表示はなく、Finished と出力されるでしょう
  - Cargo.toml ファイル(レジストリ？)を変更しない限り、すでにダウンロード、コンパイルが完了していることを cargo は認識しているからです
  - **main.rs** を書き換えた場合でも、変更に対する最小限のコンパイルのみが行われます
- Cargo.lock ファイルによる再ビルド性の保証
  - cargo はあなたや他の人が同じアーティファクトによって、いつでも再ビルドする仕組みを補修しています
  - `cargo build`を実行したときに、実際にインストールされた package 情報が、Cargo.lock に記載されます
    - これ以降、Cargo.lock に記載されたバージョンの Crage がインストールおよび使用されるようになります
- `crate update`: Crate のアップデートと新しいバージョンの取得
  - Cargo.lock を除外し、Cargo.toml に記載された内容での最終バージョンを取得します
  - 結果は Cargo.lock に上書きされます
  - 細かいことは、Chapter14 で説明します

### ランダム番号の生成

f: src/main.rs

```rust
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

- `use rand::Rng` を追加しました。Rng はランダム番号を生成します
  - この trait(英訳：特性)は、これらを利用する際にスコープに入っている必要があります。
    - 詳細は Chapter10 にて
- `rand::thread_rng()` : ランダム関数のジェネレータを提供
  - これはローカルスレッドで実行され OS によってシードされます(多分ランダム性の保証の話)
- `gen_range()`: 引数に range expression を取り、ランダム数を生成する
  - Rng trait によって定義されているため利用できる
- range expression: 範囲指定について
  - `start..end` という書き方で、範囲指定ができる
  - 開始数は含むが、終了数は含まないため `1..101` で "1 <= x < 101" となる
    - `1..=100` と記載してもよい

### create の使用方法について

- 各 crate や、trait の利用方法は crate ドキュメントに記載されています
- `cargo doc --open` によってドキュメント が生成され、ブラウザで閲覧できます
  - 作られるのは Cargo.toml に記載されている dependencies に依存します
  - 気になった crate をサイドバーから選んで閲覧してください

### 番号比較

```rs
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    // --snip--

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

- `std::cmp::Ordering;`は、enum です
  - Result と似ていますが、 `Less`, `Greater`, `Equal` のいずれかです
- cmp メソッドは、比較できる値なら何でも比較し、Ordering enum を返却します
  - 比較したい対象のリファレンスを引数に取ります
- match 式
  - arms({}のこと)で構成され、先頭の引数と値が一致したときに実行する式で構成されます
  - match は様々な機能を有しており、Chapter6, 18 で説明します
- ビルドエラー
  - rust は、強力な static type system を保有しています。
    - 型推論もありますヨ
  - 上記のコードは guess が String、secret_number が Int のため型違いによりエラーになります
  - i32, u32, i64 など複数の数値型が存在します　(default: i32)
    - u: unsigned, 数値: bit

### 型変換

最終的に、私達は入力値を数値に変換する必要があります

```rs
    let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

- guess を上書きしているますが大丈夫でしょうか？ 大丈夫です
- この機能は shadowing といい、一つの値を外の型に変換して利用できます
  - これにより一つの引数を型推論で利用することが可能です(詳細は chapter3)
- `trim()` は入力により発生する、空白や、改行文字を削除します
- `parse()`は数種類の数値タイプを解析します。そのため変換後の型を指定しておく必要があります
  - `:` のあとの `u32` annotation(注釈) により型を指定しています
  - u32 は小さな正の値としては良い選択です。(他の型については chapter3)
- これで同じ方による比較が可能になりました
- `parse()` の返却値は `Result`で、上記でも記載しているように`expect()`によって処理されています

### 複数回できるようにし、正解時に終了、数値以外の入力に対応

```rs
    // --snip--

    loop {
        // --snip--

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
```

- `loop`によってなんども入力が行われるようになりました
  - 内部に移植したコードのインデントは合わせるようにしましょう
- `parse()` の返却値 `Result` を match で判定するようにしています
  - 失敗時、`continue`により、loop を最初からやり直します
- `guess.cmp(&secret_number)`での判定で `Ordering::Equal`のとき`break;`により、loop を抜けています
- これで、guessing game(推測ゲーム)が完成です
