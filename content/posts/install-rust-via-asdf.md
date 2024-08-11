---
date: 2024-08-11T14:03:58.600002167Z
title: Install Rust via asdf
---
# Install

[asdf](/posts/asdf/) install [rust](/posts/rust/) latest

```shell
❯ asdf install rust latest
info: downloading installer
warning: it looks like you have an existing installation of Rust at:
warning: /home/xxx/.asdf/shims
warning: It is recommended that rustup be the primary Rust installation.
warning: Otherwise you may have confusion unless you are careful with your PATH
warning: If you are sure that you want both rustup and your already installed Rust
warning: then please reply `y' or `yes' or set RUSTUP_INIT_SKIP_PATH_CHECK to yes
warning: or pass `-y' to ignore all ignorable checks.
error: cannot install while Rust is installed
warning: continuing (because the -y flag is set and the error is ignorable)
info: profile set to 'default'
info: default host triple is x86_64-unknown-linux-gnu
info: syncing channel updates for '1.80.1-x86_64-unknown-linux-gnu'
info: latest update on 2024-08-08, rust version 1.80.1 (3f5fd8dd4 2024-08-06)
info: downloading component 'cargo'
  8.2 MiB /   8.2 MiB (100 %)   5.3 MiB/s in  2s ETA:  0s
info: downloading component 'clippy'
info: downloading component 'rust-docs'
 15.8 MiB /  15.8 MiB (100 %)   8.0 MiB/s in  3s ETA:  0s
info: downloading component 'rust-std'
 26.7 MiB /  26.7 MiB (100 %)   8.1 MiB/s in  3s ETA:  0s
info: downloading component 'rustc'
 65.0 MiB /  65.0 MiB (100 %)   8.2 MiB/s in  8s ETA:  0s
info: downloading component 'rustfmt'
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
info: installing component 'rust-std'
 26.7 MiB /  26.7 MiB (100 %)  24.1 MiB/s in  1s ETA:  0s
info: installing component 'rustc'
 65.0 MiB /  65.0 MiB (100 %)  26.2 MiB/s in  2s ETA:  0s
info: installing component 'rustfmt'
info: default toolchain set to '1.80.1-x86_64-unknown-linux-gnu'

  1.80.1-x86_64-unknown-linux-gnu installed - rustc 1.80.1 (3f5fd8dd4 2024-08-06)


Rust is installed now. Great!

To get started you need Cargo's bin directory 
(/home/xxx/.asdf/installs/rust/1.80.1/bin) in your PATH
environment variable. This has not been done automatically.

To configure your current shell, you need to source
the corresponding env file under /home/xxx/.asdf/installs/rust/1.80.1.

This is usually done by running one of the following (note the leading DOT):
. "/home/xxx/.asdf/installs/rust/1.80.1/env"            # For 
sh/bash/zsh/ash/dash/pdksh
source "/home/xxx/.asdf/installs/rust/1.80.1/env.fish"  # For fish
```

Set version

```shell
asdf global rust latest
asdf local rust latest
```

Recreate shims for version of a package

```shell
asdf reshim rust
```

List the plugins and versions that provide a command
```shell
❯ asdf shim-versions cargo   
rust 1.70.0
rust 1.75.0
rust 1.76.0
rust 1.78.0
rust 1.79.0
rust 1.80.0
rust 1.80.1
```

# rust-analyzer

[Rust](/posts/rust/) add component `rust-analyzer`
```shell
❯ rustup component add rust-analyzer

❯ asdf shim-versions rust-analyzer   
rust 1.70.0
rust 1.75.0
rust 1.76.0
rust 1.78.0
rust 1.79.0
rust 1.80.0
rust 1.80.1
```


# Environment variables

-   `RUSTUP_DIST_SERVER` (default:https://static.rust-lang.org) Sets the root URL for downloading static resources related to Rust. You can change this to instead use a local mirror, or to test the binaries from the staging directory.
-   `RUSTUP_DIST_ROOT`  (default: https://static.rust-lang.org/dist) Deprecated. Use `RUSTUP_DIST_SERVER` instead.
-   `RUSTUP_UPDATE_ROOT` (default: https://static.rust-lang.org/rustup) Sets the root URL for downloading self-updates.

Read more on rust [Environment variables](https://rust-lang.github.io/rustup/environment-variables.html)

