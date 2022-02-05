## G.UNS.SAS.02  在 Unafe 函数中应该使用 `assert!` 而非 `debug_assert!` 去校验边界条件

**【级别】** 要求

**【描述】**

`assert!` 宏在 Release 和 Debug 模式下都会被检查，并且不能被禁用。它通常用来在 unsafe 函数中判断传入的参数是否满足某种边界条件，以此来防止不合法的参数传入导致未定义行为。

但是 `debug_assert!` 则可以通过配置 `-C debug-assertions` 来禁用它， 而且 `debug_assert!` 在 Release 模式下也会被编译器优化。所以，一旦使用了 `debug_assert!` 在 unsafe 函数中用来防范不合法参数，那有可能会失效。

**【反例】**

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

**【正例】**

来自标准库 `slice` 的代码示例。

```rust
	pub fn split_at_mut(&mut self, mid: usize) -> (&mut [T], &mut [T]) {
        assert!(mid <= self.len()); // 判断边界条件，杜绝非法参数
        // SAFETY: `[ptr; mid]` and `[mid; len]` are inside `self`, which
        // fulfills the requirements of `from_raw_parts_mut`.
        unsafe { self.split_at_mut_unchecked(mid) }
    }
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认 level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ---------- |
| [debug_assert_with_mut_call](https://rust-lang.github.io/rust-clippy/master/index.html#debug_assert_with_mut_call) | yes           | no           | nursery    | allow      |

注意该 lint 当前是 Nursery Group，意味着可能会产生误报 Bug。
