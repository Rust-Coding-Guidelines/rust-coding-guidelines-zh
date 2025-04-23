## G.TYP.03  不要用数字类型边界值判断能否安全转换，而应使用 `try_from` 方法

**【级别】** 建议

**【描述】**

在 Rust 中 `From` 代表不能失败的转换，而 `TryFrom` 则允许返回错误。

一般在数字类型转换的时候，不需要防御式地去判断数字大小边界，那样可读性比较差，应该使用 `try_from` 方法，在无法转换的时候处理错误即可。

**【反例】**

```rust
#![warn(clippy::checked_conversions)]

// 不符合
let foo: u32 = 5;
let _ = foo <= i16::MAX as u32; // 等价于 let _ = foo <= (i32::MAX as u32);
```

**【正例】**

```rust
#![warn(clippy::checked_conversions)]

// 符合
let foo: u32 = 5; 
let f = i16::try_from(foo).is_ok(); // 返回 false
```

**【Lint 检测】**

| lint name                                                                                  | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [checked_conversions](https://rust-lang.github.io/rust-clippy/master/#checked_conversions) | yes           | no           | pedantic   | allow     |

