# 安全抽象规范



使用 Unsafe Rust 的一种方式是将 Unsafe 的方法或函数进行安全抽象，将其变成安全的方法或函数。

所谓安全抽象，就是充分考虑边界条件，消除某些不安全条件引发 UB 的可能性。



---

## P.UNS.SafeAbstract.01  

【描述】





---



## G.UNS.ABS.01  在 公开的 unsafe 函数的文档中必须增加 `# Safety` 注释

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [missing_safety_doc](https://rust-lang.github.io/rust-clippy/master/index.html#missing_safety_doc) | yes           | no           | Style      | warn       |

### 【描述】

在公开（pub）的 unsafe 函数文档中，必须增加 `# Safety` 注释来解释该函数的安全边界，这样使用该函数的用户才可以安全地使用它。

说明： 该规则通过 cargo clippy 来检测。默认会发出警告。

### 【示例】

【正例】

示例来自于标准库文档： [https://doc.rust-lang.org/stable/src/alloc/vec/mod.rs.html#1167](https://doc.rust-lang.org/stable/src/alloc/vec/mod.rs.html#1167)

```rust
    /// Creates a `Vec<T>` directly from the raw components of another vector.
    ///
    /// # Safety
    ///
    /// This is highly unsafe, due to the number of invariants that aren't
    /// checked:
    ///
    /// * `ptr` needs to have been previously allocated via [`String`]/`Vec<T>`
    ///   (at least, it's highly likely to be incorrect if it wasn't).
    /// * `T` needs to have the same size and alignment as what `ptr` was allocated with.
    ///   (`T` having a less strict alignment is not sufficient, the alignment really
    ///   needs to be equal to satisfy the [`dealloc`] requirement that memory must be
    ///   allocated and deallocated with the same layout.)
    /// * `length` needs to be less than or equal to `capacity`.
    /// * `capacity` needs to be the capacity that the pointer was allocated with.
    pub unsafe fn from_raw_parts(ptr: *mut T, length: usize, capacity: usize) -> Self {
        unsafe { Self::from_raw_parts_in(ptr, length, capacity, Global) }
    }
```


【反例】

```rust
    /// Creates a `Vec<T>` directly from the raw components of another vector.
    pub unsafe fn from_raw_parts(ptr: *mut T, length: usize, capacity: usize) -> Self {
        unsafe { Self::from_raw_parts_in(ptr, length, capacity, Global) }
    }
```

## G.UNS.ABS.02   在 Unafe 函数中应该使用 `assert!` 而非 `debug_assert!` 去校验边界条件

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [debug_assert_with_mut_call](https://rust-lang.github.io/rust-clippy/master/index.html#debug_assert_with_mut_call) | yes           | no           | nursery    | allow      |

注意该 lint 当前是 Nursery Group，意味着可能会产生误报 Bug。

### 【描述】

`assert!` 宏 在 Release 和 Debug 模式下都会被检查，并且不能被禁用。它通常用来在  unsafe 函数中判断传入的参数是否满足某种边界条件，以此来防止不合法的参数传入导致未定义行为。

但是 `debug_assert!` 则可以通过配置 `-C debug-assertions` 来禁用它， 而且 `debug_assert!`    在 Release 模式下也会被编译器优化。所以，一旦使用了 `debug_assert!` 在 unsafe 函数中用来防范不合法参数，那有可能会失效。

【正例】

来自标准库 `slice` 的代码示例。

```rust
	pub fn split_at_mut(&mut self, mid: usize) -> (&mut [T], &mut [T]) {
        assert!(mid <= self.len()); // 判断边界条件，杜绝非法参数
        // SAFETY: `[ptr; mid]` and `[mid; len]` are inside `self`, which
        // fulfills the requirements of `from_raw_parts_mut`.
        unsafe { self.split_at_mut_unchecked(mid) }
    }
```

【反例】

```rust
	// 使用了 debug_assert! 那就说明这个校验在 Release 模式不一定有效
    // 那么该函数就要被标记为  unsafe
	pub unsafe fn split_at_mut(&mut self, mid: usize) -> (&mut [T], &mut [T]) {
        debug_assert!(mid <= self.len()); // 注意，这里是 debug_assert!
        // SAFETY: `[ptr; mid]` and `[mid; len]` are inside `self`, which
        // fulfills the requirements of `from_raw_parts_mut`.
        unsafe { self.split_at_mut_unchecked(mid) }
    }

   // or
   // 在 debug_assert_eq! 中包含可变引用的调用，
   // 也会因为 debug_assert_ 系列的断言宏在 Release 下产生不可预料的结果，它是 unsafe 的
   debug_assert_eq!(vec![3].pop(), Some(3));
```

