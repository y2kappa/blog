---
title: "Timing Your Function Execution (in Rust)"
date: 2020-09-19T20:27:20+03:00
draft: false
---

If you ever had to migrate some slow code (Python) to Rust and were mindblown by the speed gains, you are probably now always paranoid for adding extra new code, just so you don't ruin the amazing speed you gained. Those annoying new features adding extra cycles!

I know I am. I have an AWS lambda function that used to run for 15 seconds end-to-end in Python. I managed to get it to 1.5 seconds in Rust, so like a 10x gain, and now every time I add new functionality I cringe at the extra new miliseconds that it adds.

That's why I constantly measure the speed of my functions and try to stay within the previous limits. Having the metrics always right there in front of you makes you optimize right away rather than later on when you discover things are slow.

Also, it's nice to see that your complicated IO bound code takes very little, some vanity metrics to soften your ego.

And so I used to do this all the time:

```rust
let start = Instant::now();
// my logic
info!("function=call_firebase duration={}", start.elapsed());
```

This is everywhere in my code. No, I don't think it's ugly and yes it probably is useless in most of the places. But it gives me a sense of control. Also it keeps reminding me of what functions I might want to optimize.

But it's really verbose, so I developed a macro that does just that.

How to get started:

```
$ cargo new --bin hello
$ cd hello
$ cargo run
   Compiling hello v0.1.0 (hello)
    Finished dev [unoptimized + debuginfo] target(s) in 1.40s
     Running `target/debug/hello`
Hello, world!

```

`main.rs` is:
```rust
fn main() {
    println!("Hello, world!");
}
```

So now, instead of:
```
use std::time::Instant;

fn main() {
    let start = Instant::now();
    println!("Hello, world!");

    println!("function=main duration={:?}", start.elapsed());
}
```

You can use [timed](https://crates.io/crates/timed) and do:

```toml
[dependencies]
timed = "0.1.2"
```

```rust
use timed::timed;

#[timed]
fn main() {
    println!("Hello, world!");
}
```

```
: cargo run
Hello, world!
function=main duration=28.301µs
```

Next time we'll talk about how this crate works.