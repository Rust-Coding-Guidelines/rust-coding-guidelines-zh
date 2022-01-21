## G.CMT.02  使用行注释而避免使用块注释

**【级别】** 建议

**【描述】**

尽量使用行注释（`//` 或 `///`），而非块注释。

对于文档注释，仅在编写模块级文档时使用 `//!`，在其他情况使用 `///`更好。

**【反例】**

```rust
/*
 * Wait for the main task to return, and set the process error code
 * appropriately.
 */

mod tests {
    //! This module contains tests

    // ...
}

#![doc = "Example documentation"]

#[doc = "Example item documentation"]
pub enum Foo {}
```

**【正例】**

当 `normalize_comments = true`  时：

```rust
// Wait for the main task to return, and set the process error code
// appropriately.

// 在使用 `mod` 关键字定义模块时，在 `mod`之上使用 `///` 更好。
/// This module contains tests
mod tests {
    // ...
}

//! Example documentation

/// Example item documentation
pub enum Foo {}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`normalize_comments`](https://rust-lang.github.io/rustfmt/?#normalize_comments) | false（默认） true（推荐） | No| 将 `/**/` 注释转为 `//`|
| [`normalize_doc_attributes`](https://rust-lang.github.io/rustfmt/?#normalize_doc_attributes) | false（默认） | No| 将 `#![doc]` 和 `#[doc]` 注释转为 `//!` 和 `///` |