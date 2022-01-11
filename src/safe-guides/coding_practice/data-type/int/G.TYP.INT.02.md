## G.TYP.INT.02 对于大整数字面量宜使用十六进制表示

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let a = `255` 
let b = `65_535`
let c =`4_042_322_160` 
```

**【正例】**

```rust
let a = `0xFF`
let b = `0xFFFF`
let c = `0xF0F0_F0F0
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [decimal_literal_representation](https://rust-lang.github.io/rust-clippy/master/#decimal_literal_representation) | yes           | no           | restriction | allow |




