# 包管理

Cargo 不仅仅是包管理，它还是一个 Workflow 工具。

---

## P.CAR.01 

【描述】



---



## G.CAR.01  Crate 的 Cargo.toml  中应该包含必要的元信息

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cargo_common_metadata](https://rust-lang.github.io/rust-clippy/master/#cargo_common_metadata) | yes           | no           | cargo      | allow |

### 【描述】

在 Cargo.toml 中应该包含必要的元信息，以便使用者知道它的作用。

【正例】

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

【反例】

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

## G.CAR.02   Feature 命名应该避免否定式或多余的前后缀

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [negative_feature_names](https://rust-lang.github.io/rust-clippy/master/#negative_feature_names) | yes           | no           | cargo      | allow |
| [redundant_feature_names](https://rust-lang.github.io/rust-clippy/master/#redundant_feature_names) | yes           | no           | cargo      | allow |

### 【描述】

Feature 命名应该避免出现 `no-` 或 `not-` 之类的否定前缀，或诸如 `use-`，`with-` 前缀或 `-support`后缀。Feature 的目的是正向的，可选的特性，使用否定式命名和它的目的背道而驰。

【正例】

```toml
[features]
default = ["abc", "def", "ghi"]
abc = []
def = []
ghi = []
```

【反例】

```toml
# The `Cargo.toml` with negative feature names
[features]
default = ["with-def", "ghi-support"]
no-abc = []
with-def = []   // redundant
ghi-support = []   // redundant
```

## G.CAR.03   Cargo.toml  中依赖包版本不要使用通配符

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [wildcard_dependencies](https://rust-lang.github.io/rust-clippy/master/#wildcard_dependencies) | yes           | no           | cargo      | allow |

### 【描述】

【正例】

```toml
[dependencies]
regex = "1.5"
```

【反例】

```toml
[dependencies]
regex = "*"
```

