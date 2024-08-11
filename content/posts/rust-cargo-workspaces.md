---
date: 2024-08-11T09:49:45.386677678Z
title: Rust Cargo Workspaces
---
[Cargo Workspaces](https://doc.rust-lang.org/cargo/reference/workspaces.html) is a feature of [Rust](/posts/rust/)'s package manager. Here's a step-by-step tutorial on how to set up a workspace  based on [the rust official online doc](https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html).

## Create a workspace root directory

Create a directory as the root of the a new workspace.

``` shell
$ mkdir my_workspace
$ cd my_workspace
```


## Initialize `Cargo.toml` and `[workspace]` section

Inside the workspace, create `Cargo.toml` file, add `[workspace]` section with member crates that going to be added to this file.

``` toml
[workspace]
resolver = "2"
members = ["member1", "path/to/member2", "crates/*"]
exclude = ["crates/foo", "path/to/other"]
default-members = ["path/to/member2", "path/to/member3/foo"]
```

The `members` list also supports [globs](https://docs.rs/glob/0.3.0/glob/struct.Pattern.html) to match multiple paths, using typical filename glob patterns like `*` and `?`. 

So, for the sake of convenience, we can use `crates/*` to  set all the sub directory under `crates` as member crates for the current workspace.

```toml
[workspace]
resolver = "2"
members = ["crates/*"]
```

## Add Member Crates

Under the workspace root directory, use `cargo new path [options]` to create member crates. By the way, the `path` should be matched with one of `members` items in the `[workspace]` section of workspace root `Cargo.toml`.

```shell
cargo new crates/crate1 --lib --vcs none # Creates a new library crate  
cargo new crates/crate2 --bin --vcs none # Creates a new binary crate
```


## Package selection

In a workspace, package-related Cargo commands like [`cargo build`](https://doc.rust-lang.org/cargo/commands/cargo-build.html) or [cargo run](https://doc.rust-lang.org/cargo/commands/cargo-run.html) can use the `-p` / `--package` command-line flags to determine which packages to operate on.

```shell
cargo run -p crate2
cargo add dependency -p crate1
```

## The key points

The following key points of workspaces are copied from [cargo/reference/workspaces.html](https://doc.rust-lang.org/cargo/reference/workspaces.html)
- Common commands can run across all workspace members, like `cargo check --workspace`.
- All packages share a common [`Cargo.lock`](https://doc.rust-lang.org/cargo/guide/cargo-toml-vs-cargo-lock.html) file which resides in the _workspace root_.
- All packages share a common [output directory](https://doc.rust-lang.org/cargo/guide/build-cache.html), which defaults to a directory named `target` in the _workspace root_.
- Sharing package metadata, like with [`workspace.package`](https://doc.rust-lang.org/cargo/reference/workspaces.html#the-package-table).
- The [`[patch]`](https://doc.rust-lang.org/cargo/reference/overriding-dependencies.html#the-patch-section), [`[replace]`](https://doc.rust-lang.org/cargo/reference/overriding-dependencies.html#the-replace-section) and [`[profile.*]`](https://doc.rust-lang.org/cargo/reference/profiles.html) sections in `Cargo.toml` are only recognized in the _root_ manifest, and ignored in member crates’ manifests.


## Ref

- https://doc.rust-lang.org/book/ch14-03-cargo-workspaces.html
- https://doc.rust-lang.org/cargo/reference/manifest.html
- https://doc.rust-lang.org/cargo/reference/workspaces.html
