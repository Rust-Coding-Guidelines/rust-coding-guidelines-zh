## P.CMT.01 代码能做到自注释，文档要干练简洁

**【描述】**

一、代码能够做到自注释，避免冗余的普通代码注释。

注释固然很重要, 但最好的代码应当本身就是文档。有意义的类型名、函数名和变量名, 要远胜过要用注释解释的含糊不清的名字。当有意义的类型名、函数名和变量名还不能表达完整的语义时，再使用注释。

不要描述显而易见的现象, 永远不要用自然语言翻译代码作为注释。

二、文档注释要干练简洁：

1. 文档注释中内容用语应该尽量简短精干，不宜篇幅过长。请确保你的代码注释良好并且易于他人理解，好的注释能够传达上下文关系和代码目的。
2. 注释内容始终围绕两个关键点来构建：
    - What : 用于阐述代码为了什么而实现。
    - How : 用于阐述代码如何去使用。
3. 注释和文档注释使用的自然语言要保持一致。
4. Rust 项目文档应该始终基于 `rustdoc` 工具来构建，`rustdoc` 支持 Markdown 格式，为了使得文档更加美观易读，文档注释应该使用 Markdown 格式。

**【正例】** 

模块级文档，来自于 Rust 标准库`std::vec`：

```rust
// 符合

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
// 符合

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