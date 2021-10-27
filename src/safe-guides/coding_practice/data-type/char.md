# 字符

在 Rust 中，字符是一个合法的 Unicode 标量值（Unicode scalar value），一个字符大小为 4 字节，对应一个 Unicode 码位（CodePoint）。

---

## G.TYP.Char.01  不要将 字符字面量强转为 `u8` 去使用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [char_lit_as_u8](https://rust-lang.github.io/rust-clippy/master/#char_lit_as_u8) | yes           | no           | complexity | warn  |

### 【描述】

应该使用 字节字面量 去代替 将 字符字面量强转为 `u8`。

【正例】

```rust
b'x'
```

【反例】

```rust
'x' as u8
```

## G.TYP.Char.02    字符串方法中如果需要单个字符的值作为参数，最好使用字符而非字符串

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [single_char_pattern](https://rust-lang.github.io/rust-clippy/master/#single_char_pattern) | yes           | no           | perf       | warn  |

### 【描述】

使用 字符 比 用字符串性能更好一些。

【正例】

```rust
let s = "yxz";
s.split('x');
```

【反例】

```rust
let s = "yxz";
s.split("x");
```

## G.TYP.Char.03    当需要将整数转换为字符时，请使用安全转换函数，而非 `transmute`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [transmute_int_to_char](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_char) | yes           | no           | perf       | warn  |

### 【描述】

并非每个整数都对应一个合法的 Unicode 标量值，使用 `transmute` 转换会有未定义行为。

【正例】

```rust
let x = 37_u32;
unsafe {
    let x = std::char::from_u32(x).unwrap(); // 请按情况处理 None
    // let x = std::char::from_u32_unchecked(x);  // 如果确定该整数对应合法的unicode，可以使用 uncheck 方法加速
    assert_eq!('%', x);
}
```

【反例】

```rust
let x = 37_u32;
unsafe {
    let x: char = std::mem::transmute(x); // where x: u32
    assert_eq!('%', x);
}
```

