## P.CMT.06  在文档中应该使用 Markdown 格式

**【描述】** 

Rust 文档注释支持 Markdown ，所以在编写文档注释的时候，应该使用 Markdown 格式。

**【正例】** 

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