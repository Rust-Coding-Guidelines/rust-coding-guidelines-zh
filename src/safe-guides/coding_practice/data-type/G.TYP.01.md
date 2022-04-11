## G.TYP.01 类型转换尽可能使用安全的转换函数代替 `as`

**【级别】** 建议

**【描述】**

当在数字类型之间转换时，需要注意的是，如果要确保不会存在有损转换(lossy conversion)，就不要使用 `as`，而应该使用 `From::from`。因为 From 只实现了无损转换。例如，可以用From从i32转换到i64，但反过来转换却不允许。

对于指针类型，尽量使用 `cast`方法来代替 `as` 直接转换。

**【反例】**

```rust
#![warn(
    clippy::as_conversions,
    clippy::cast_lossless,
    clippy::cast_possible_truncation,
    clippy::cast_possible_wrap,
    clippy::cast_precision_loss,
    clippy::ptr_as_ptr
)]

fn as_u64(x: u8) -> u64 {
    // 不符合
    x as u64
}
// or

// 不符合
fn main() {
    // or
    let ptr: *const u32 = &42_u32;
    let mut_ptr: *mut u32 = &mut 42_u32;
    let _ = ptr as *const i32; // 不符合
    let _ = mut_ptr as *mut i32; // 不符合
}
```

**【正例】**

```rust
#![warn(
    clippy::as_conversions,
    clippy::cast_lossless,
    clippy::cast_possible_truncation,
    clippy::cast_possible_wrap,
    clippy::cast_precision_loss,
    clippy::ptr_as_ptr
)]

fn as_u64(x: u8) -> u64 {
    // 符合
    u64::from(x)
}

fn main() {
    let ptr: *const u32 = &42_u32;
    let mut_ptr: *mut u32 = &mut 42_u32;
    let _ = ptr.cast::<i32>(); // 符合
    let _ = mut_ptr.cast::<i32>(); // 符合
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [as_conversions](https://rust-lang.github.io/rust-clippy/master/#as_conversions) | yes| no | restriction | allow |
| [cast_lossless](https://rust-lang.github.io/rust-clippy/master/#cast_lossless) | yes| no | pedantic | allow |
| [cast_possible_truncation](https://rust-lang.github.io/rust-clippy/master/#cast_possible_truncation) | yes| no | pedantic | allow |
| [cast_possible_wrap](https://rust-lang.github.io/rust-clippy/master/#cast_possible_wrap) | yes| no | pedantic | allow |
| [cast_precision_loss](https://rust-lang.github.io/rust-clippy/master/#cast_precision_loss) | yes| no | pedantic | allow |
| [cast_sign_loss](https://rust-lang.github.io/rust-clippy/master/#cast_sign_loss) | yes| no | pedantic | allow |
| [fn_to_numeric_cast](https://rust-lang.github.io/rust-clippy/master/#fn_to_numeric_cast) | yes| no | Style | warn |
| [fn_to_numeric_cast_with_truncation](https://rust-lang.github.io/rust-clippy/master/#fn_to_numeric_cast_with_truncation) | yes| no | Style | warn |
| [char_lit_as_u8](https://rust-lang.github.io/rust-clippy/master/#char_lit_as_u8) | yes| no | Complexity | warn |
| [cast_ref_to_mut](https://rust-lang.github.io/rust-clippy/master/#cast_ref_to_mut) | yes| no | correctness | deny |
| [ptr_as_ptr](https://rust-lang.github.io/rust-clippy/master/#ptr_as_ptr) | yes| no | pedantic | allow |
