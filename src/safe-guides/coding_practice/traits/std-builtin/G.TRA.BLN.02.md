## G.TRA.BLN.02    不要为迭代器实现`Copy` 特质

**【级别】** 要求

**【描述】**

在 Rust 中，迭代器是不能实现 Copy 的。因为在需要迭代修改的场景，因为 Copy 的存在，而失去效果。

**【反例】**

比如，对于标准库里的 `Range<T>` 就不能实现 Copy，因为它也是一个迭代器。

```rust
let mut iter = 0..n;
for i in iter { if i > 2 { break; } }
iter.collect();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [copy_iterator](https://rust-lang.github.io/rust-clippy/master/#copy_iterator) | yes           | no           | **pedantic** | allow |