---
date: 2024-08-04T07:30:55.393278523Z
title: Rust Asynchronous Programming
---

# Some Important concepts

## Concurrency vs Parallelism
- Concurrency (`_-_-_`) : the execution of tasks is interleaved 
- Parallelism (`=====`) : multiple tasks are executing at the same time

## Reactor Pattern

https://en.wikipedia.org/wiki/Reactor_pattern


### Future

A `Future` represents the processing whose result may be available at a _future_ point in time.


### Executor

The _Executor_ is the working horse of the asynchronous processing. He pretty much represents the runtime that is able to continuously request the result of every `Future` until they return the `Ready` state. Registering a `Future` at the _Executor_ to allow the same to run it to completion is called _spawning_. As part of this the `Future` is wrapped into a structure that allows proper handling of processing and re-spawning of pending futures. In the Rust libraries this wrapper is typically called a _Task_.

### Async

`async` can be seen as syntactic sugar. Every function preceded with the `async` keyword will be translated into a `function` that returns a `Future` containing the processing logic of the original function.

### Await

`await` is used to tell the compiler that the current sequential processing shall be paused until the value of an asynchronous processing is available


## Context

In the current version of Rust, the `Context` that will be handed over to the `Future` while polling the same only contains a reference to a `Waker`. This `Waker` can be used to _wake_ the processing of a `Future` at a later point in time, when the result of ths `Future` is likely to be present.

# ? in async Blocks

The compiler cannot infer the error type of the `async` block when using `?` inside the `async` block

For example, this code
```rust
let fut = async {
  foo().await?;
  bar().await?;
  Ok(())
}
```

will trigger this error

```shell
error[E0282]: type annotations needed
 --> src/main.rs:5:9
  |
4 |     let fut = async {
  |         --- consider giving `fut` a type
5 |         foo().await?;
  |         ^^^^^^^^^^^^ cannot infer type
```

But currently, there's no way to give `fut` a type to explicitly specify the return type of an `async` block. To work around this, use the "turbofish" operator to supply the success and error types for the `async` block:

```rust
let fut = async {
    foo().await?;
    bar().await?;
    Ok::<(), MyError>(()) // <- note the explicit type annotation here
};
```


