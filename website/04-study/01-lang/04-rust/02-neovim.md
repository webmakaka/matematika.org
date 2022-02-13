---
layout: page
title: Настройка neovim для работы с языком Rust
description: Настройка neovim для работы с языком Rust
keywords: rust, neovim
permalink: /study/lang/rust/neovim/
---

<br/>

# Настройка neovim для работы с языком Rust

<br/>

Neovim пока как из [здесь](//jsdev.org/env/ide/neovim/)

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

<!-- <br/>

```
// Не знаю зачем. Наверное, чтобы не было ошибки!

$ sudo apt-get install ctags
$ cargo install rusty-tags
``` -->

https://rust-analyzer.github.io/manual.html#vimneovim

<br/>

```
:CocInstall coc-rust-analyzer
```

<br/>

```
$ rustup component add rustfmt
$ cargo fmt
```

<br/>

### Может что полезное есть. Недоразобрался

**БОЖЕСТВЕННЫЙ nvim как IDE для Python, Rust и всех-всех-всех — встречаем LSP!**  
https://www.youtube.com/watch?v=PA7zZNJXJEk

<br/>

**Vim Rust Setup**
https://www.youtube.com/watch?v=evNNcc4E2DQ

https://www.youtube.com/watch?v=LfJZboTeDt0
