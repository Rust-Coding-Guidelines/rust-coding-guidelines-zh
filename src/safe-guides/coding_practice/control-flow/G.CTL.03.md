## G.CTL.03 当需要通过多个`if`判断来比较大小来区分不同情况时，优先使用`match`和`cmp`来代替`if`表达式

**【级别】** 建议

**【描述】**

在使用多个`if-else`来对不同情况进行区分时，使用 `match` 和 `cmp` 代替 `if` 的好处是语义更加明确，而且也能帮助开发者穷尽所有可能性。
但是这里需要注意这里使用 `match` 和 `cmp` 的性能要低于 `if`表达式，因为 一般的 `>` 或 `<` 等比较操作是内联的，而 `cmp`方法没有内联。

根据实际情况来选择是否设置 `comparison_chain` 为 `allow`。

**【反例】**

```rust
# fn a() {}
# fn b() {}
# fn c() {}
fn f(x: u8, y: u8) {
    if x > y {
        a()
    } else if x < y {
        b()
    } else {
        c()
    }
}
```

**【正例】**

```rust
use std::cmp::Ordering;
# fn a() {}
# fn b() {}
# fn c() {}
fn f(x: u8, y: u8) {
     match x.cmp(&y) {
         Ordering::Greater => a(),
         Ordering::Less => b(),
         Ordering::Equal => c()
     }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [comparison_chain](https://rust-lang.github.io/rust-clippy/master/#comparison_chain) | yes           | no           | style      | warn  |



