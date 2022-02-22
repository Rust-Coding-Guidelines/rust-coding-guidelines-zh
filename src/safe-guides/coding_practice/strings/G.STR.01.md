## G.STR.01   在实现`Display`特质时不应调用`to_string()`方法

**【级别】** 建议

**【描述】**

因为 `to_string` 是间接通过 `Display` 来实现的，如果实现 `Display` 的时候再使用 `to_tring` 的话，将会无限递归。

**【反例】**

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.to_string())
    }
}
```

**【正例】**

```rust
use std::fmt;

struct Structure(i32);
impl fmt::Display for Structure {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.0)
    }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [to_string_in_display](https://rust-lang.github.io/rust-clippy/master/#to_string_in_display) | yes           | no           | correctness | deny |



