---
title: "Hello World"
date: 2020-05-10T19:17:56+01:00
draft: false
tags: ["OCaml", "functional"]
tldr: Functional programming ftw
codelang: ocaml
code: "type food =

\  	| Potatoes

\  	| Tomatoes

\  	| Salmon
"
---

I recently listened to a podcast in which `Elizabeth Gilbert` mentioned that sometimes when she reads a well written sentence, it makes her want to stand up an clap for how well written that is.

I believe the same concept applies to code. I agree with the idea that code is meant to be read, that code is read 10 times more than it is written and therefore we need to optimize for that.

Of course, this is not about a single dimension of code, such as readability, expressiveness, efficiency, etc. It's everything, measured as a whole.

And so, because I'm hungry, I'd like to start with `OCaml`'s variant type, pattern matching and minimal function syntax:


```ocaml

type patty = Pork | Beans

type food =
    | Potatoes
    | Tomatoes
    | Salmon
    | Chicken
    | Burger of patty

let am_i_vegetarian food =
    match food with
    | Potatoes -> true
    | Tomatoes -> true
    | Burger (Beans) -> true
    | _ -> false

```

When I first saw pattern matching it blew my mind. Then when I saw how minimal is the syntax for writing functions my mind was blown again.

-----

And some beautiful poetry to go along:

```

I said to my soul, be still, and wait without hope
For hope would be hope for the wrong thing; wait without love,
For love would be love of the wrong thing; there is yet faith
But the faith and the love and the hope are all in the waiting.
Wait without thought, for you are not ready for thought:
So the darkness shall be the light, and the stillness the dancing.

```

Have a great day. We're looking for `contributions`. If you want to share a snippet of code you find beautiful, feel free to send a *Pull Request*.