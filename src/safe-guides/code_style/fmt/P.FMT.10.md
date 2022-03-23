## P.FMT.10  `match` 分支格式

**【描述】**

1. 当match分支右侧代码体太长无法和`=>`置于同一行需要使用块(block)来包裹。
2. 在match分支左侧匹配表达式前不要增加管道符(`|`)

**【反例】**

```rust
// 当 `match_arm_blocks=false`
fn main() {
    match lorem {
        ipsum => 
            foooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo(x),
        dolor => println!("{}", sit),
        sit => foo(
            "foooooooooooooooooooooooo",
            "baaaaaaaaaaaaaaaaaaaaaaaarr",
            "baaaaaaaaaaaaaaaaaaaazzzzzzzzzzzzz",
            "qqqqqqqqquuuuuuuuuuuuuuuuuuuuuuuuuuxxx",
        ),
    }
}

// 当 `match_arm_leading_pipes="Alaways"`
fn foo() {
    match foo {
        | "foo" | "bar" => {}
        | "baz"
        | "something relatively long"
        | "something really really really realllllllllllllly long" => println!("x"),
        | "qux" => println!("y"),
        | _ => {}
    }
}
```

**【正例】**

```rust
// 当 `match_arm_blocks=true`
fn main() {
    match lorem {
        ipsum => { 
            foooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo(x)
        }
        dolor => println!("{}", sit),
        sit => foo(
            "foooooooooooooooooooooooo",
            "baaaaaaaaaaaaaaaaaaaaaaaarr",
            "baaaaaaaaaaaaaaaaaaaazzzzzzzzzzzzz",
            "qqqqqqqqquuuuuuuuuuuuuuuuuuuuuuuuuuxxx",
        ),
    }
}

// 当 `match_arm_leading_pipes="Never"`
fn foo() {
    match foo {
        "foo" | "bar" => {}
        "baz"
        | "something relatively long"
        | "something really really really realllllllllllllly long" => println!("x"),
        "qux" => println!("y"),
        _ => {}
    }
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`match_arm_blocks`](https://rust-lang.github.io/rustfmt/?#match_arm_blocks) | true（默认） | No | 当match分支右侧代码体太长无法和`=>`置于同一行需要使用块(block)来包裹|
| [`match_arm_leading_pipes`](https://rust-lang.github.io/rustfmt/?#match_arm_leading_pipes) | Never（默认） | No| 在match分支左侧匹配表达式前不要增加管道符(`|`) |
