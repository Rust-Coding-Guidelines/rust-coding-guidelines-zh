## G.EXP.04 自增或自减运算使用`+=`或`-=`

**【级别】** 建议

**【描述】**

C/Cpp 等编程语言常用的自增自减操作，如 `++i` 、`i++` 、`i--` 等不是合法的 Rust 表达式， `--i` 虽然是合法的 Rust 表达式，但是是表达对i的符号取反两次，而不是自减语义。

**【反例】**

```rust
let mut x = 3;
--x; // 不符合：x 的值还是 3
```

**【正例】**

```rust
let mut x = 3;
x -= 1; // 符合
```

**【Lint 检测】**

| lint name                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [double_neg](https://rust-lang.github.io/rust-clippy/master/#double_neg) | yes           | no           | style      | warn      |

