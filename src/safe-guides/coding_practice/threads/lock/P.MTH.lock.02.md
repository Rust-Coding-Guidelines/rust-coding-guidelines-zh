## P.MTH.lock.04    多线程下要注意识别锁争用的情况，避免死锁

**【描述】**

Rust 并不能保证没有死锁，要注意 ` LockResult<MutexGuard<'_, T>>` 的生命周期，以防止出现锁争用的情况。
