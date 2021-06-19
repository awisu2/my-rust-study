# my-rust-study

公式のドキュメントを元に勉強したことをまとめています(2021/05 頃)  
英語の勉強がてら作っているので正確性及びこれに関する一切の責任は負いません。

また、このリポジトリを見るまでもなく公式に非公式日本語版が存在します。そちらのほうが正確でしょう

- [The Rust Programming Language 日本語版 \- The Rust Programming Language 日本語版](https://doc.rust-jp.rs/book-ja/)

## links

- [The Rust Programming Language \- The Rust Programming Language](https://doc.rust-lang.org/book/title-page.html)

## other

その他番外で気になったこと

- 公式ドキュメントは順に読んでいくようにできています
  - 途中詳細がわからない部分は、前後のドキュメントを読むことによって解決される可能性があります
- package 名はケバブケース
  - 参考: [Packages and Crates \- The Rust Programming Language](https://doc.rust-lang.org/book/ch07-01-packages-and-crates.html)

## まとめ

- about coding rule
  - indent: space 4
  - end of code must set semicolon (`;`)
- Cargo.toml: cargo コマンドで作ったパッケージの管理ファイル
  - ライブラリの追加などのもこのファイルで行う
  - 実際にインストールされるのは、 `cargo build` のとき
- Crate: rust におけるパッケージのこと
  - crate の提供元: [crates\.io: Rust Package Registry](https://crates.io/)

### 重要コマンド

- `rustc --version` : インストールチェックに。cargo に吸収されるため殆ど使わない
- `cargo new {package}` : 新規パッケージ作成
- `cargo run` : ビルド及び実行
- `cargo build [--release]` : ビルド(--release をつけると release 用にビルド)
- `cargo check` : コンパイルしないビルドチェック(早い)
- `cargo doc --open`: 利用している crate の使用ドキュメントを生成しブラウザで閲覧

### 簡易コード

```rust
use std::io;
// Rng traitをスコープに入れる
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number!");

    let start = 1;
    let end = 100;

    // range expression (`1..101` と `1..=100` は整数であれば等価)
    let secret_number = rand::thread_rng().gen_range(start..=end);

    // rust say arm => {}
    println!("The secret number is: {}", secret_number);

  　// like while
    loop {
        println!("Please input your guess. {} ~ {}", start, end);

        let mut guess = String::new();

        io::stdin()
            // 入力待ち
            .read_line(&mut guess)
            // Resultのエラーチェック(エラー発生時は panic)
            .expect("Failed to read line");

        // parse()の帰り値もResultだが、matchで細かく処理も可能
        // guessが再宣言されているが上書きではなく、複数の型を保有
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => {
                println!("! input number");
                continue;
            }
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        };
    }
}
```
