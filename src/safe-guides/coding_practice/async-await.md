# 异步编程


---

## P.ASY.01  异步编程并不适合所有场景，计算密集型场景应该考虑同步编程

【描述】

异步编程适合 I/O 密集型应用，如果是计算密集型场景应该考虑使用同步编程。

## P.ASY.02  异步编程中要避免阻塞操作

【描述】

异步编程中如果出现阻塞，则会阻止同一线程上其他异步任务的执行，从而导致很大的延迟，或者死锁。




---

## G.ASY.01 在 `async` 块/函数中调用 `async` 函数/闭包请不要忘记添加`.await`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [async_yields_async](https://rust-lang.github.io/rust-clippy/master/#async_yields_async) | yes| no | correctness | deny |

也有例外情况。

### 【描述】


【正例】

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo().await
  };
}
```

【反例】

```rust
async fn foo() {}

fn bar() {
  let x = async {
    foo()
  };
}
```

【例外】

```rust
// https://docs.rs/crate/fishrock_lambda_runtime/0.3.0-patched.1/source/src/lib.rs#:~:text=clippy%3a%3aasync_yields_async

#[allow(clippy::async_yields_async)]
let task = tokio::spawn(async move { handler.call(body, ctx) });

let req = match task.await {
    // ...
}

```


## G.ASY.02 在 跨`await` 调用中持有同步互斥锁需要进行处理

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [await_holding_lock](https://rust-lang.github.io/rust-clippy/master/#await_holding_lock) | yes| no | pedantic | allow |

### 【描述】

同步互斥锁本来就不是为异步上下文跨 `await` 调用而设计的，在这种场景中使用同步互斥锁容易造成死锁。当同步互斥锁被跨 await 时，有可能很长时间都不会返回这个调用点，在其他任务中再次用到这个互斥锁的时候，容易造成死锁。

这里有三种解决方案：

1. 使用异步互斥锁。但是异步互斥锁的开销要大于同步互斥锁。
2. 确保同步互斥锁在调用 `await` 之前已经释放。

 【正例】

```rust
use std::sync::Mutex;
// 使用同步互斥锁
async fn foo(x: &Mutex<u32>) {
  {
    let guard = x.lock().unwrap();
    *guard += 1;
  }
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
                // 这里的 lock 在每次迭代后都会被释放
                let mut lock = my_count.lock().await;
                *lock += 1;
                println!("{} {} {}", i, j, lock);
            }
        });
    }

    loop {
        // 这里的 lock 在每次迭代后都会被释放
        if *count.lock().await >= 50 {
            break;
        }
    }
    println!("Count hit 50.");
}
```

【反例】

```rust
use std::sync::Mutex;

async fn foo(x: &Mutex<u32>) {
  let guard = x.lock().unwrap();
  *guard += 1;
  bar.await;
}
```

【例外】

```rust
    // FROM: https://github.com/khonsulabs/kludgine/blob/main/app/src/runtime/smol.rs#L31
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




## G.ASY.03 在 跨`await` 调用持有`RefCell`的引用需要进行处理

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [await_holding_refcell_ref](https://rust-lang.github.io/rust-clippy/master/#await_holding_refcell_ref) | yes| no | pedantic | allow |


### 【描述】

跟不要在异步上下文中跨 `await` 使用  同步互斥锁类似，使用 `RefCell` 的独占（可变）借用会导致 Panic。因为 `RefCell` 是运行时检查独占的可变访问，如果 跨 `await` 持有一个可变引用则可能会因为共享的可变引用而引起 Panic。

这种共享可变在编译期是无法被检查出来的。

【正例】

```rust
use std::cell::RefCell;

async fn foo(x: &RefCell<u32>) {
  {
     let mut y = x.borrow_mut();
     *y += 1;
  }
  bar.await;
}
```

【反例】

```rust
use std::cell::RefCell;

async fn foo(x: &RefCell<u32>) {
  let mut y = x.borrow_mut();
  *y += 1;
  bar.await;
}
```

【例外】

跨 `await` 持有 `RefCell` 的可变借用，但是当前场景确信永远不会 Panic，则可以使用。

```rust
// From ： https://github.com/MattiasBuelens/wasm-streams/blob/master/src/readable/into_underlying_byte_source.rs#L65
let fut = async move {
    // This mutable borrow can never panic, since the ReadableStream always queues
    // each operation on the underlying source.
    //  这个可变借用永远不会恐慌，因为 ReadableStream 对底层源的每个操作总是有序的。
    let mut inner = inner.try_borrow_mut().unwrap_throw();
    inner.pull(controller).await
};
```

## G.ASY.04 避免定义不必要的异步函数

### 【描述】

如果一个异步函数内部没有任何异步代码，相比一个同步函数，它会产生额外的调用成本。

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [unused_async](https://rust-lang.github.io/rust-clippy/master/#unused_async) | yes| no | pedantic | allow |


【正例】

```rust
fn add(value: i32) -> i32 {
    value + 1
}
```

【反例】

```rust
async fn add(value: i32) -> i32 {
    value + 1
}
```
