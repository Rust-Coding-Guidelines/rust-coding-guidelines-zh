## G.CNS.01 对于科学计算中涉及浮点数近似值的常量宜使用预定义常量

**【级别】** 建议

**【描述】**

Rust标准库中已经提供了一些特殊常量的定义，其精确度通常会比开发者自行定义的高，所以若考虑数值精确度时则宜使用标准库已定义的特殊常量。

这些特殊常量都可以在标准库中找到，例如[std::f32::consts](https://doc.rust-lang.org/std/f32/consts/index.html)

**【反例】**

```rust
let x = 3.14;
let y = 1_f64 / x;
```

**【正例】**

```rust
let x = std::f32::consts::PI;
let y = std::f64::consts::FRAC_1_PI;
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [approx_constant](https://rust-lang.github.io/rust-clippy/master/#approx_constant) | yes| no | Correctness | deny |

该 Lint 默认为 `deny`，但在某些场景下，可以设置为`allow`.
