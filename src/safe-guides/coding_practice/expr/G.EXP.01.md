## G.EXP.01 当需要对表达式求值后重新赋值时，宜使用复合赋值模式

**【级别】** 建议

**【描述】**

略

**【反例】**

```rust
let mut a = 5;
let b = 0;
a = a + b; // 不符合
```

**【正例】**

```rust
let mut a = 5;
let b = 0;
a += b; // 符合
```

【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assign_op_pattern](https://rust-lang.github.io/rust-clippy/master/#assign_op_pattern) | yes| no | style | warn |


