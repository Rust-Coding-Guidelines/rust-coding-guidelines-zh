## P.FMT.03  行间距最大宽度空一行

**【描述】**

代码行之间，最小间隔 `0` 行，最大间隔`1`行。

**【反例】**

```rust
fn foo() {
    println!("a");
}
// 不符合：空两行 
// 不符合：空两行
fn bar() {
    println!("b");
// 不符合：空两行 
// 不符合：空两行
    println!("c");
}
```

**【正例】**

```rust
fn foo() {
    println!("a");
}
// 符合：空一行 
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
	// 符合：空一行 
    println!("c");
}

```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`blank_lines_lower_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_lower_bound) | 0（默认） | No| 不空行|
|[`blank_lines_upper_bound`](https://rust-lang.github.io/rustfmt/?#blank_lines_upper_bound)| 1（默认）| No | 最大空一行|