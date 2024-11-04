---
title: "cargoでサブコマンドを簡単に作る"
emoji: "💭"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["rust", "cargo"]
published: true
---

## 概要

Cargoのサブコマンドとして自作のコマンドを実行する方法を示します。Cargoを使ったプロジェクト内で簡単にカスタムコマンドを呼び出せるようになります。
今回は、`src/bin/`以下にあるファイルをfzfを使って選択して実行するコマンド(`cargo-select`)を自作します。

![img](/images/cargo-select.gif)

## 手順

`$HOME/.cargo/bin/`以下に`cargo-`から始まる実行可能なファイルを置いておけば`cargo`のサブコマンドとして認識されます。

### 1. 実行形式のファイルを作成する

`src/bin/*.rs`ファイルをfzfで選択し、`cargo run --bin`で実行するスクリプトを作成します。

```sh
#!/bin/bash

BIN_DIR="src/bin"
TARGET=$(find $BIN_DIR -name "*.rs" | fzf)

if [ -z "$TARGET" ]; then
	echo "No target selected."
	exit 1
fi

TARGET_NAME=$(basename -s .rs "$TARGET")
cargo run --bin "$TARGET_NAME"
```

このスクリプトを `cargo-select` という名前で保存します。ファイルに実行権限を付与しておきます。

```sh
chmod +x cargo-select
```

### 2. 実行ファイルを配置する

次に、作成した実行ファイルを `$HOME/.cargo/bin/` ディレクトリに移動します。

```sh
mv cargo-select $HOME/.cargo/bin/
```

### 3. サブコマンドとして認識されているか確認する

`cargo --list`でサブコマンドの一覧が確認できます。`cargo-select`が含まれていればOKです。

```
$ cargo --list
Installed Commands:
    add                  Add dependencies to a Cargo.toml manifest file
    b                    alias: build
    bench                Execute all benchmarks of a local package
    build                Compile a local package and all of its dependencies
    c                    alias: check
    (中略)
    search               Search packages in crates.io
    select
    sort
    (後略)
```

### 4. 実行する

これで自作のコマンドが cargo のサブコマンドとして使えるようになります。以下のコマンドを実行してみてください。なお、`src/bin/`がないと動作しないことに注意してください。

```sh
cargo select
```

## 参考文献

- [Extending Cargo with Custom Commands](https://doc.rust-lang.org/book/ch14-05-extending-cargo.html)

