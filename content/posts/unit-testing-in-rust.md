---
title: "Unit Testing in Rust (or why I love Rust)"
date: 2020-05-12T08:01:54+01:00
draft: false
tags: ["Rust", "testing"]
tldr: Rust tooling is fantastic

---

If you've been through the process of adding unit tests in C++ with gmock or
OCaml or python you know that you at least need to create a new file, add some
linking flags, import a library and trigger the test suite from code and then
execute the test program separately.

No wonder it takes a not so subtle code review comment to get people to start writing tests.

Let's take another use case. You clone a big repository, maybe even yours from a while ago, and you want to change the implementation of a function. Maybe that function does not have a unit test. Or maybe you want to extract the functionality of a function which, again, does not have a test.

I have seen a few things people to to go around this:
- Create a new 'main' only file what only executes that function you want to test or develop. If you write C++ you need to list out all the dependencies that the new file needs, all link flags and cross your fingers and hope to work.
- Create a test file which you have to ditch afterwards. Probably you let it live there and you never call it in your CI and someone tells you to remove it from the review. Maybe nobody. I do that mostly in `python`
- Add your test next to some other existing tests but it doesn't quite fit anywhere. Also, maybe you just simply want to call that function with a few inputs to see what it generates. That's not really a test, it's just you trying to call the function directly, rather than trying to call the top level main file. So it doesn't fit really anyhere.

You're basically struggling to do TDD because your tooling does not enable you to do so. Even if you have good intentions, it's annoyingly painful.

---

`Rust` turns that pain into pleasure.

Let's say you have this literally is your  `main.rs` file:

```rust

pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

fn main() {
    println!("{}", add(2,3));
}

```

How do you test it. Well, I'm glad you asked. Just add this in the **SAME** file:
```rust
#[test]
fn test_add() {
    assert_eq!(4, add(2,2));
}
```

That's it. So the whole file `main.rs` becomes:

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

The beauty of this is that:
- all you had to do is add code in the **same** file as the function you want to test
- that code **does not** get compiled in the new binary
- you did not have to create any new files, no new linking flags, no test suite, no new executable
----

Be a good citizen and start learning `Rust`.