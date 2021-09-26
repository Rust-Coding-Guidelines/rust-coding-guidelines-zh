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

