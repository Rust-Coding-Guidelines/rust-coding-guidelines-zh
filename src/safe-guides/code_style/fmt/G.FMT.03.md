## G.FMT.03  行间距最大宽度空一行

**【级别】** 建议

**【描述】**

代码行之间，最小间隔 `0` 行，最大间隔`1`行。

**【反例】**

```rust
fn foo() {
    println!("a");
}
// 1
// 2
fn bar() {
    println!("b");
// 1
// 2
    println!("c");
}
```

**【正例】**

```rust
fn foo() {
    println!("a");
}
// 1
fn bar() {
    println!("b");
    println!("c");
}
```

或者

```rust
fn foo() {
    println!("a");
}
fn bar() {
    println!("b");
	// 1
    println!("c");
}

```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`blank_lines_lower_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_lower_bound) | 0（默认） | No| 不空行|
|[`blank_lines_upper_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_upper_bound)| 1（默认）| No | 最大空一行|