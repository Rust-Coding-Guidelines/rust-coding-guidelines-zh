## G.UNS.SAS.09 在任何 Unsafe 块之前都应该加 `SAFETY` 注释

**【描述】**

在任何 unsafe 块（即 `unsafe {...}`）之前都应该加 `# SAFETY` 注释，用于表明 Unsafe 块里面的代码为什么是正确的。并且 `# SAFETY` 是全大写，注意区分与文档注释中 `# Safety` 的用法。

虽然有时原因可能看起来微不足道或很明显，但编写这些注释不仅是记录所考虑内容的好方法，而且最重要的是，它提供了一种知道没有额外隐含约束的方法。


**【反例】**

未在 unsafe 块之前增加 `# SAFETY` 注释。

```rust

// 注意这里 Safety 是用于文档注释，用于说明 unsafe 方法或函数的 安全边界
/// Returns the contained [`Some`] value, consuming the `self` value,
/// without checking that the value is not [`None`].
///
/// # Safety
///
/// Calling this method on [`None`] is *[undefined behavior]*.
///
/// [undefined behavior]: https://doc.rust-lang.org/reference/behavior-considered-undefined.html
///
/// # Examples
///
/// ```
/// let x = Some("air");
/// assert_eq!(unsafe { x.unwrap_unchecked() }, "air");
/// ```
pub unsafe fn unwrap_unchecked(self) -> T {
        match self {
                Some(val) => val,
                // 这里存在一个 Unsafe block ，应该为其增加 SAFETY 注释来说明其
                // 但是现在没有相关注释
                None => unsafe { hint::unreachable_unchecked() },
        }
}
```

**【正例】**

已在 unsafe 块之前增加 `# SAFETY` 注释。

```rust

// 注意这里 Safety 是用于文档注释，用于说明 unsafe 方法或函数的 安全边界
/// Returns the contained [`Some`] value, consuming the `self` value,
/// without checking that the value is not [`None`].
///
/// # Safety
///
/// Calling this method on [`None`] is *[undefined behavior]*.
///
/// [undefined behavior]: https://doc.rust-lang.org/reference/behavior-considered-undefined.html
///
/// # Examples
///
/// ```
/// let x = Some("air");
/// assert_eq!(unsafe { x.unwrap_unchecked() }, "air");
/// ```
pub unsafe fn unwrap_unchecked(self) -> T {
        match self {
                Some(val) => val,
                // 这里存在一个 Unsafe block ，为其增加 SAFETY 注释来说明其
                // SAFETY: The safety contract must be upheld by the caller.
                None => unsafe { hint::unreachable_unchecked() },
        }
}
```