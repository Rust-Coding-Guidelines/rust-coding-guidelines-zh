## G.EXP.04    不应使用无效表达式语句

**【级别】** 建议

**【描述】**

无效的表达式语句，虽然会执行，但实际并没有起到什么效果。

也有例外情况存在。

**【反例】**

```rust
a+1;
```

**【正例】**

```rust
let a = 41;
let a = a+1;
```

**【例外】**

像在下面代码中，为了确保常量函数 `new` 可以在输入参数超出 MAX 限制的情况下 panic，使用了一个数组的技巧：` ["tag number out of range"][(byte > Self::MAX) as usize];` 。因为目前 在常量上下文中还无法直接使用 `panic!`，等 `const_panic` 功能稳定就可以了。

如果不加 `#[allow(clippy::no_effect)]` ，Clippy 会有警告。

```rust
// From: https://docs.rs/crate/der/0.4.1/source/src/tag/number.rs

#[derive(Copy, Clone, Debug, Eq, PartialEq, PartialOrd, Ord)]
pub struct TagNumber(pub(super) u8);

impl TagNumber {
    /// Maximum tag number supported (inclusive).
    pub const MAX: u8 = 30;

    /// Create a new tag number (const-friendly).
    ///
    /// Panics if the tag number is greater than [`TagNumber::MAX`]. For a fallible
    /// conversion, use [`TryFrom`] instead.
    #[allow(clippy::no_effect)]
    pub const fn new(byte: u8) -> Self {
        // TODO(tarcieri): hax! use const panic when available
        ["tag number out of range"][(byte > Self::MAX) as usize];
        Self(byte)
    }
	// ...   
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [no_effect](https://rust-lang.github.io/rust-clippy/master/#no_effect) | yes           | no           | **complexity** | warn  |

