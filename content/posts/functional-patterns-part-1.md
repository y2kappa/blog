---
title: "Functional Patterns Part 1"
date: 2021-04-03T23:47:24+01:00
draft: true
---

What I want in C++ / Python.

Let's say you're Spotify and you need to expose this public API with this simplified implementation.

```C++

?? get_songs(string user_id) no_throw(?)
{
    auto connection = get_db_connection();

    // User data
    auto user = get_user(connection, user_id);
    auto downloaded_songs = get_songs(connection, user_id);
    auto subscription = get_subscription(connection, user_id);

    // Consider permissions, subscriptions, etc
    auto available_songs = get_permissioned(user, downloaded_songs, subscription);

    return available_songs;
}

```

Some questions:
1. What should be the signature of this function?
2. What should be the signature of the internal functions?
3. How do you implement this in the cleanest possible way, such that:
    - Readability is prioritised, you want multiple developers to contribute to this codebase and that it doesn't turn into a dumping ground of `ifs`, early returns, `try` and `catch` etc?
    - The code is self documenting
    - You enable code reuse, the same functions can be used in different places with similar calling patterns
    - You can test it
4. How can I stay expressive, how can the code show my intent without the language getting in my way?

Possible ways:
    - return codes / enums and return by reference
    - throwing exceptions
    - Variant/Result types (how can I get away from the if() early return pattern)

## First attempt

## How you do it in Rust

```rs
enum Error { DbError, UserError, PermissionError, SubscriptionError, Other(String) }

fn get_db_connection() -> Result<Connection, Error>;
fn get_user(connection: &DbConnection, user: String) -> Result<User, Error>;
fn get_songs(connection: &DbConnection, user: &User) -> Result<Vec<Song>, Error>;
fn get_subscription(connection: &DbConnection, user: &User) -> Result<Subscription, Error>;
fn get_permissioned(user: &User, songs: Vec<Song>, &Subscription) -> Result<Vec<Song>, Error>;

fn get_songs(user_id: String) -> Result<Vec<Song>, Error> {
    let connection = get_db_connection()?;
    let user = get_user(connection, user_id)?;
    let downloaded_songs = get_songs(connection, user_id)?;
    let subscription = get_subscription(connection, user_id)?;
    get_permissioned(user, downloaded_songs, subscription)
}
```

This is literally as clean as it gets. That if you know what the `?` operator does. I would say it's fairly easy to teach, significantly easier than teaching monads.

## How you do it in OCaml

```ocaml
type error = DbError | UserError | PermissionError | SubscriptionError | Other of string

val get_db_connection : unit -> (Connection.t, error) result
val get_user : Connection.t -> string -> (user, error) result
val get_songs : Connection.t -> user -> (song list, error) result
val get_subscription : Connection.t -> user -> (subscription, error) result
val get_premissioned : user -> song list -> subscription -> (song list, error) result

let get_songs user_id =
    let* connection = get_db_connection () in
    let* user = get_user connection user_id in
    let* downloaded_songs = get_songs connection user_id in
    let* subscription = get_subscription connection user_id in
    get_permissioned user downloaded_songs subscription
```

This is also fairly clean, however there is this `magic` `let*` operator which, again, makes is very f weird. It works to say "belive me, this is fine", but I don't think it's very easy to actually explain what is really happening.

