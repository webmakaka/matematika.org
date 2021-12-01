---
layout: page
title: Материалы для изучению языка программирования Rust
description: Материалы для изучению языка программирования Rust
keywords: программирование, языки, rust, книги, видеокурсы
permalink: /study/lang/rust/
---

<br/>

# Материалы для изучению языка программирования Rust

<br/>

### [Book] Стив Клабник, Кэрол Никол | Программирование на Rust [RUS, 2021]

<br/>

Оригинал 2019

**Перевод**  
https://doc.rust-lang.ru/book/

**GitHub:**  
https://github.com/rust-lang/book/

<br/>

```
$ cd ~/tmp/
$ curl https://sh.rustup.rs -sSf | sh

$ rustup update

$ source $HOME/.cargo/env
$ rustc --version
$ cargo --version
```

<br/>

```
$ rustup show
Default host: x86_64-unknown-linux-gnu
rustup home:  /home/marley/.rustup

stable-x86_64-unknown-linux-gnu (default)
rustc 1.56.1 (59eed8a2a 2021-11-01)
```

<br/>

## Видео по книге

https://www.youtube.com/watch?v=OX9HJsJUDxA&list=PLai5B987bZ9CoVR-QEIN9foz4QCJ0H2Y8&index=1

### [Настройка neovim для работы с языком Rust](/study/lang/rust/neovim/)

<br/>

### 01 - Rust Lang Tutorial! - Getting Started

```
$ cargo new hello_cargo
$ cd hello_cargo/
$ cargo build
$ cargo run
$ cargo check
$ cargo build --release
```

<br/>

### 02 - Programming a Guessing Game in Rust!

**cargo.toml**

<br/>

```
[dependencies]
rand = "0.5.5"
colored = "2.0.0"
```

<br/>

**main.rs**

<br/>

```rust
use std::io;
use rand::Rng;
use std::cmp::Ordering;
use colored::*;

fn main() {
    println!("Guess the number!");

    let secter_number = rand::thread_rng().gen_range(1,101);

    println!("The secret number is: {}", secter_number);

    loop {

    println!("Please input your guess");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = match guess.trim().parse() {
        Ok(num) => num,
        Err(_) => continue,
    };

    println!("You guessed: {}", guess);

    match guess.cmp(&secter_number){
     Ordering::Less => println!("{}", "Too small!".red()),
     Ordering::Greater => println!("{}", "Too big!".red()),
     Ordering::Equal => {
         println!("{}", "You win!".green());
         break;
     }
    }
    }
}
```

<br/>

```
$ cargo build
$ cargo run
```
