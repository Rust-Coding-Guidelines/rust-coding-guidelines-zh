# 动态数组

这里指可以动态增长的数组`Vec<T>`。

在数组一节中有[部分原则和规则](./array.md)也适用于这里。

---
<!-- toc -->
---

## P.TYP.Vec.01 非必要不要使用动态数组

【描述】

相关原则参见数组一节中有[对应原则](./array.md)，非必须不要使用 `Vec<T>`，应该优先尝试使用固定长度数组或常量泛型。

## P.TYP.Vec.02   创建动态数组时，可以预先分配大约足够的容量来避免后续操作中产生多次分配

**【描述】**

预分配足够的容量，避免后续内存分配，可以提升代码性能。

【正例】

```rust
let mut output = Vec::with_capacity(input.len());
```

【反例】

```rust
let mut output = Vec::new();
```



---



## G.TYP.Vec.01  禁止访问还未初始化的数组

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [uninit_vec](https://rust-lang.github.io/rust-clippy/master/#uninit_vec) | yes           | no           | correctness | deny  |

### 【描述】

访问未初始化数组的内存会导致 未定义行为。

【正例】

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

【反例】

 ```rust
 let mut vec: Vec<u8> = Vec::with_capacity(1000);
 unsafe { vec.set_len(1000); }
 reader.read(&mut vec); // error: Undefined Behavior: using uninitialized data, but this operation requires initialized memory
 ```

