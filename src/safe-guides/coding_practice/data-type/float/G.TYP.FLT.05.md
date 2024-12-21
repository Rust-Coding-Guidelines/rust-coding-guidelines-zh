## G.TYP.FLT.05 禁止在浮点数和整数相互转换时使用 `transmute`

**【级别】** 要求

**【描述】**

使用 `transmute` 转换容易产生未定义行为，建议使用 `to_bites` 这样转换更加安全。

**【反例】**

```rust
// 不符合
unsafe {
    let _: u32 = std::mem::transmute(1f32);
    let _: f32 = std::mem::transmute(1_u32);
}
```

**【正例】**

```rust
//符合
let _: u32 = 1f32.to_bits();
let _: f32 = f32::from_bits(1_u32);
```

**【Lint 检测】**

| lint name                                                                                        | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [transmute_float_to_int](https://rust-lang.github.io/rust-clippy/master/#transmute_float_to_int) | yes           | no           | complexity | warn      |
| [transmute_int_to_float](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_float) | yes           | no           | complexity | warn      |

