## G.CAR.02 Crate 的 Cargo.toml 中应该包含必要的元信息

**【级别】** 建议

**【描述】**

在 Cargo.toml 中应该包含必要的元信息，以便使用者知道它的作用。
此外，若要将 `crate` 发布到 crates.io 上的话，这些信息也是必须的。可参考 The Cargo Book 中的[相关介绍](https://doc.rust-lang.org/cargo/reference/manifest.html)。

**【反例】**

```toml
# This `Cargo.toml` is missing a description field:
[package]
name = "clippy"
version = "0.0.212"
repository = "https://github.com/rust-lang/rust-clippy"
readme = "README.md"
license = "MIT OR Apache-2.0"
keywords = ["clippy", "lint", "plugin"]
categories = ["development-tools", "development-tools::cargo-plugins"]
```

**【正例】**

```toml
# This `Cargo.toml` includes all common metadata
[package]
name = "clippy"
version = "0.0.212"
description = "A bunch of helpful lints to avoid common pitfalls in Rust"
repository = "https://github.com/rust-lang/rust-clippy"
readme = "README.md"
license = "MIT OR Apache-2.0"
keywords = ["clippy", "lint", "plugin"]
categories = ["development-tools", "development-tools::cargo-plugins"]
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cargo_common_metadata](https://rust-lang.github.io/rust-clippy/master/#cargo_common_metadata) | yes           | no           | cargo      | allow |
