## G.TYP.Vec.01  禁止访问未初始化的数组

**【级别】** 建议

**【描述】**

访问未初始化数组的内存会导致 未定义行为。

**【反例】**

 ```rust
 let mut vec: Vec<u8> = Vec::with_capacity(1000);
 unsafe { vec.set_len(1000); }
 reader.read(&mut vec); // error: Undefined Behavior: using uninitialized data, but this operation requires initialized memory
 ```

**【正例】**

```rust
let mut vec: Vec<u8> = vec![0; 1000];
reader.read(&mut vec);

// or
let mut vec: Vec<MaybeUninit<T>> = Vec::with_capacity(1000);
vec.set_len(1000);  // `MaybeUninit` can be uninitialized

// or
let mut vec: Vec<u8> = Vec::with_capacity(1000);
let remaining = vec.spare_capacity_mut();  // `&mut [MaybeUninit<u8>]`
// perform initialization with `remaining`
vec.set_len(...);  // Safe to call `set_len()` on initialized part
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [uninit_vec](https://rust-lang.github.io/rust-clippy/master/#uninit_vec) | yes           | no           | correctness | deny  |
