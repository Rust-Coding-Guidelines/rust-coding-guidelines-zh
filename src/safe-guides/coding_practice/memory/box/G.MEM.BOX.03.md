## G.MEM.BOX.03  一般情况下，不要直接对不需要堆内存分配就可以正常工作的类型进行 Box 装箱

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [boxed_local ](https://rust-lang.github.io/rust-clippy/master/#boxed_local ) | yes| no | perf | warn |

也有例外。

### 【描述】


【正例】

```rust
# fn foo(bar: usize) {}
let x = 1;
foo(x);
println!("{}", x);
```

【反例】

```rust
# fn foo(bar: usize) {}
let x = Box::new(1);
foo(*x);
println!("{}", *x);
```

【例外】

当栈变量太大的情况下，需要使用堆分配。或者栈变量需要逃逸的时候。

```rust

// https://docs.rs/crate/aitch/0.1.1/source/src/servers/hyper.rs#:~:text=clippy%3a%3aboxed_local

pub trait ServeFunc {
    fn call_box(self: Box<Self>) -> Result<()>;
}

impl<F> ServeFunc for F
where
    F: FnOnce() -> Result<()>,
{
    #[cfg_attr(feature = "cargo-clippy", allow(boxed_local))]
    fn call_box(self: Box<Self>) -> Result<()> {
        (*self)()
    }
}
```

