## G.EXP.06 避免在比较中添加无用的掩码操作

**【级别】** 要求

**【描述】**

检查比较中的无用位掩码操作，可以在不改变结果的情况下删除该位掩码操作。

请对照下面表格进行检查。

|Comparison	| Bit Op |Example	| equals |
| ------ | ---- | --------- | ------ |
|\> / <= |	&#124; / ^ | x &#124; 2 > 3 | x > 3 |
|< / >= |	&#124; / ^ | x ^ 1 < 4 | x < 4 |

**【反例】**

```rust
// 不符合
if (x | 1 > 3) {  }
```

**【正例】**

```rust
// 符合
if (x > 3) {  }
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [ineffective_bit_mask](https://rust-lang.github.io/rust-clippy/master/#ineffective_bit_mask) | yes           | no           | correctness | **deny**  |

