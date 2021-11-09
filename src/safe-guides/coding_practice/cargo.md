# 包管理

Cargo 不仅仅是包管理，它还是一个 Workflow 工具。这一节包含 Cargo 和 Crate 相关内容。

---

## P.CAR.01   应该尽量把项目划分为合理的 crate 组合

### **【描述】**

将整个项目按一定逻辑划分为合理的 crate，在工程方面，有利于组件化。并且 crate 是 Rust 的编译单元，也有助于提升编译时间。

但需要注意，crate 之间的依赖关系应该是单向的，避免相互依赖的情况。

但 Rust 中 编译时间、性能、编译大小之间，在考虑优化的时候，也是需要权衡的。 

内联是优化的关键，当编译单元越大，内联优化效果就越好。所以需要权衡 crate 划分的粒度。

## P.CAR.02    不要滥用 `features` 

### **【描述】**

Rust 的 features ，提供了方便的条件编译功能。从软件工程来说，features 应该是为了避免让用户依赖没必要依赖的功能而使用的。

在使用 features 的时候，应该考虑到底是不是真的需要 features。

滥用features会带来额外的测试和静态检查的难度，需要保证不同features下的测试覆盖和静态检查情况。

---

## G.CAR.01    当项目是可执行程序而非库时，建议使用 `src/main.rs` 和 `src/lib.rs` 模式

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

### **【描述】**

`crate` 结构类似于：

```text
src/
  -- lib.rs
  -- main.rs
```

或

```text
src/
  -- lib.rs
bin/
  -- main.rs
```

这样的好处有：

1. 便于单元测试。
2. 这样拆分有利于面向接口思考，让代码架构和逻辑更加清晰。

如果你编写的可执行程序比较复杂时，在 `main.rs`里需要依赖太多东西，那就需要创建 Workspace， 把 `main.rs` 在独立为一个 crate 了，而在这个 crate 内也没有必要再拆分为 `main`和 `lib` 了。

## G.CAR.02  Crate 的 Cargo.toml  中应该包含必要的元信息

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cargo_common_metadata](https://rust-lang.github.io/rust-clippy/master/#cargo_common_metadata) | yes           | no           | cargo      | allow |

### 【描述】

在 Cargo.toml 中应该包含必要的元信息，以便使用者知道它的作用。并且后续上传到crates.io上，这些信息也是必须的。

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

## G.CAR.03   Feature 命名应该避免否定式或多余的前后缀

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

## G.CAR.04   Cargo.toml  中依赖包版本不要使用通配符

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

