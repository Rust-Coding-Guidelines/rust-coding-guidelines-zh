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
