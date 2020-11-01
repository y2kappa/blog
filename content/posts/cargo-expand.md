---
title: "Cargo Expand"
date: 2020-11-01T11:01:15Z
draft: false
---

1. `main()`

```rs
fn main() {}
```


```rs
#![feature(prelude_import)]
#[prelude_import]
use std::prelude::v1::*;
#[macro_use]
extern crate std;
fn main() {}
```

2. `vec![]`

```rs
fn main() {
    let x = vec![];
}
```

```rs
fn main() {
    let x = ::alloc::vec::Vec::new();
}
```

3. `vec![1, 2, 3]`
```rs
fn main() {
    let x = vec![1, 2, 3];
}
```

```rs
#![feature(prelude_import)]
#[prelude_import]
use std::prelude::v1::*;
#[macro_use]
extern crate std;
fn main() {
    let x = <[_]>::into_vec(box [1, 2, 3]);
}
```

Notes: Wtf is `<[_]>`.

4. `#[derive(Debug)]`

```rs
#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
    name: String,
}
fn main() {}
```

```rs
struct Point {
    x: i32,
    y: i32,
    name: String,
}
#[automatically_derived]
#[allow(unused_qualifications)]
impl ::core::fmt::Debug for Point {
    fn fmt(&self, f: &mut ::core::fmt::Formatter) -> ::core::fmt::Result {
        match *self {
            Point {
                x: ref __self_0_0,
                y: ref __self_0_1,
                name: ref __self_0_2,
            } => {
                let mut debug_trait_builder = f.debug_struct("Point");
                let _ = debug_trait_builder.field("x", &&(*__self_0_0));
                let _ = debug_trait_builder.field("y", &&(*__self_0_1));
                let _ = debug_trait_builder.field("name", &&(*__self_0_2));
                debug_trait_builder.finish()
            }
        }
    }
}
fn main() {}
```

5. `#[derive(Clone)]`

```rs
#[derive(Clone)]
struct Point {
    x: i32,
    y: i32,
    name: String,
}
fn main() {}
```

```rs
struct Point {
    x: i32,
    y: i32,
    name: String,
}
#[automatically_derived]
#[allow(unused_qualifications)]
impl ::core::clone::Clone for Point {
    #[inline]
    fn clone(&self) -> Point {
        match *self {
            Point {
                x: ref __self_0_0,
                y: ref __self_0_1,
                name: ref __self_0_2,
            } => Point {
                x: ::core::clone::Clone::clone(&(*__self_0_0)),
                y: ::core::clone::Clone::clone(&(*__self_0_1)),
                name: ::core::clone::Clone::clone(&(*__self_0_2)),
            },
        }
    }
}
fn main() {}
```

6. `println!()`
```rs
fn main() {
    println!("Hello world");
}
```

```rs
fn main() {
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1(
            &["Hello world\n"],
            &match () {
                () => [],
            },
        ));
    };
}

```

```rs
fn main() {
    println!("Hello {}", "world");
}
```
```rs
fn main() {
    {
        ::std::io::_print(::core::fmt::Arguments::new_v1(
            &["Hello ", "\n"],
            &match (&"world",) {
                (arg0,) => [::core::fmt::ArgumentV1::new(
                    arg0,
                    ::core::fmt::Display::fmt,
                )],
            },
        ));
    };
}
```


7. `info!()`

```rs
use log::info;
fn main() {
    {
        let lvl = ::log::Level::Info;
        if lvl <= ::log::STATIC_MAX_LEVEL && lvl <= ::log::max_level() {
            let _ = ::core::fmt::Arguments::new_v1(
                &["Hello"],
                &match () {
                    () => [],
                },
            );
            ::log::__private_api_log_lit(
                "Hello",
                lvl,
                &("syntactic_sugar", "syntactic_sugar", "src/main.rs", 3u32),
            );
        }
    };
}
```

This was a rabbit hole. Wanted to see what `__private_api_log_lit` does and it seems it's implemented by `log` which calls a static global object `LOGGER` that is defined in `log`. This interface allows you to set your global logger using the interface: ```log::set_logger```and it needs to implement the `Log` trait.
```rs
pub trait Log: Sync + Send {
    fn enabled(&self, metadata: &Metadata) -> bool;
    fn log(&self, record: &log::Record);
    fn flush(&self);
}
```

8. `panic!()`

```rs
fn main() {
    panic!("WTF");
}
```

```rs
fn main() {
    {
        ::std::rt::begin_panic("WTF")
    };
}
```

Also a rabbit hole. It calls either `__rust_start_panic` which is a `C` binding, or `intrinsics::abort()`

Also, the signature of `abort` is weird, returning a bang, which means that it never returns.
```rs
pub fn abort() -> !;
```

Also, it's imported like `extern "rust-intrinsic"`.
```
https://doc.rust-lang.org/beta/unstable-book/language-features/intrinsics.html
```