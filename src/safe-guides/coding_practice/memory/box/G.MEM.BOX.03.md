## G.MEM.BOX.03  一般情况下，不应直接对栈分配类型进行 `Box` 装箱

**【级别】** 建议

**【描述】**

此举会对性能造成不必要的影响。只有当某个栈变量太大，需要使用堆分配的情况下，或是栈变量需要逃逸的时候，才需要考虑是否对其使用 `Box` 装箱。

**【反例】**

```rust
fn foo(bar: usize) {}
// 不符合
let x = Box::new(1);
foo(*x);
println!("{}", *x);
```

**【正例】**

```rust
fn foo(bar: usize) {}
// 符合
let x = 1;
foo(x);
println!("{}", x);
```

**【例外】**

用例来源：[aitch](https://github.com/mjkillough/aitch/blob/69fbd677a72d0ed1851624d16a53c2a676d49bd5/src/servers/hyper.rs#L28)

```rust
pub trait ServeFunc {
    fn call_box(self: Box<Self>) -> Result<()>;
}

impl<F> ServeFunc for F
where
    F: FnOnce() -> Result<()>,
{
    // 特殊情况，F 是泛型，且要匹配 trait定义
    #[cfg_attr(feature = "cargo-clippy", allow(boxed_local))]
    fn call_box(self: Box<Self>) -> Result<()> {
        (*self)()
    }
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [boxed_local ](https://rust-lang.github.io/rust-clippy/master/#boxed_local ) | yes| no | perf | warn |
