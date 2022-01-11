## G.TYP.Array.01 创建大全局数组时宜使用静态变量而非常量

**【级别】** 建议

**【描述】**

因为常量会内联，对于大的数组，使用静态变量定义更好。

相关：[G.CNS.03 ](safe-guides/coding_practice/consts/G.CNS.03.md)

**【反例】**

```rust
pub const A: [u32;1_000_000] = [0u32; 1_000_000];
```

**【正例】**

```rust
pub static A: [u32;1_000_000] = [0u32; 1_000_000];
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_const_arrays](https://rust-lang.github.io/rust-clippy/master/#large_const_arrays) | yes           | no           | perf       | warn  |
| [large_stack_arrays](https://rust-lang.github.io/rust-clippy/master/#large_stack_arrays) | yes           | no           | pedantic   | allow |

注意： `large_stack_arrays` 会检查在栈上分配的大数组，但clippy默认是 allow，根据实际使用场景决定是否针对这种情况发出警告。



