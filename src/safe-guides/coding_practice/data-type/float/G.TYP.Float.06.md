## G.TYP.Float.06 禁止在浮点数和整数相互转换时使用 `transmute`

**【级别】** 要求

**【描述】**

使用 `transmute` 转换是非常容易出错的，建议使用 `to_bites` 这样转换更加安全。

**【反例】**

```rust
unsafe {
    let _: u32 = std::mem::transmute(1f32);
    let _: f32 = std::mem::transmute(1_u32); // where x: u32
}
```

**【正例】**

```rust
let _: f32 = f32::from_bits(1_u32);
let _: u32 = 1f32.to_bits();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [transmute_float_to_int](https://rust-lang.github.io/rust-clippy/master/#transmute_float_to_int) | yes           | no           | complexity | warn  |
| [transmute_int_to_float](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_float) | yes           | no           | complexity | warn  |

