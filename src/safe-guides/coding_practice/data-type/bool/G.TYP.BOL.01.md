## G.TYP.BOL.01 返回为布尔值的表达式或函数值不应和布尔字面量进行比较

**【级别】** 要求

**【描述】**

在 Rust 中，返回为布尔值的表达式或函数值可以直接当作布尔值使用。

总之，使用布尔表达式的时候，要尽可能地简洁明了。

**【反例】**

```rust
if x == true {}
if y == false {}

assert_eq!("a".is_empty(), false);
assert_ne!("a".is_empty(), true);
```

**【正例】**

```rust
if x {}
if !y {}

assert!(!"a".is_empty());
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bool_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_comparison ) | yes| no | complexity | warn |
| [bool_assert_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_assert_comparison ) | yes| no | style | warn |
| [needless_bool ](https://rust-lang.github.io/rust-clippy/master/#needless_bool ) | yes| no | complexity | warn |
| [nonminimal_bool  ](https://rust-lang.github.io/rust-clippy/master/#nonminimal_bool  ) | yes| no | complexity | warn |
| [needless_bitwise_bool  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | pedantic | allow |
| [assertions_on_constants  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | pedantic | warn |

