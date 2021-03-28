---
title: "Piping in Rust"
date: 2021-03-28T14:10:56+01:00
draft: false
summary: A macro way of piping functions together in Rust to simulate the functional pipe operator.
---


There are many really useful concepts in functional programming, each worth their praise, but today I feel like paying tribute to the `pipe` operator in particular. It's among my favourite things to use and makes me looking for it when I can't find it in another language.

Of course, it enables composition and all the good stuff that comes with composition and composing functions, but my personal reason is that it allows you to write linear code when the operations you're doing are linear and groups the code together and it's visually easier to separate from the rest of the code.


It enables you to avoid writing code like this:

```OCaml
let get_top_three_ user password =
  let id = get_user user password in
  let tasks = get_tasks id in
  let outstanding = List.filter (fun t -> not t.is_finished) tasks in
  let sorted = List.sort (fun t1 t2 -> if t1.priority > t2.priority then 1 else -1) outstanding in
  let top_three = take 3 sorted in
  top_three
```

In order to write it like this
```OCaml
let get_top_three user password =
  get_user user password
  |> get_tasks
  |> List.filter (fun t -> not t.is_finished)
  |> List.sort (fun t1 t2 -> if t1.priority > t2.priority then 1 else -1)
  |> take 3
```

Piping things together makes for one single block of variables and functions that seem to belong together. You can even factor it out in a single function. Having many `lets` makes it a bit indistinguishable from the rest of the code that surrounds it.

And so I missed it in Rust, especially when trying to compose some async functions. For example, this looks a little bit awful (subjectively, of course):

```rust
let x = f(1).await;
let y = g(x).await;
let z = f(y).await;
let w = f(z).await;
```

Or this:

```rust
let w = f(f(g(f(1).await).await).await).await;
```

Would it not be nice to be able to write something like this:

```rust
let w = pipe!(
    1
    => f
    => g
    => h
    => z
);
```

Async or not, OCaml or Rust, I find this code `better` just because the piping makes it all live together. So I wrote a macro that does it:

```rust
macro_rules! pipe {
    ($init:tt $(=>$fn:ident)+) => {{
        let mut r = $init;
        $( r = $fn(r).await; )*
        r
    }}
}
```

One of the things about Rust that I like is that the macro system is very powerful, it allows you to write macros easily (arguably) and safely and that the macros that are not just doing string replace like in C++, but come with their own expressive syntax.

Crate [here](https://crates.io/crates/ftools). Some OCaml [code](https://gist.github.com/y2kappa/e919be9d13ec42117b84bba6997ddaa5) to demonstrate it.