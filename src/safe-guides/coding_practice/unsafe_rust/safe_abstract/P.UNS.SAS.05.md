## P.UNS.SAS.05  手动实现 auto trait 时要充分考虑其安全性

**【描述】**

所谓 auto trait 是指 Safe Rust 中由编译器自动实现的 trait，比如 `Send/Sync` 。在 Unsafe Rust中就需要手动实现这俩 trait 了。

所以，在手动实现的时候要充分考虑其安全性。

**【正例】**

Rust futures 库中发现的问题，错误的手工 `Send/Sync` 实现 破坏了线程安全保证。

受影响的版本中，`MappedMutexGuard` 的 `Send/Sync` 实现只考虑了 `T` 上的差异，而 `MappedMutexGuard` 则取消了对 `U` 的引用。

当 `MutexGuard::map()` 中使用的闭包返回与 `T` 无关的 `U` 时，这可能导致安全 Rust 代码中的数据竞争。

这个问题通过修正 `Send/Sync` 的实现，以及在 `MappedMutexGuard` 类型中添加一个 `PhantomData<&'a mut U>` 标记来告诉编译器，这个防护也是在 U 之上。

```rust
// CVE-2020-35905: incorrect uses of Send/Sync on Rust's futures
pub struct MappedMutexGuard<'a, T: ?Sized, U: ?Sized> {
    mutex: &'a Mutex<T>,
    value: *mut U,
    _marker: PhantomData<&'a mut U>, // + 修复代码
}

impl<'a, T: ?Sized> MutexGuard<'a, T> {
    pub fn map<U: ?Sized, F>(this: Self, f: F)
        -> MappedMutexGuard<'a, T, U>
        where F: FnOnce(&mut T) -> &mut U {
            let mutex = this.mutex;
            let value = f(unsafe { &mut *this.mutex.value.get() });
                mem::forget(this);
                // MappedMutexGuard { mutex, value }
                MappedMutexGuard { mutex, value, _marker: PhantomData } //  + 修复代码
    }
}

// unsafe impl<T: ?Sized + Send, U: ?Sized> Send
unsafe impl<T: ?Sized + Send, U: ?Sized + Send> Send // + 修复代码
for MappedMutexGuard<'_, T, U> {}
//unsafe impl<T: ?Sized + Sync, U: ?Sized> Sync
unsafe impl<T: ?Sized + Sync, U: ?Sized + Sync> Sync // + 修复代码
for MappedMutexGuard<'_, T, U> {}

// PoC: this safe Rust code allows race on reference counter
* MutexGuard::map(guard, |_| Box::leak(Box::new(Rc::new(true))));
```
