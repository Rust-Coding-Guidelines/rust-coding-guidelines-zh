## G.ASY.02 在跨 `await` 调用中，需要对其持有的同步互斥锁进行处理

**【级别】** 建议

**【描述】**

同步互斥锁本来就不是为异步上下文跨 `await` 调用而设计的，在这种场景中使用同步互斥锁容易造成死锁。当同步互斥锁被跨 `await` 时，有可能很长时间都不会返回这个调用点，在其他任务中再次用到这个互斥锁的时候，容易造成死锁。

这里有两种解决方案：

1. 使用异步互斥锁。但是异步互斥锁的开销要大于同步互斥锁。
2. 确保同步互斥锁在调用 `await` 之前已经释放。

**【反例】**

```rust
#![warn(clippy::await_holding_lock)] 
use std::sync::Mutex;

async fn foo(x: &Mutex<u32>) {
  let mut guard = x.lock().unwrap();
  *guard += 1;
  baz().await; // 不符合
}

```

**【正例】**

```rust
#![warn(clippy::await_holding_lock)] 

use std::sync::Mutex;
// 使用同步互斥锁
async fn foo(x: &Mutex<u32>) {
    {
        let guard = x.lock().unwrap();
        *guard += 1;
    } // 符合：await 之前先释放锁
    bar.await;
}

// 使用异步互斥锁
use tokio::sync::Mutex;
use std::sync::Arc;

#[tokio::main]
async fn main() {
    // 使用 Arc 允许跨线程共享 Mutex
    let count = Arc::new(Mutex::new(0));

    for i in 0..5 {
        let my_count = Arc::clone(&count);
        tokio::spawn(async move {
            for j in 0..10 {
                // 符合：这里的 lock 在每次迭代后都会被释放
                let mut lock = my_count.lock().await;
                *lock += 1;
                println!("{} {} {}", i, j, lock);
            }
        });
    }

    loop {
        // 符合：这里的 lock 在每次迭代后都会被释放
        if *count.lock().await >= 50 {
            break;
        }
    }
    println!("Count hit 50.");
}
```

**【例外】**

用例来源：[kludgine](https://github.com/khonsulabs/kludgine/blob/dafc1b5bab10702265cdd1d8ab210ce882d0f998/app/src/runtime/smol.rs#L31)

```rust
// Launch a thread pool
std::thread::spawn(|| {
    let (signal, shutdown) = flume::unbounded::<()>();

    easy_parallel::Parallel::new()
        // Run four executor threads.
        .each(0..4, |_| {
            #[allow(clippy::await_holding_lock)] // 这里是 读写锁，不是互斥锁
            futures::executor::block_on(async {
                let guard = GLOBAL_THREAD_POOL.read(); // 获取读写锁的读锁，不会出现锁争用情况，所以是线程安全的
                let executor = guard.as_ref().unwrap();
                executor.run(shutdown.recv_async()).await
            })
        })
        // Run the main future on the current thread.
        .finish(|| {});

    drop(signal);
});
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [await_holding_lock](https://rust-lang.github.io/rust-clippy/master/#await_holding_lock) | yes           | no           | pedantic   | allow |
