## G.TYP.BOL.02 如果 match 匹配表达式为布尔类型，宜使用 `if` 表达式来代替

**【级别】** 建议

**【描述】**

对于布尔表达式更倾向于使用 `if ... else ...`，相比较 `match` 模式匹配更有利于代码可读性。

**【反例】**

```rust
# fn foo() {}
# fn bar() {}
let condition: bool = true;
match condition {
    true => foo(),
    false => bar(),
}
```

**【正例】**

```rust
# fn foo() {}
# fn bar() {}
let condition: bool = true;
if condition {
    foo();
} else {
    bar();
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes | no | correctness | deny |
| [match_bool](https://rust-lang.github.io/rust-clippy/master/#match_bool) | yes | no | pedantic | allow |


