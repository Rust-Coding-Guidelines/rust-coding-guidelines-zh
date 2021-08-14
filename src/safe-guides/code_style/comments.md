# 注释与文档

在 Rust 中，注释分为两类：普通注释和文档注释。普通注释使用 `//` 或 `/* ... */`，文档注释使用 `///`、`//!` 或 `/** ... **/`。

在原则和规则中提到「注释」时，包括普通注释和文档注释。当提到「文档」时，特指文档注释。

---

## P.CMT.01 不到必要的时候不要添加注释

### 【描述】

注释固然很重要, 但最好的代码应当本身就是文档。有意义的类型名、函数名和变量名, 要远胜过要用注释解释的含糊不清的名字。当有意义的类型名、函数名和变量名还不能表达完整的语义时，再使用注释。

不要描述显而易见的现象, 永远不要用自然语言翻译代码作为注释。

## P.CMT.02 文档应该始终基于 `rustdoc` 工具来构建

### 【描述】

Rust 语言提供 `rustdoc` 工具来帮助构建文档，所以应该始终围绕`rustdoc`工具的特性来构建项目文档。

## P.CMT.03 文档应该围绕 What 和 How 为核心来构建

### 【描述】 

文档应该始终围绕两个方向来构建：

1. What : 用于阐述代码为什么而构建。
2. how : 用于阐述代码如何去使用。

## P.CMT.04 注释和文档应该保持简短精干

### 【描述】

1. 文档内容用语应该尽量简短精干，不宜篇幅过长。请确保你的代码注释良好并且易于他人理解。
2. 使用通俗易懂的描述而尽量避免使用专业技术术语。好的注释能够传达上下文关系和代码目的。

## P.CMT.05 注释和文档使用的自然语言要保持一致

### 【描述】 

注释和文档尽量使用英文来填写，如果要使用中文，整个项目必须都使用中文。请确保整个项目中文档和注释都使用同一种文本语言，保持一致性。


## P.CMT.06 使用行注释而避免使用块注释

### 【描述】 

尽量使用行注释（`//` 或 `///`），而非块注释。

对于文档注释，仅在编写模块级文档时使用 `//!`，在其他情况使用 `///`更好。

### 【示例】 

【正例】

```rust
// Wait for the main task to return, and set the process error code
// appropriately.

// 在使用 `mod` 关键字定义模块时，在 `mod`之上使用 `///` 更好。
/// This module contains tests
mod tests {
    // ...
}

```

【反例】

```rust
/*
 * Wait for the main task to return, and set the process error code
 * appropriately.
 */

mod tests {
    //! This module contains tests

    // ...
}
```

## P.CMT.07 在文档中应该使用 Markdown 格式

### 【描述】 

Rust 文档注释支持 Markdown ，所以在编写文档注释的时候，应该使用 Markdown 格式。

### 【示例】 

模块级文档，来自于 Rust 标准库`std::vec`：

```rust
//! # The Rust core allocation and collections library
//!
//! This library provides smart pointers and collections for managing
//! heap-allocated values.
//!
//! This library, like libcore, normally doesn’t need to be used directly
//! since its contents are re-exported in the [`std` crate](../std/index.html).
//! Crates that use the `#![no_std]` attribute however will typically
//! not depend on `std`, so they’d use this crate instead.
//!
//! ## Boxed values
//!
//! The [`Box`] type is a smart pointer type. There can only be one owner of a
//! [`Box`], and the owner can decide to mutate the contents, which live on the
//! heap.
```

普通文档注释示例，来自标准库`Vec::new`方法：

```rust
    /// Constructs a new, empty `Vec<T>`.
    ///
    /// The vector will not allocate until elements are pushed onto it.
    ///
    /// # Examples
    ///
    /// ```
    /// # #![allow(unused_mut)]
    /// let mut vec: Vec<i32> = Vec::new();
    /// ```
    #[inline]
    #[rustc_const_stable(feature = "const_vec_new", since = "1.39.0")]
    #[stable(feature = "rust1", since = "1.0.0")]
    pub const fn new() -> Self {
        Vec { buf: RawVec::NEW, len: 0 }
    }
```

---

## G.CMT.01 注释应该有一定宽度限制

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`comment_width`](https://rust-lang.github.io/rustfmt/?#comment_width) | 80（默认） | No|  指定一行注释允许的最大宽度 |
| [`wrap_comments`](https://rust-lang.github.io/rustfmt/?#wrap_comments) | false（默认），true（建议） | No|  指定一行注释允许的最大宽度 |

### 【描述】

每行注释的宽度不能过长，需要设置一定的宽度，有助于提升可读性。`comment_width`可配合 `wrap_comments` 将超过宽度限制的注释自动分割为多行。

注意：`use_small_heuristics`配置项并不包括`comment_width`。

### 【示例】

【正例】

当 `comment_width=80` 且 `wrap_comments=true`时。

注意：这里 `wrap_comments`并未使用默认值，需要配置为 true。

```rust
// Lorem ipsum dolor sit amet, consectetur adipiscing elit,
// sed do eiusmod tempor incididunt ut labore et dolore
// magna aliqua. Ut enim ad minim veniam, quis nostrud
// exercitation ullamco laboris nisi ut aliquip ex ea
// commodo consequat.
```

【反例】

```rust
// Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
```


## G.CMT. 在每一个文件开头加入版权公告

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`license_template_path`](https://rust-lang.github.io/rustfmt/?#license_template_path) | 格式化每一个Rust文件（默认） | No|  指定许可证模版路径 |

### 【描述】

每个文件都应该包含许可证引用。为项目选择合适的许可证版本.(比如, Apache 2.0, BSD, LGPL, GPL)。

可以通过 `rustfmt` 的 `license_template_path` 配置项 和 `license.template`来自动化此操作。

### 【示例】

【正例】

来自 [TiKV](https://github.com/tikv/tikv/blob/master/etc/license.template) 项目的案例。可以命名为`.rustfmt.license-template`许可证模版。

```rust
// Copyright {\d+} TiKV Project Authors. Licensed under Apache-2.0.
```

在 `rustfmt.toml` 中配置：

```toml
license_template_path = ".rustfmt.license-template"
```

在代码文件中手工添加对应的注释 （自动插入功能还未支持）：

```rust
// Copyright 2021 TiKV Project Authors. Licensed under Apache-2.0.
```







---

## 参考

1. [RFC 505: API 注释约定](https://github.com/rust-lang/rfcs/blob/master/text/0505-api-comment-conventions.md)
2. [RFC 1574: API 文档约定](https://github.com/rust-lang/rfcs/blob/master/text/1574-more-api-documentation-conventions.md)
3. [Making Great Docs with Rustdoc](https://www.tangramvision.com/blog/making-great-docs-with-rustdoc)
4. [Rust Doc book](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)

