# 整数

Rust 中有目前有 十二种整数类型：`i8/u8`, `i16/u16`, `i32/u32`, `i64/u64`, `i128/u128`, `isize/usize` 。

---

## G.TYP.INT.01 对于大整数字面量使用十六进制表示比十进制更好

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [decimal_literal_representation](https://rust-lang.github.io/rust-clippy/master/#decimal_literal_representation) | yes           | no           | restriction | allow |

### 【描述】

【正例】

```rust
let a = `255` 
let b = `65_535`
let c =`4_042_322_160` 
```

【反例】

```rust
let a = `0xFF` 
let b = `0xFFFF` 
let c = `0xF0F0_F0F0
```

## G.TYP.INT.02  数字字面量在使用的时候应该明确标注好类型

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [default_numeric_fallback](https://rust-lang.github.io/rust-clippy/master/#default_numeric_fallback) | yes           | no           | restriction | allow |

### 【描述】

如果数字字面量没有被指定具体类型，那么单靠类型推导，整数类型会被默认绑定为 `i32` 类型，而浮点数则默认绑定为 `f64`类型。这可能导致某些运行时的意外。

【正例】

```rust
let i = 10u32;
let f = 1.23f32;
```

【反例】

```rust
let i = 10; // i32
let f = 1.23; // f64
```

