# Blog

- `$ cd themes && git clone https://github.com/athul/archie.git`
- `$ hugo server -D` to test locally
- `$ hugo new posts/hello-world.md`
- `$ hugo` to publish


## TODO
- [ ] modify theme to have the preview as highlighted code
- [ ] get custom domain and link it
- [ ] 30 days daily challenges
- [ ] newsletter box - get a `post` endpoint via aws lambda & dynamodb

## Posts ideas:
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