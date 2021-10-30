# 锁同步

Rust 中多线程并发使用锁来进行线程同步。

---

## P.MTH.lock.01  首选  [`parking_lot`](https://crates.io/crates/parking_lot) 中定义的 同步原语，而非标准库 `std::sync` 模块

**【描述】**

标准库中 `std::sync` 模块中实现的锁同步原语，存在一些问题，比如需要使用 `Box<T>` 将操作系统锁原语维持在同一个内存位置，这点浪费内存。而   [`parking_lot`](https://crates.io/crates/parking_lot)  的实现则更加轻量和正确，性能也更好，比如 `parking_lot` 的 `Mutex` 和  `Rwlock` 都支持 最终公平性，在不失性能的基础上保证公平。 目前官方正在推动 parking_lot 进入标准库中。在使用 parking_lot 时注意和标准库的区别。

parking_lot 也提供了一些有用的 feature，比如 死锁检测（deadlock detection），在使用  Mutex 的时候，可以打开这个特性，可以在编译期发现死锁，没准可以节省你很多时间。

## P.MTH.lock.02   根据场景选择使用互斥锁还是 Channel 

**【描述】**

不要从哪种方式更快的角度来考虑，而应该从使用场景。性能取决于你如何使用它们。

一个简单的指南：

| **Channel** 适用于                                     | **Mutex** 适用于                |
| ------------------------------------------------------ | ------------------------------- |
| 传递数据所有权 <br /> 分发工作单元 <br /> 传递异步结果 | 修改共享缓存<br /> 修改共享状态 |

## P.MTH.lock.03   如果要使用 Channel  建议使用 `crossbeam` 或 `flume`

**【描述】**

标准库中的 channel 实现并不好，也许会被移使用 `crossbeam` 或 `flume`  目前是约定俗成。



## P.MTH.lock.04    多线程下要注意识别锁争用的情况，避免死锁

**【描述】**

Rust 并不能保证没有死锁，要注意 ` LockResult<MutexGuard<'_, T>>` 的生命周期，以防止出现锁争用的情况。







---

## G.MTH.lock.01   多线程下修改布尔值或使用引用可以用原子类型代替互斥锁

### 【级别：建议】



---

## G.MTH.lock.01   对 布尔 或 引用 并发访问应该使用原子类型而非互斥锁

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [mutex_atomic](https://rust-lang.github.io/rust-clippy/master/#mutex_atomic) | yes           | no           | perf       | warn  |

### 【描述】

使用原子类型性能更好。但要注意指定合理的内存顺序。

【正例】

```rust
let x = AtomicBool::new(y);
```

【反例】

```rust
let x = Mutex::new(&y);
```

## G.MTH.lock.02   多线程环境下要使用 `Arc` 代替 `Rc`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_mutex](https://rust-lang.github.io/rust-clippy/master/#rc_mutex) | yes           | no           | restriction | allow |

### 【描述】

`Rc` 是专门用于单线程的，多线程下应该用  `Arc` 。

【正例】

```rust
use std::rc::Rc;
use std::sync::Arc;
use std::cell::RefCell
fn foo(interned: Rc<RefCell<i32>>) { ... }
// or
fn foo(interned: Arc<Mutex<i32>>) { ... }
```

【反例】

```rust
use std::rc::Rc;
use std::sync::Mutex;
fn foo(interned: Rc<Mutex<i32>>) { ... }
```

## G.MTH.lock.03   建议使用 `Arc<str>/ Arc<[T]>` 来代替  `Arc<String> / Arc<Vec<T>>`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [rc_buffer](https://rust-lang.github.io/rust-clippy/master/#rc_buffer) | yes           | no           | restriction | allow |

### 【描述】

  `Arc<str>/ Arc<[T]>` 的性能比  `Arc<String> / Arc<Vec<T>>` 更好。

因为 ：

-   `Arc<String> / Arc<Vec<T>>` 有一层中间层： `arc -> String len/Vec<T> len -> text/data` ，它是一个 薄指针（thin pointer） 。
-   `Arc<str>/ Arc<[T]>` 则没有中间层： `arc & string len / [T] len -> text/data`， 它是一个胖指针（fat pointer）。

【正例】

```rust
use std::rc::Rc;
use std::sync::Arc;

fn main() {
    let a: &str = "hello world";
    let b: Rc<str> = Rc::from(a);
    println!("{}", b);

    // or equivalently:
    let b: Rc<str> = a.into();
    println!("{}", b);

    // we can also do this for Arc,
    let a: &str = "hello world";
    let b: Arc<str> = Arc::from(a);
    println!("{}", b);
}
```

【反例】

```rust
use std::rc::Rc;
use std::sync::Arc;

fn main() {
    let a = "hello world".to_string();
    let b: Rc<String> = Rc::from(a);
    println!("{}", b);

    // or equivalently:
    let a = "hello world".to_string();
    let b: Rc<String> = a.into();
    println!("{}", b);

    // we can also do this for Arc,
    let a = "hello world".to_string();
    let b: Arc<String> = Arc::from(a);
    println!("{}", b);
}
```

