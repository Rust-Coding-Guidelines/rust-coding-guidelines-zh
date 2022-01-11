## P.FUD.02   函数返回值不要使用 `return`

**【描述】**

Rust 中函数块会自动返回最后一个表达式的值，不需要显式地指定 Return。

只有在函数过程中需要提前返回的时候再加 Return。

**【反例】**

```rust
fn foo(x: usize) -> usize {
    if x < 42{
        return x;
    }
    return x + 1;
}
```

**【正例】**

```rust
fn foo(x: usize) -> usize {
    if x < 42{
        return x;
    }
    x + 1
}
```
