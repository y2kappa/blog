---
title: "A less dreadful linked list (or why I like OCaml)"
date: 2020-05-10T22:18:20+01:00
draft: false
tags: ["OCaml", "functional"]
summary: Let's assume the entire universe hates linked lists. But in OCaml
---

Let's assume the entire universe hates linked lists, but somehow functional languages make them bearable. I don't mean to trash them too harshly, I did learn a lot about memory management in C++ using linked lists, but that came at a cost to my soul.
<br><br>

That being said, check this out:

```ocaml
type linked_list =
    | End
    | Node of int * linked_list

let ll = Node (1, Node(2, Node(3, End)));;

```

Printing:

```ocaml
let rec stringify ll =
    match ll with
    | End -> "End"
    | Node (x, nxt) -> Printf.sprintf "%d -> %s" x (stringify nxt);;

print_string @@ stringify ll;;

```

Output:
```
1 -> 2 -> 3 -> End
```

I mean, come on!