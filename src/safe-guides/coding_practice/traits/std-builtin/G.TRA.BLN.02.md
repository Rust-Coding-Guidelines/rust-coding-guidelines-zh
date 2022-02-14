## G.TRA.BLN.02    不要为迭代器实现`Copy` 特质

**【级别】** 要求

**【描述】**

不要为迭代器实现 `Copy` 特质，因为迭代器一般都是有状态的对象，如果实现 `Copy`，则可能会被意外复制，导致意外行为。

**【反例】**

比如，对于标准库里的 `Range<T>` 就不能实现 Copy，因为它也是一个迭代器。

不让实现 Copy 是为了避免下面的情况：

```rust
// 假如迭代器实现 Copy
let mut iter = 0..n;
// 在执行 for 循环的时候会有隐式复制
// 这里可能会针对迭代器副本做一些操作
for i in iter { if i > 2 { break; } }
// 但是这里是对原迭代器进行处理，可能产生不一致的行为
iter.collect();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [copy_iterator](https://rust-lang.github.io/rust-clippy/master/#copy_iterator) | yes           | no           | **pedantic** | allow |