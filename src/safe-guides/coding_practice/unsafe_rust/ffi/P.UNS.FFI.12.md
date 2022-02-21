## P.UNS.FFI.12 当依赖 C 端传入参数时，需要在文档注释中不变性声明，根据不同的调用场景选择合适的安全抽象方式

**【级别】** 建议

**【描述】**

当 Rust 代码中依赖 C 接口传入的参数时，比如指针或其他类型的参数，需要在文档注释中不变性声明。

如果在调用场景可以确定所传入的参数都是有效的，比如指针不会是空指针等情况时，我们可以通过文档注释中对该接口做不变性声明(type invariant)，来强调对依赖的 C 代码的信任，这样做的好处有两点：

1. 可以避免检查，而达到零成本安全抽象的目的。
2. 可以减少 unsafe 函数声明。

具体可以参考【正例】的代码注释。

**【正例】**

场景一：如果在调用场景上能确定所传入的参数都是有效时。

```rust

/// # Invariants （不变性说明）
///
/// 这个指针来自 C语言 端，这里默认 C 端来的是有效指针，信任 C 端
/// 这种信任对性能有益：零成本（没有检查开销）
pub struct CPtr(*mut bindings::cptr);

impl CPtr {
    /// # Safety
    ///
    /// 这里使用unsafe 函数，是因为确实无法保证传入的指针是否有效，
    /// 这是构造 CPtr结构体实例的入口方法，所以有必要声明为 unsafe
    pub unsafe fn new(cptr: *mut bindings::cptr) -> Self {
        Self(cptr)
    }

    // 安全性说明：这个指针已经有上面结构体定义时注释中的不变性声明来承诺安全了
    // 所以这是个方法没必要加 unsafe，因为已经有 new 方法标记为 unsafe 足以
    // 这种做法可以消除很多 unsafe 函数了
    // 同时，也不需要去检查 self.0 这个指针是不是空指针了
    pub fn get(&self) -> usize {
        // SAFETY: The pointer is valid by the type invariant.
        unsafe { bindings::cptr_get(self.0) as usize }
    }

    // 同上
    pub fn enable(self) -> Result<EnabledCPtr> {
        // SAFETY: The pointer is valid by the type invariant.
        to_result(|| unsafe { bindings::cptrenable(self.0) })?;
        Ok(EnabledCPtr(self))
    }

    impl Drop for CPtr {
        // 同上
        fn drop(&mut self) {
            // SAFETY: The pointer is valid by the type invariant.
            unsafe { bindings::clk_put(self.0) };
        }
    }
}
```

场景二：如果在调用场景上能无法确定所传入的参数都是有效时。

```rust

/// # Invariants （不变性说明）
///
/// 这个指针来自 C语言 端，这里默认 C 端来的是有效指针，信任 C 端
/// 这种信任对性能有益：零成本（没有检查开销）
pub struct CPtr(*mut bindings::cptr);

impl CPtr {
    // 这里对空指针进行判断
    // 虽然抽象为了安全方法，但是如果不能panic的情况就比较麻烦，比如用 Rust 写 Linux 内核驱动
    // 或者，还需要进行错误处理，增加针对空指针的错误类型，增加复杂性
    pub fn new(cptr: *mut bindings::cptr) -> Self {
        if cptr.is_null() {
            panic!("CPtr should not be null!")
        }
        Self(cptr)
    }

    // 因为 new 已经安全了，所以这个方法也安全
    pub fn get(&self) -> usize {
        // SAFETY: The pointer is valid by the type invariant.
        unsafe { bindings::cptr_get(self.0) as usize }
    }

    // 同上
    pub fn enable(self) -> Result<EnabledCPtr> {
        // SAFETY: The pointer is valid by the type invariant.
        to_result(|| unsafe { bindings::cptrenable(self.0) })?;
        Ok(EnabledCPtr(self))
    }

    impl Drop for CPtr {
        // 同上
        fn drop(&mut self) {
            // SAFETY: The pointer is valid by the type invariant.
            unsafe { bindings::clk_put(self.0) };
        }
    }
}
```

**【参考】**

- [https://github.com/Rust-for-Linux/linux/pull/324](https://github.com/Rust-for-Linux/linux/pull/324)
