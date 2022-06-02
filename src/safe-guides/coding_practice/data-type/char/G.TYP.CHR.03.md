## G.TYP.CHR.03 需要将整数转换为字符时，应使用安全转换函数，而非 `transmute`

**【级别】** 要求

**【描述】**

并非每个整数都对应一个合法的 Unicode 标量值，使用 `transmute` 转换会有未定义行为。

**【反例】**

```rust
let x = 37_u32;
unsafe {
    // 不符合
    let x: char = std::mem::transmute(x); // where x: u32
    assert_eq!('%', x);
}
```

**【正例】**

```rust
let x = 37_u32;

// 符合：x 会返回一个 Result 类型，开发者可以进行错误处理
let x = std::char::from_u32(x); 
assert_eq!('%', x);

// 符合：如果确定该整数对应合法的 unicode，可以使用 uncheck 方法加速
let x = unsafe {std::char::from_u32_unchecked(x) };  
assert_eq!('%', x);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [transmute_int_to_char](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_char) | yes           | no           | complexity       | warn  |

