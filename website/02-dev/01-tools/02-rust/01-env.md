---
layout: page
title: Инсталляция rust в linux
description: Инсталляция rust в linux
keywords: программирование, языки, rust, Инсталляция rust в linux
permalink: /dev/tools/rust/env/
---

<br/>

# Инсталляция rust в linux

<br/>

```
$ cd ~/tmp/
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

$ source $HOME/.cargo/env

$ rustup update
```

<br/>

```
$ rustc --version
rustc 1.65.0 (897e37553 2022-11-02)

$ cargo --version
cargo 1.65.0 (4bc8f24d3 2022-10-20)
```

<br/>

```
$ rustup show
Default host: x86_64-unknown-linux-gnu
rustup home:  /home/marley/.rustup

stable-x86_64-unknown-linux-gnu (default)
rustc 1.65.0 (897e37553 2022-11-02)
```

<br/>

```
// Использовать последнюю версию rust
// $ rustup override set nightly

// Использовать стабильную версию rust
$ rustup override set stable
```

<br/>

### Faster Linking

<br/>

```
$ sudo apt-get install -y lld clan libssl-dev
```


<br/>

```
$ vi ~/.cargo/config.toml
```

<br/>

```
# On Linux:
# - Ubuntu, `sudo apt-get install -y lld clang`
# - Arch, `sudo pacman -S lld clang`
[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]
```

<br/>

### Дополнительные пакеты

<br/>

```
// Linting
$ rustup component add clippy

// Formatting
$ rustup component add rustfmt
```

<br/>

```
// monitors your source code to trigger commands every time a file changes
$ cargo install cargo-watch

// Code Coverage
$ cargo install cargo-tarpaulin

// Security Vulnerabilities
$ cargo install cargo-audit
```

<br/>

**rust-analyzer**
https://github.com/rust-analyzer/rust-analyzer

<br/>

```
$ cd ~/apps/
$ git clone https://github.com/rust-analyzer/rust-analyzer
$ cd rust-analyzer/

$ rustup update
$ cargo xtask install --server
$ rust-analyzer --version
```
