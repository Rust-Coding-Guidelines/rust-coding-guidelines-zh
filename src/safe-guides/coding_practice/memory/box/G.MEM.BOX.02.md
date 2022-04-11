## G.MEM.BOX.02  一般情况下，不应直接对已经在堆上分配内存的类型进行 `Box` 装箱

**【级别】** 建议

**【描述】**

像 `Vec<T> / String` 这样的类型（数据集）已经在堆上分配了内存，就没有必要再使用 `Box` 对其进行装箱操作。当然也存在例外情况。

**【反例】**

```rust
struct X {
    // 不符合：Vec已在堆上分配了内存
    values: Box<Vec<Foo>>,
}
```

**【正例】**

```rust
struct X {
    // 符合
    values: Vec<Foo>,
}
```

**【例外】**

用例来源：[jex](https://github.com/rocurley/jex/blob/31e51ff77ea4f7f3c6eaa4aaca2e439985e1241d/src/jq/query.rs#L22)

```rust
#[derive(Debug)]
pub struct JQ {
    ptr: *mut jq_state,
    // We want to make sure the vec pointer doesn't move, so we can keep pushing to it.
    // 这里不想把 Vec 的指针 Move 掉，所以用 Box 装箱可以达到这个效果
    // 注：clippy::box_vec 以更名为 clippy::box_collection
    #[allow(clippy::box_vec)]
    errors: Box<Vec<JVRaw>>,
}
```

或以下情况。来源 [mmtk-core](https://github.com/mmtk/mmtk-core/blob/ad95d7797f020eaab1e608dc03b7db6a3d79839a/src/plan/mutator_context.rs#L19)

```rust
#[repr(C)]
pub struct MutatorConfig<VM: VMBinding> {
    // ...

    /// Mapping between allocator selector and spaces. Each pair represents a mapping.
    /// Put this behind a box, so it is a pointer-sized field.
    // 这里是为了让字段拥有指针一样的大小，所以装箱了
    #[allow(clippy::box_collection)]
    pub space_mapping: Box<SpaceMapping<VM>>,
  
    // ...
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [box_collection](https://rust-lang.github.io/rust-clippy/master/#box_collection) | yes| no | perf | warn |
