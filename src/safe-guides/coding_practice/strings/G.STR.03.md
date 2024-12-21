## G.STR.03   将只包含 `ASCII`字符的字符串字面量转为字节序列可以直接使用`b"str"` 语法代替调用`as_bytes`方法

**【级别】** 建议

**【描述】**

这是为了增强可读性，让代码更简洁。

注意，`"str".as_bytes()` 并不等价于  `b"str"`，而是等价于 `&b"str"[..]`  。

**【反例】**

```rust
#![warn(clippy::string_lit_as_bytes)]
// 不符合
let bs = "a byte string".as_bytes();
```

**【正例】**

```rust
#![warn(clippy::string_lit_as_bytes)]
// 符合
let bs = b"a byte string";
```

**【Lint 检测】**

| lint name                                                                                  | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [string_lit_as_bytes](https://rust-lang.github.io/rust-clippy/master/#string_lit_as_bytes) | yes           | no           | nursery    | allow     |


