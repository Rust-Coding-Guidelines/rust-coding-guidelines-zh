## P.MTH.LOK.01 多线程下要注意识别锁争用的情况，避免死锁

**【描述】**

Rust 并不能保证没有死锁，要注意 `LockResult<MutexGuard<'_, T>>` 的生命周期，以防止出现死锁的情况。

**【反例】**

下面代码有一定的几率会触发死锁。

```rust
// 触发死锁时，只会输出：
// Thread 1 holds a lock and starts waiting b lock
// Thread 2 hodls a lock and starts waiting a lock

use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let a = Arc::new(Mutex::new(0));
    let b = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    {
        let a = Arc::clone(&a);
        let b = Arc::clone(&b);
        let handle = thread::spawn(move || {
            let mut a_num = a.lock().unwrap();
            *a_num += 1;
            println!("Thread 1 holds a lock and starts waiting b lock");
            let mut b_num = b.lock().unwrap();
            *b_num += 1;
        });
        handles.push(handle);
    }
    {
        let a = Arc::clone(&a);
        let b = Arc::clone(&b);
        let handle = thread::spawn(move || {
            let mut b_num = b.lock().unwrap();
            *b_num += 1;
            println!("Thread 2 holds b lock and starts waiting a lock");
            let mut a_num = a.lock().unwrap();
            *a_num += 1;
            println!("Thread 2");
        });
        handles.push(handle);
    }
    for handle in handles {
        handle.join().unwrap();
    }

    println!("Done {}", *a.lock().unwrap()); // never reach here
}
```

**【正例】**

```rust
// 无死锁发生，正常输出
// Thread 1 holds a lock and starts waiting b lock
// Thread 2 hodls a lock and starts waiting a lock
// Thread 2
// Done 2

use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let a = Arc::new(Mutex::new(0));
    let b = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    {
        let a = Arc::clone(&a);
        let b = Arc::clone(&b);
        let handle = thread::spawn(move || {
            {   // <- 这里增加显示作用域，确保 lock 之后可以自动解锁
                // 即 LockResult<MutexGuard<'_, T>> 在作用域之外会自动释放
                let mut a_num = a.lock().unwrap();
                *a_num += 1;
                println!("Thread 1 holds a lock and starts waiting b lock");
            }
            {   // <- 这里增加显示作用域，确保 lock 之后可以自动解锁
                // 即 LockResult<MutexGuard<'_, T>> 在作用域之外会自动释放
                let mut b_num = b.lock().unwrap();
                *b_num += 1;
            }
        });
        handles.push(handle);
    }
    {
        let a = Arc::clone(&a);
        let b = Arc::clone(&b);
        let handle = thread::spawn(move || {
            {   // <- 这里增加显示作用域，确保 lock 之后可以自动解锁
                // 即 LockResult<MutexGuard<'_, T>> 在作用域之外会自动释放
                let mut b_num = b.lock().unwrap();
                *b_num += 1;
                println!("Thread 2 holds b lock and starts waiting a lock");
            }
            {   // <- 这里增加显示作用域，确保 lock 之后可以自动解锁
                // 即 LockResult<MutexGuard<'_, T>> 在作用域之外会自动释放
                let mut a_num = a.lock().unwrap();
                *a_num += 1;
            }
            println!("Thread 2");
        });
        handles.push(handle);
    }
    for handle in handles {
        handle.join().unwrap();
    }

    println!("Done {}", *a.lock().unwrap());
}
```
