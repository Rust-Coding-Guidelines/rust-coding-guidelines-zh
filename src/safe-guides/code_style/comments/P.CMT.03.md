## P.CMT.03  使用行注释而避免使用块注释

**【描述】**

尽量使用行注释（`//` 或 `///`），而非块注释。这是Rust社区的约定俗成。

对于文档注释，仅在编写模块级文档时使用 `//!`，在其他情况使用 `///`更好。

说明： `#![doc]` 和 `#[doc]` 对于简化文档注释有特殊作用，没有必要通过 `rustfmt` 将其强制转化为 `//!` 或 `///` 。

**【反例】**

```rust
// 不符合

/*
 * Wait for the main task to return, and set the process error code
 * appropriately.
 */
mod tests {
    //! This module contains tests

    // ...
}
```

**【正例】**

当 `normalize_comments = true`  时：

```rust
// 符合

// Wait for the main task to return, and set the process error code
// appropriately.

// 符合
// 在使用 `mod` 关键字定义模块时，在 `mod`之上使用 `///` 更好。

/// This module contains tests
mod tests {
    // ...
}

// 符合
#[doc = "Example item documentation"]
pub enum Foo {}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`normalize_comments`](https://rust-lang.github.io/rustfmt/?#normalize_comments) | false（默认） true（推荐） | No| 将 `/**/` 注释转为 `//`|
| [`normalize_doc_attributes`](https://rust-lang.github.io/rustfmt/?#normalize_doc_attributes) | false（默认） | No| 将 `#![doc]` 和 `#[doc]` 注释转为 `//!` 和 `///` |