## G.UNS.PTR.03  尽量使用 `pointer::cast` 来代替 使用 `as` 强转指针

**【级别】** 要求

**【描述】**

使用 `pointer::cast` 方法转换更加安全，它不会意外地改变指针的可变性，也不会将指针转换为其他类型。

**【反例】**

```rust
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr as *const i32; // 不符合
let _ = mut_ptr as *mut i32; // 不符合
```

**【正例】**

```rust
let ptr: *const u32 = &42_u32;
let mut_ptr: *mut u32 = &mut 42_u32;
let _ = ptr.cast::<i32>(); // 符合
let _ = mut_ptr.cast::<i32>(); // 符合
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [ptr_as_ptr](https://rust-lang.github.io/rust-clippy/master/#ptr_as_ptr) | yes           | no           | **correctness** | deny  |
