---
layout: page
title: Стив Клабник, Кэрол Никол | Программирование на Rust [RUS, 2021]
description: Стив Клабник, Кэрол Никол | Программирование на Rust [RUS, 2021]
keywords: программирование, языки, rust, книги, видеокурсы, [Book] Стив Клабник, Кэрол Никол | Программирование на Rust [RUS, 2021]
permalink: /study/lang/rust/rust-lang-book/
---

<br/>

# [Book] Стив Клабник, Кэрол Никол | Программирование на Rust [RUS, 2021]

**Оригинал 2019**

<br/>

**Перевод**  
https://doc.rust-lang.ru/book/

**GitHub:**  
https://github.com/rust-lang/book/

**Видео по книге:**  
https://www.youtube.com/watch?v=OX9HJsJUDxA&list=PLai5B987bZ9CoVR-QEIN9foz4QCJ0H2Y8&index=1

<br/>

### [Инсталляция в linux](//gitops.ru/dev/rust/setup/)

<br/>

### Hello World!

<br/>

**main.rs**

<br/>

```rust
fn main() {
    println!("Hello, World!");
}
```

<br/>

```rust
$ rustc main.rs
$ ./main
```

<br/>

### [Настройка neovim для работы с языком Rust](/dev/tools/rust/neovim/)

<br/>

### 01 - Rust Lang Tutorial! - Getting Started

```
$ cargo new hello_cargo
$ cd hello_cargo/
$ cargo build
$ cargo run
$ cargo run -q
```

<br/>

**response:**

```
Hello, world!
```

<br/>

```
$ cargo check
$ cargo build --release
```

<br/>

### 02 - Programming a Guessing Game in Rust!

**cargo.toml**

```
[dependencies]
rand = "0.5.5"
colored = "2.0.0"
```

<br/>

**main.rs**

```rust
use colored::*;
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");

    let secter_number = rand::thread_rng().gen_range(1, 101);

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

        match guess.cmp(&secter_number) {
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
$ cargo fmt
$ cargo build
$ cargo run -q
```

<br/>

### 03 - Common Programming Concepts in Rust

<br/>

### 04 - Understanding Ownership in Rust

<br/>

### 05 - Structs in Rust

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let mut user1 = User {
        email: String::from("bogdan@mail.com"),
        username: String::from("bogdan123"),
        active: true,
        sign_in_count: 1,
    };

    let name = user1.username;
    user1.username = String::from("wallace123");

    let user2 = build_user(String::from("kyle@mail.com"), String::from("kyle123"));

    let user3 = User {
        email: String::from("james@mail.com"),
        username: String::from("james123"),
        ..user2
    };
}

fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

<br/>

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let rect = Rectangle {
        width: 30,
        height: 50,
    };

    let rect1 = Rectangle {
        width: 20,
        height: 40,
    };

    let rect2 = Rectangle {
        width: 40,
        height: 50,
    };

    let rect3 = Rectangle::square(25);

    println!("rect can hold rect1: {}", rect.can_hold(&rect1));
    println!("rect can hold rect2: {}", rect.can_hold(&rect2));

    println!("rect: {:#?}", rect);

    println!(
        "The area of the rectangle is {} square pixels.",
        rect.area()
    );

    println!("rect3: {:#?}", rect3);
}
```

<br/>

### 06 - Enums and Pattern Matching in Rust

```rust
enum IpAddrKind {
    V4(u8, u8, u8, u8),
    V6(String),
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

fn main() {
    let four = IpAddrKind::V4;
    let six = IpAddrKind::V6;

    let localhost = IpAddrKind::V4(127, 0, 0, 1);
}

fn route(ip_kind: IpAddrKind) {}
```
