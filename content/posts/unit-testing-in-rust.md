---
title: "Unit Testing in Rust"
date: 2020-05-12T08:01:54+01:00
draft: false
tags: ["Rust", "testing"]
tldr: Rust tooling is fantastic
codelang: rust
code: "#[test]

fn test_add() {

\  	assert_eq!(4, add(2,2));

}"
---

If you've been through the process of adding unit tests in C++ with gmock or
OCaml or python you know that you at least need to create a new file, add some
linking flags, import a library and trigger the test suite from code and then
execute the test program sepately.

No wonder it takes some painful code review comment to get people to start writing tests.

`Rust` turns that pain into pleasure.

Let's say you have this main file:

```rust

pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    println!("{}", add(2,3));
}

```

How do you test it. Well, I'm glad you asked. Just add this in the `SAME` file:
```rust
#[test]
fn test_add() {
    assert_eq!(4, add(2,2));
}
```

That's it.

So the whole file is:

```rust
fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    println!("{}", add(2,3));
}

#[test]
fn test_add() {
    assert_eq!(4, add(2,2));
}
```

Ain't that pretty?

----

So, be a good citizen and start learning `Rust`. Also, we're `looking for contributions` for some `sweet lookin' code`.