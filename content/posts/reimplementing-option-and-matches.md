---
title: "(Non) Functional Rust, Option and Patterns"
date: 2021-04-05T18:22:28+01:00
draft: false
summary: Patterns of writing code, functional or not, seem to appl
---

I ramdomly stumbled upon this [Pull Request](https://github.com/ocaml/ocaml/pull/1940/) where the author is adding `Option` to the OCaml standard library. Pretty confused why this only happened in 2018, but maybe I am reading this wrongly.

I in this PR you can see how OCaml's expressivity shines. Here's a snippet of the implementation (more or less):

```OCaml
type 'a t = 'a option = None | Some of 'a

let none = None
let some v = Some v
let value o ~default = match o with Some v -> v | None -> default
let get = function Some v -> v | None -> invalid_arg "option is None"
let bind o f = match o with None -> None | Some v -> f v
let join = function Some (Some _ as o) -> o | _ -> None
let map f o = match o with None -> None | Some v -> Some (f v)
let fold ~none ~some = function Some v -> some v | None -> none
let iter f = function Some v -> f v | None -> ()
let is_none = function None -> true | Some _ -> false
let is_some = function None -> false | Some _ -> true
let to_result ~none = function None -> Error none | Some v -> Ok v

```

I thought about writing it in Rust to see how it would look like, what can I learn along the way and how much more verbose it gets.

## Type definition

```OCaml
type 'a t = 'a option = None | Some of 'a
```

```rs
pub enum Maybe<T> {
    Just(T),
    Nothing,
}
```

Not too bad, very close.

## Constructors

```OCaml
let none = None
let some v = Some v
```

```Rust
impl<T> Maybe<T> {
    pub fn nothing() -> Self {
        Self::Nothing
    }

    pub fn just(value: T) -> Self {
        Self::Just(value)
    }
}
```

Ok, somewhat different, but not too bad. A few reasons:
- You could move `nothing` and `just` outside of the `impl` block but your would not gain too much.
- The main difference is really due to OCaml not having to type annotate as long as the compiler can figure it out. In Rust the functions and their arguments just **have** to be annotated with the return types. This is what adds the extra verbosity.
- Misc: OCaml has the `.mli` file, Rust doesn't. Any `.ml` file is already a module, just like rust, so you get the same things implicitly. One difference is that in OCaml the `type t` parametrizes the entire module. You can't get the same in Rust.

## Predicates

```OCaml
let is_none = function None -> true | Some _ -> false
let is_some = function None -> false | Some _ -> true
```

Rust attempt 1:
```rs
pub fn is_none(self) -> bool {
    match self {
        Self::Nothing => true,
        _ => false,
    }
}
pub fn is_some(self) -> bool {
    match self {
        Self::Something(_) => true,
        _ => false,
    }
}
```

Both readable, difference in whitespace really.

Rust attempt 2:
```rs
pub fn is_none(self) -> bool {
    matches!(self, Maybe::Nothing)
}

pub fn is_some(self) -> bool {
    matches!(self, Maybe::Just(_))
}
```

Damn. That's much better, although it's a semi`hack`. More about it at the end.

## Map and Bind

```OCaml
let map f o = match o with None -> None | Some v -> Some (f v)
let bind o f = match o with None -> None | Some v -> f v
```

```rs
pub fn map<U, F: FnOnce(T) -> U>(self, f: F) -> Maybe<U> {
    match self {
        Self::Just(something) => Maybe::Just(f(something)),
        Self::Nothing => Maybe::<U>::Nothing,
    }
}

pub fn bind<U, F: FnOnce(T) -> Maybe<U>>(self, f: F) -> Maybe<U> {
    match self {
        Self::Just(something) => f(something),
        Self::Nothing => Maybe::<U>::Nothing,
    }
}
```

At first sight, not that bad, just whitespace. However, looking at the implementation you can actually see that the `map` and `bind` are templated by the function type. You can't just define:
```rs
pub fn map<U>(self, f: FnOnce(T) -> U) -> Maybe<U>;
```

Because the compiler yells at you:
```rs
pub fn map<U>(self, f: FnOnce(T) -> U) -> Maybe<U> {
                    ^ doesn't have a size known at compile-time
```

You can try all the things the compiler messages tell you to. God bless its sould for the struggle, but the rabbit hole just gets deeper. This really illustrates how difficult it is to pass functions as parameters in Rust and how fairly simple idiomatic functional style starts breaking due to the lifetimes and borrowing rules, etc.

It's easy it you make it a template, it resolves everything at compile time. I am still not 100% why this works.

From all of this, and some previous experience, a few lessons that I learned:
- Rust is not functional (this has been said before)
- Lifetimes and borrowing are a blessing and a curse. A unique feature, by all means, but not a cure for everything
- Rust doesn't work for everything. I don't want to struggle to compile something as easy as passing a function as parameter. The struggle is worth it if you really need all the benefits that Rust offers compared to, say, OCaml. And yes, there are loads of benefits, especially regarding the availability of open souces libraries, not even going into domain specifics where functional is better than imperative. It may be worth it, although I would prefer if I can just write my functions like: `let map o f = .. ` and that's it.

In Rust `bind` is called `and_then`.

A test:

```rs
#[test]
fn test_bind() {
    use Maybe::*;

    let two_multiplied_by = |x: i32| x * 2;
    let two_divided_by = |x| if x == 0 { Nothing } else { Just(2 / x) };
    let two_added_to = |x: i32| x + 2;

    let res = Just(0)
        .bind(two_divided_by)
        .bind(two_divided_by)
        .bind(two_divided_by)
        .map(two_multiplied_by)
        .map(two_multiplied_by)
        .map(two_added_to)
        .map(two_multiplied_by)
        .map(two_added_to);

    assert_eq!(res, Nothing);
}
```

## Closing thoughts

It's pretty cool to see some abstractions (re)discovered across time, languages and problems and how they are (similarly) implemented in different languages. As a proof it's just the fact that both Rust and OCaml are using `map`, `bind`/`and_then`, that `matches!` is a thing, that the Rust `?` operator is also a thing and people actually use them heavily.

More interestingly, it points to the fact that there are some models/patterns of writing code are universal, that are somewhat domain-agnostic and that if you learn them you will define better APIs, write more extensible code and you bank on the fact that these lessons have already been learned before.

Other two examples are `Option` and `Result`. Sure, both examples of the same thing, sum types, but the reality is that among all sum types, these two are used the most. I really miss them in C++/Java (old java) where the ways to indicate the something fails is through return codes, or exceptions. Just the fact that you can't indicate that something can succeed or fail but through a whole new mechanism shows how a language can get in your way.

Keep coding!