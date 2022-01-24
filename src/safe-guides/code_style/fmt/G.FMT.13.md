## G.FMT.13  结尾逗号规则

**【级别】** 建议

**【描述】**

1. 当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号。
2. 在match分支中，如果包含了块，则不需要加逗号，否则需要加。

**【反例】**

```rust
// 当 `trailing_comma="Always"`
fn main() {
    let Lorem { ipsum, dolor, sit, } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing,
    } = elit;
}

// 当 `trailing_comma="Never"`
fn main() {
    let Lorem { ipsum, dolor, sit } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing
    } = elit;
}

// 当 `match_block_trailing_comma=true`
fn main() {
    match lorem {
        Lorem::Ipsum => {
            println!("ipsum");
        },
        Lorem::Dolor => println!("dolor"),
    }
}
```

**【正例】**

```rust
// 当 `trailing_comma="Vertical"`
fn main() {
    let Lorem { ipsum, dolor, sit } = amet;
    let Lorem {
        ipsum,
        dolor,
        sit,
        amet,
        consectetur,
        adipiscing,
    } = elit;
}

// 当 `match_block_trailing_comma=false`
fn main() {
    match lorem {
        Lorem::Ipsum => {
            println!("ipsum");
        }
        Lorem::Dolor => println!("dolor"),
    }
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`trailing_comma`](https://rust-lang.github.io/rustfmt/?#trailing_comma) | "Vertical"（默认） | No |  当多个字段在不同行时，在最后一个字段结尾添加逗号，如果在同一行，则不加逗号|
| [`match_block_trailing_comma`](https://rust-lang.github.io/rustfmt/?#match_block_trailing_comma) | false（默认） | No| 在match分支中，如果包含了块，则不需要加逗号，否则需要加 |
