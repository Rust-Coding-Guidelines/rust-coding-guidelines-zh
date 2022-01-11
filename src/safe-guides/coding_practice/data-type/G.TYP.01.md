## G.TYP.01 类型转换尽可能使用安全的转换函数代替 `as`

**【级别】** 建议

**【描述】**

Rust 的 `as` 转换包含了「静默的有损转换(lossy conversion)」。诸如 `i32::from` 之类的转换函数只会执行无损转换(lossless conversion)。 如果输入表达式的类型发生变化，使用转换函数可以防止转换变成无声的有损转换，并使阅读代码的人更容易知道转换是无损的。

**【反例】**

```rust
fn as_u64(x: u8) -> u64 {
    x as u64
}
// or
let a: u32;
f(a as u16);
// or
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr as *const i32;
let _ = mut_ptr as *mut i32;
```

**【正例】**

```rust
fn as_u64(x: u8) -> u64 {
    u64::from(x)
}
// or
f(a.try_into()?);
// or
f(a.try_into().expect("Unexpected u16 overflow in f"));
// or
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr.cast::<i32>();
let _ = mut_ptr.cast::<i32>();
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
