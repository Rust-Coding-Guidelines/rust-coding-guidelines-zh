## G.EXP.05 使用括号来清楚表示表达式的计算顺序

**【级别】** 建议

**【描述】**

并不是每个人都能记得住优先级，所以最好使用括号把优先级顺序区分出来，增加可读性。

**【反例】**

```rust
1 << 2 + 3 // 不符合
-1i32.abs() // 不符合
```

**【正例】**

```rust
(1 << 2) + 3  // 符合
(-1i32).abs() // 符合
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [precedence](https://rust-lang.github.io/rust-clippy/master/#precedence) | yes           | no           | complexity | warn  |

