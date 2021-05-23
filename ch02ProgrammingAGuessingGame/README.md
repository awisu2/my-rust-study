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
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

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
