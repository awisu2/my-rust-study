# Getting Started

- インストール
- hello world の実践
- cargo を使う

## links

- [Installation \- The Rust Programming Language](https://doc.rust-lang.org/book/ch01-01-installation.html)

## installation

- コマンドラインツールの `rustup` を使ってダウンロード
  - rust のバージョンと、関連ツールの管理
- なにか理由があって rustup が使いたくない場合はこちら
  - [Install Rust \- Rust Programming Language](https://www.rust-lang.org/tools/install)
- 後述のサンプルコマンドは安定バージョンのインストールです

### linux or macOS でのインストール

```bash
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

NOTE: linker could not execute エラーが発生することがあります。この場合、C compilers の linker に問題がある可能性があるため、再インストールなどの見直しが必要かもしれません。

### windows でのインストール

事前に、C++ build tools for Visual Studio 2013 か、それ以降のバージョンが必要です。(インストール時は、"C++ build tools" "Windows 10 SDK and the English language pack components" にチェックを入れるのを忘れないように)

- [Microsoft C\+\+ Build Tools \- Visual Studio](https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/)

以下のリンクからインストーラをダウンロードしてインストールしてください(wsl 用のコマンドも記載)

- [Install Rust \- Rust Programming Language](https://www.rust-lang.org/tools/install)

ドキュメントないのコマンドは基本的に cmd.exe および PowerShell で動作します。

### install の確認

```bash
$ rustc --version
rustc 1.52.1 (9bc8c42bb 2021-05-09)

$ cargo --version
cargo 1.52.0 (69767412a 2021-04-21)
```

- インストール後、ターミナルなどは再起動する必要あり
- うまく行かない場合はこちらで質問できます。
  - [the official Rust Discord](https://discord.com/invite/rust-lang)
  - [The Rust Programming Language Forum](https://users.rust-lang.org/)
  - [Newest 'rust' Questions \- Stack Overflow](https://stackoverflow.com/questions/tagged/rust)

### update と uninstall

```bash
rustup update
```

```bash
rustup self uninstall
```

### local でドキュメントを読みたい場合

以下のコマンドで API ドキュメントがブラウザに立ち上がります

```bash
rustup doc
```

## hello world

基礎的な hello world のチュートリアルがありましたが、最終的に cargo コマンドでまとめられそうなので飛ばしています。

```bash
# プロジェクトの作成
$ cargo new hello_cargo
     Created binary (application) `hello_cargo` package
$ cd hello_cargo
# コンパイルと実行(新規の場合やコードに変化があった場合はCompileも同時に行ってくれる)
$ cargo run
   Compiling hello_cargo v0.1.0 (D:\develop\study\my-rust-study\ch01GettingStarted\hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 1.68s
     Running `target\debug\hello_cargo.exe`
Hello, world!
```

### 直接実行する方法

- 直接実行したい場合は `rustc main.rs && ./main`
- rustc でコンパイルされ実行ファイル main が生成される
  - windows の場合は main.exe, main.pdb
  - 実行ファイル名は、元のファイル名に準拠

### hello world プログラムでわかること

- 拡張子は、`.rs`
- `fn main() {}` は最初に動作する function
- インデントは 4 つの space です(tab は NG)
- `println!` はマクロです。関数で利用する場合は `!` を除く必要があります。(マクロの詳細は chapter19 で)
- `println!` に値を渡すことでスクリーンに文字列が print されること(ここでは"Hello, world!")
- コードの最後は semicolon (`;`) であること

### cargo について

- パッケージマネージャ
- rust をインストールすると同時に利用できるようになっている
- 多くの rust プロジェクトは cargo を利用していて、ドキュメントでも cargo を利用することを期待
- git リポジトリ付きでプロジェクトを作る場合 `cargo new --vcs=git hello_cargo_with_git`
  - git 以外の vcs(version control system)を選択したい場合は --vcs を変更することで可能
    - 詳細は `cargo new --help`
- **Cargo.toml**: cargo の構成管理ファイル
- **Cargo.lock**: cargo プロジェクトの依存関係をまとめたファイル
  - cargo コマンドで自動生成/更新されるため、手動更新は不要
- 生成したプロジェクトは"Hello world!" が出力される
- cargo を利用せず作ったプロジェクトを cargo に導入したい場合は src ディレクトリの中にコピーするだけで OK です

かんたんコマンド集

```bash
# buildと実行
$ cargo build
$ ./target/debug/hello_cargo

# buildと実行を同時
$ cargo run

# コードチェック(buildおよび実行はしないので早い)
$ cargo check

# リリースビルド(ビルドに時間がかかることあり)
$ cargo build --release
$ ./target/release/hello_cargo
```

- `cargo check` は build プロセスがないため高速にビルドが通るか確認できるコマンドです。
  - 複雑なプログラムなどを少しずつ確認しながら書くことができます
  - 実行時チェックではないことに注意

#### Cargo.toml

- cargo の構成管理ファイル
- Toml(Tom’s Obvious, Minimal Language) 形式
  - [TOML: Tom's Obvious Minimal Language](https://toml.io/en/)

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["awisu2 <awisu2@gmail.com>"]
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```
