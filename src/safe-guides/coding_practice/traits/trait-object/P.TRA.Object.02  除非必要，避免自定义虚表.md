## P.TRA.Object.02  除非必要，避免自定义虚表

**【描述】**

trait 对象 `dyn Trait` 隐藏了复杂而又为危险的虚表实现，为我们提供了简单而又安全的动态分发。手动实现虚表的代码中充斥着大量的 `unsafe`，稍有不慎，就会引入 bug 。如无必要，不要自定义虚表。

如果你的设计不能使用标准的 `dyn Trait` 结构来表达，那么你首先应该尝试重构你的程序，并参考以下理由来决定是否使用自定义的虚表。

- 你想要为一类指针对象实现多态，并且无法忍受多级指针解引用造成的性能开销，参考 [RawWaker](https://doc.rust-lang.org/std/task/struct.RawWaker.html) 与 [Bytes](https://docs.rs/bytes/1.1.0/bytes/struct.Bytes.html)。
- 你想要自定义内存布局，比如像 C++ 中虚表一样紧凑的内存结构（虚表指针位于对象内），参考 [RawTask](https://github.com/tokio-rs/tokio/blob/master/tokio/src/runtime/task/raw.rs#L12)。
- 你的 crate 需要在 `no_std` 环境中使用动态分发，参考 [RawWaker](https://doc.rust-lang.org/std/task/struct.RawWaker.html)  。
- 或者，标准的 trait object 确实无法实现你的需求。

**【正例】**

来自标准库中 [RawWaker](https://doc.rust-lang.org/std/task/struct.RawWaker.html) 的定义。

```rust

pub struct RawWaker {
    /// A data pointer, which can be used to store arbitrary data as required
    /// by the executor. This could be e.g. a type-erased pointer to an `Arc`
    /// that is associated with the task.
    /// The value of this field gets passed to all functions that are part of
    /// the vtable as the first parameter.
    data: *const (),
    /// Virtual function pointer table that customizes the behavior of this waker.
    vtable: &'static RawWakerVTable,
}
#[derive(PartialEq, Copy, Clone, Debug)]
pub struct RawWakerVTable {
    /// This function will be called when the [`RawWaker`] gets cloned, e.g. when
    /// the [`Waker`] in which the [`RawWaker`] is stored gets cloned.
    ///
    /// The implementation of this function must retain all resources that are
    /// required for this additional instance of a [`RawWaker`] and associated
    /// task. Calling `wake` on the resulting [`RawWaker`] should result in a wakeup
    /// of the same task that would have been awoken by the original [`RawWaker`].
    clone: unsafe fn(*const ()) -> RawWaker,

    /// This function will be called when `wake` is called on the [`Waker`].
    /// It must wake up the task associated with this [`RawWaker`].
    ///
    /// The implementation of this function must make sure to release any
    /// resources that are associated with this instance of a [`RawWaker`] and
    /// associated task.
    wake: unsafe fn(*const ()),

    /// This function will be called when `wake_by_ref` is called on the [`Waker`].
    /// It must wake up the task associated with this [`RawWaker`].
    ///
    /// This function is similar to `wake`, but must not consume the provided data
    /// pointer.
    wake_by_ref: unsafe fn(*const ()),

    /// This function gets called when a [`RawWaker`] gets dropped.
    ///
    /// The implementation of this function must make sure to release any
    /// resources that are associated with this instance of a [`RawWaker`] and
    /// associated task.
    drop: unsafe fn(*const ()),
}
```

来自 [Bytes](https://docs.rs/bytes/1.1.0/bytes/struct.Bytes.html) 的示例。

```rust
pub struct Bytes {
    ptr: *const u8,
    len: usize,
    // inlined "trait object"
    data: AtomicPtr<()>,
    vtable: &'static Vtable,
}

pub(crate) struct Vtable {
    /// fn(data, ptr, len)
    pub clone: unsafe fn(&AtomicPtr<()>, *const u8, usize) -> Bytes,
    /// fn(data, ptr, len)
    pub drop: unsafe fn(&mut AtomicPtr<()>, *const u8, usize),
}
```
