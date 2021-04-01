---
title: "Higher Level Languages"
date: 2021-04-01T23:25:11+01:00
draft: false
tldr: (Programming) languages as translation mechanisms for converting higher level concepts, ideas, into lower level representations - soundwaves or code.
---

One way of looking at programming languages or *languages*, in general, is as translation vehicles from higher level concepts into lower level representations. For example, *English* converts a higher level concept, i.e. an idea, a thought, into a lower level representation, i.e. soundwaves.

Product managers write (high level) specs, that is what they expect the product to be. Engineers, process that input, and compile it along with the rest of their understanding of the subsystems and produce an even lower representation, i.e. C++ code to create another request type and a yellow button on the screen.

If we agree with the concept that the job of a programmer is to translate ideas, requirements, visions of a product into lower level representations, or machine code, then we can imply that the best thing a programmer can do is to use as higher level abstractions as available to them.

That could mean:

- Using Python instead of C++. The more verbose you are the more likely you are to make a mistake.
- Using existing libraries, rewriting as little as possible. That basically means to picking a language with:
    - a good package manager
    - a thriving open source community
- Using expressive languages. One example is to use sum types. Nobody ever, in their right minds, thought that a `Dog` is something which belongs to the *Pet* types of things, or in `javaspeak` something which implements the Pet interface. Normal people think that a pet is `a Dog or a Cat`. That's much better expressed in OCaml: `type pet = Dog | Cat` rather than `public class Doc implements Pet`
- Decouple, use modules, abstract away as much as possible in logically cohesive ways and forget about it. That means having a useful module system and an easy way to package them (because nobody likes monoliths). In that case use OCaml, Rust. I don't think Python or C++ are particularly good at this.
- Use a language specialised for that domain - I would prefer Rust for performance critical systems, Python for testing/trying an ML model, Typescript for UI, OCaml for anything if it had a bigger community.

In some cases, requirements come with more constraints than just simply to making a screen with boxes. Sometimes it's CPU cycles, since more time spent processing could mean more milliseconds billed by the cloud provider. Sometimes the requirements come with an SLA, where the end client expects a task to complete within an interval. In that case, giving up on higher level abstractions is the trade off necessary to satisfy the constraints. In these cases I believe a language like OCaml or Rust can save the day, since they provide a good tradeoff, strong performance with good expressivity.

Keep coding.
