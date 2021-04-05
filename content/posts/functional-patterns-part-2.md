---
title: "Functional Patterns Part 2: Monads"
date: 2021-04-04T00:27:20+01:00
draft: true
---

Given these APIs:

```ocaml
type error = DbError | UserError | PermissionError | SubscriptionError | Other of string

val get_db_connection : unit -> (Connection.t, error) result
val get_user : Connection.t -> string -> (user, error) result
val get_songs : Connection.t -> user -> (song list, error) result
val get_subscription : Connection.t -> user -> (subscription, error) result
val get_premissioned : user -> song list -> subscription -> (song list, error) result

```

We need to write this logic (TODO: remove the let* for the initial demo):

```ocaml
let get_songs user_id =
    let* connection = get_db_connection () in
    let* user = get_user connection user_id in
    let* downloaded_songs = get_songs connection user_id in
    let* subscription = get_subscription connection user_id in
    get_permissioned user downloaded_songs subscription

```

Let's say we know nothing of `monads` and `bind`.

How would you do this with pattern matching:

```ocaml
let get_songs user_id =
    match get_db_connection () with
    | Error smth -> Error smth              (* early return, propagate the error upwards *)
    | Ok connection ->
      (
        match get_user connection user_id with
        | Error smth -> Error smth
        | Ok user ->
          (
             let downloaded_songs_res = get_songs connection user_id in
             let subscription_res = get_subscription connection user_id in
             match downloaded_songs_res, subscription_res with
             | Error download_err, _ -> Error download_err
             | _, Error subscription_err -> Error subscription_err
             | Ok songs, Ok subscription ->
               (
                   get_permissioned user songs subscription
               )
          )
      )
```

It seems we are repeating ourselves quite a lot using this pattern:

```ocaml
match result with
| Error err -> Error err
| Ok value -> continuation
(* .. *)
    match result with
    | Error err -> Error err
    | Ok value -> continuation
    (* .. *)
        match result with
        | Error err -> Error err
        | Ok value -> continuation
        (* .. *)
            match result with
            | Error err -> Error err
            | Ok value -> continuation
```

You're pretty mucy trying to do early exit whenever the api you are calling is giving you an error. It's a fairly standard pattern, but quite a verbose one expecially in Python, C++. The solution that functional people came up with is `bind` which is:

1. Give me a result and a function
2. If there result contains data
    - then: I will apply the function and return a Result
    - else: I will return the original result

The fact that you receive a result and you return a result allows you to reuse this pattern on and on. It wouldn't work if you didn't return an Result, so you need to keep your APIs consistent, you need to make them follow a pattern. When you follow this pattern you get to benefit from the `bind` function.

So what does bind do (you already know it by now):

```ocaml
let bind result f =
    match result with
    | Ok v -> f v
    | _ -> result
```

So you can rewrite your code like this:

```ocaml
let get_songs user_id =
    let connection = get_db_connection () in
    let user = Result.bind connection (get_user user_id connection)  in
    let downloaded_songs = Result.bind connection (get_songs user_id) in
    let subscription = Result.bind connection (get_subscription user_id) in
    get_permissioned user downloaded_songs subscription
```

In this case, if getting a connection fails, then connection is Error, therefore user is Error and downloaded songs is Error and so on. Every time `Result.bind` is invoked the Error branch of `bind` is executed and it returns early. It's pretty cool to have this pattern and to desing your APIs around these patterns. It allows you to chain computations in a very predictable, idiomatic way.

Now, that we have established the pattern, it's getting a bit weird with the syntax, but the logic remains.

From Joel, there used to be a `>>=` infix operator:

```ocaml
let ( >>= ) o f = bind o f
```

So you would end up doing (it's getting wierd). It's like chaining callbacks in javascript with promises, it should be fairly familiar

```ocaml
let get_songs user_id =
    get_db_connection () >>= (fun connection -> get_user user_id connection >>= (fun user_id -> ...))
```

The `>>=` operator decides if it calls the callback or if it returns early, the Error branch.

However, rew

let get_songs user_id =
    get_db_connection             >>= fun connection  ->
    get_user user_id connection   >>= fun user        ->
    get_songs user_id connection  >>= fun songs       ->

    let user = Result.bind connection (get_user user_id connection)  in
    let downloaded_songs = Result.bind connection (get_songs user_id) in
    let subscription = Result.bind connection (get_subscription user_id) in
    get_permissioned user downloaded_songs subscription
```

But it's super weird to see `Result.bind` everywhere and it's not extra clear what's really happening.