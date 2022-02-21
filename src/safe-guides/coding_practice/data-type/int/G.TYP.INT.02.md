## G.TYP.INT.02  避免在有符号整数和无符号整数之间进行强制转换

**【级别】** 建议

**【描述】**

当有符号整数被强制转换为无符号整数时，负值会发生回绕，变成更大的正值，这在实际应用时有可能助长缓冲区溢出风险。

**【反例】**

```rust
let y: i8 = -1;
y as u128; // will return 18446744073709551615
```

**【正例】**

```rust
let y : i8 = -1;
// Error: 
// the trait `From<i8>` is not implemented for `u128`
// the trait bound `u128: From<i8>` is not satisfied
let z = u128::from(y);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cast_sign_loss](https://rust-lang.github.io/rust-clippy/master/#cast_sign_loss) | yes           | no           | pedantic   | allow |

注意：默认情况下该 lint 是` allow`，如果需要检查这种转换，则需要设置为 `warn`或 `deny`。



