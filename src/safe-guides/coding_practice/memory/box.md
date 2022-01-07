# Box 类型

Rust 中分配堆内存必须要使用的类型，类型签名为 `Box<T>`。

---
<!-- toc -->
---


## G.MEM.BOX.01  一般情况下，不要直接对 `Box<T>` 进行借用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [borrowed_box](https://rust-lang.github.io/rust-clippy/master/#borrowed_box) | yes| no | complexity | warn |

也有例外。

### 【描述】

通常 `&T` 比 `&Box<T>` 更常用。

【正例】

```rust
fn foo(bar: &T) { ... }
```

【反例】

```rust
fn foo(bar: &Box<T>) { ... }
```

【例外】

```rust
// https://docs.rs/crate/actix-web-security/0.1.0/source/src/authentication/scheme/authentication_provider.rs

#[async_trait]
pub trait AuthenticationProvider: AuthenticationProviderClone {
    #[allow(clippy::borrowed_box)]
    async fn authenticate(
        &self,
        authentication: &Box<dyn Authentication>,
    ) -> Result<Box<dyn UserDetails>, AuthenticationError>;
}
```


## G.MEM.BOX.02  一般情况下，不要直接对已经在堆上分配内存的类型进行 Box 装箱

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [box_vec](https://rust-lang.github.io/rust-clippy/master/#box_vec) | yes| no | perf | warn |


也有例外。

### 【描述】


【正例】

```rust
struct X {
    values: Vec<Foo>,
}
```

【反例】

```rust
struct X {
    values: Box<Vec<Foo>>,
}
```

【例外】

```rust
// https://docs.rs/crate/jex/0.2.0/source/src/jq/query.rs

#[derive(Debug)]
pub struct JQ {
    ptr: *mut jq_state,
    // We want to make sure the vec pointer doesn't move, so we can keep pushing to it.
    #[allow(clippy::box_vec)]
    errors: Box<Vec<JVRaw>>,
}

// https://docs.rs/crate/mmtk/0.6.0/source/src/plan/mutator_context.rs

// This struct is part of the Mutator struct.
// We are trying to make it fixed-sized so that VM bindings can easily define a Mutator type to have the exact same layout as our Mutator struct.
#[repr(C)]
pub struct MutatorConfig<VM: VMBinding> {
    // ...

    /// Mapping between allocator selector and spaces. Each pair represents a mapping.
    /// Put this behind a box, so it is a pointer-sized field.
    #[allow(clippy::box_vec)]
    pub space_mapping: Box<SpaceMapping<VM>>,
  
    // ...
}
```


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

