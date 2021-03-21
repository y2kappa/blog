# Blog

Hosted at [https://y2kappa.github.io/blog](https://y2kappa.github.io/blog).

## Build
- `$ cd themes && git clone git@github.com:adityatelange/hugo-PaperMod.git`
- `$ hugo server -D` to test locally
- `$ hugo new posts/hello-world.md`
- `$ hugo` to publish


## TODO
- [x] modify theme to have the preview as highlighted code
- [ ] get custom domain and link it
- [ ] 30 days daily challenges
- [ ] newsletter box - get a `post` endpoint via aws lambda & dynamodb

## Posts ideas:
- [ ] What is architecture / When should you rewrite your code? How important is it to do it even if deliverables are pressing.
- [ ] Testing Rust in AWS Lambda
- [ ] Timing your function execution
- [ ] Google tracing article:
    - https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit
    - replicating the flamegraph
- [ ] Proc macro which counts how many times a function was called
    - https://stackoverflow.com/questions/54582761/how-to-check-if-a-function-has-been-called-in-rust
    - https://users.rust-lang.org/t/rust-how-to-check-whether-a-function-being-called-or-not/25045/13
- [ ] Proc macro that checks code coverage
- [ ] Demonstrating `cargo expand`
    - [ ] syntactic sugar in rust
    - [ ] templates in Rust
- [ ] trying to learn C++ posts
- [ ] trying to learn Haskell posts
- [ ] trying to learn java posts
- [ ] A comparison between Rust and other languages
    - [ ] C++ pointers
    - [ ] Java interfaces & streams() and collect()
    - [ ] Python for loops & ranges & slices
    - [ ] OCaml pattern matching & variant types & structs
    - [ ] "let" from javascript etc
    - [ ] OCaml & Haskell deriving and syntax ppx
    - [ ] C++ macros
- [ ] new article on how you end up with bad code
    - so much tribal knowledge, so much knowledge comes from years of practice
    - such that initial code is unavoidably bad
    - arcitecure does not meet requirements anymore
- [ ] article about deleting code
- [ ] cargo depcheck like npm with warnings and clippy and github action
- [ ] cargo binary to create a template rust html