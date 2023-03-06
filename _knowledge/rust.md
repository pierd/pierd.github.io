---
title: Rust
layout: knowledge
tags: [rust]
---

## Auditing for security vulnerabilities

- [cargo-audit](https://github.com/RustSec/rustsec/tree/main/cargo-audit)
- [cargo-deny](https://github.com/EmbarkStudios/cargo-deny)

## Benchmarking

More info [here in performance book](https://nnethercote.github.io/perf-book/benchmarking.html).

Important to use `RUSTFLAGS="-C target-cpu=native"` to let the compiler use instructions specific to the current CPU.

Tools:
- [Criterion](https://github.com/bheisler/criterion.rs) - standard (almost everyone uses this one)
- [Bencher](https://github.com/bluss/bencher/) - an old port
- [rustfilt](https://github.com/luser/rustfilt) - demangle Rust symbols

## Books

- [The Rust Programming Language](https://doc.rust-lang.org/book/second-edition/)
- [Too Many Linked Lists](http://cglab.ca/~abeinges/blah/too-many-lists/book/)
- [Rust by Example](https://doc.rust-lang.org/rust-by-example/)
- [The Rust Performance Book](https://nnethercote.github.io/perf-book/)
- [The Little Book of Rust Macros](https://veykril.github.io/tlborm/)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
- [Rust Atomics and Locks](https://marabos.nl/atomics/)
- [Comprehensive Rust](https://google.github.io/comprehensive-rust/) - a 4 day Rust course by Android team
- [Futures Explained in 200 Lines of Rust](https://cfsamson.github.io/books-futures-explained/)

## Code coverage

- [tarpaulin](https://github.com/xd009642/tarpaulin)

## Error handling

- [anyhow](https://github.com/dtolnay/anyhow)
- [thiserror](https://github.com/dtolnay/thiserror)

## Faster linking

Put this in `.cargo/config.toml` in your project to use `lld` or `zld` instead of default linker.

```
# On Windows
# ```
# cargo install -f cargo-binutils
# rustup component add llvm-tools-preview
# ```
[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-pc-windows-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

# On Linux:
# - Ubuntu, `sudo apt-get install lld clang`
# - Arch, `sudo pacman -S lld clang`

[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]

# On MacOS, `brew install michaeleisel/zld/zld`

[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"]

[target.aarch64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"
```

More info why is [here](https://github.com/rust-lang/rust/issues/39915).

## Logging / tracing

- [log](https://github.com/rust-lang/log) + [env_logger](https://github.com/rust-cli/env_logger/)
- [tracing](https://github.com/tokio-rs/tracing)
- [bunyan](https://github.com/lukemathwalker/bunyan) - pretty printing JSON logs

## Utilities

- [cargo-watch](https://github.com/watchexec/cargo-watch) - watch for changes and execute
- [cargo-expand](https://github.com/dtolnay/cargo-expand) - expand macros
- [cargo-msrv](https://github.com/foresterre/cargo-msrv) - find the minimum supported Rust version
- [cargo-udeps](https://github.com/est31/cargo-udeps) - find unused deps
