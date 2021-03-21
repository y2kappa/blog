---
title: "Hello World, yet another programming blog."
date: 2020-05-10T19:17:56+01:00
draft: false
tags: ["OCaml", "functional"]
tldr: Functional programming ftw

---

I recently listened to a podcast with `Elizabeth Gilbert` where she mentioned that sometimes when stumbling upon a well written sentence, it makes her want to stand up an clap at the beauty of it.

I believe the same concept applies to code. Or at least it has happened to me a few times while reading code and being like `Ok, that's beautiful`.

I stand by the idea that code is meant to be read, that, heuristically, code is probably read 10 times more than it is written and therefore we should also optimize for that.

Of course, this is not about a single dimension of code, such as readability, expressiveness, efficiency, etc. It's everything, measured as a whole.

#### So I decided to write a blog about code I think is beautiful for me, that makes me want to clap.


---

And so, just as a first taster, I'd like to start with `OCaml`'s variant type, pattern matching and minimal function syntax:


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

For the uninitiated, `am_i_vegetarian` is a function which takes one parameter.

Also, another one from the heart: when I first saw pattern matching I was mind blown. I still am every time I create a variant type. It irritates me when I want to write a more sophisticated enum in Java or C++ and I can't do variants easily.

-----

Have a great day.