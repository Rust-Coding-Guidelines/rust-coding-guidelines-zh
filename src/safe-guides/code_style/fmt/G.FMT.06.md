## G.FMT.06  单行规则

**【级别】** 建议

**【描述】**

当语言项内容为空时，即空函数，空结构体，空实现等，要保持单独一行。但是，当函数中只有一个表达式时，请不要保持单行。

**【反例】**

```rust
fn lorem() {
}

impl Lorem {
}

fn lorem() -> usize { 42 }

fn main() {
    let lorem = Lorem {
        foo: bar,
        baz: ofo,
    };
}
```

**【正例】**

```rust
fn lorem() {}

impl Lorem {}

fn lorem() -> usize {
    42
}

fn main() {
    let lorem = Lorem { foo: bar, baz: ofo };
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 默认值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`empty_item_single_line`](https://rust-lang.github.io/rustfmt/?#empty_item_single_line) | true（默认） | No| 当语言项内容为空时，要保持单行 |
| [`fn_single_line`](https://rust-lang.github.io/rustfmt/?#fn_single_line) | false（默认） | No| 当函数中只有一个表达式时，不要保持单行 |
| [`struct_lit_single_line`](https://rust-lang.github.io/rustfmt/?#struct_lit_single_line) | true（默认） | No| 当结构体字面量中只有少量表达式时，要保持单行 |