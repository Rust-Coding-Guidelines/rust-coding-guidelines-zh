## P.MTH.LKF.02 使用无锁编程时，需要合理选择内存顺序

**【描述】**

Rust 原子类型使用 [`C++20` 的内存顺序模型](https://zh.cppreference.com/w/cpp/atomic/memory_order) 来指定原子操作的内存同步方式，但也不是完全采用此模型。

目前 Rust 引入五种内存顺序：`Relaxed / Release / Acquire / AcqRel / SeqCst`。

在无锁编程中，指定正确的内存顺序是很重要很复杂的一件事，这里有一些建议：
1. 如果对程序中的原子类型同步方式的判断没有太多信息，建议使用 `SeqCst`，它表示顺序一致性，会强制所有线程都同意程序指令以单一全局线性的方式来执行。这样可以保证安全性，但性能有一定损失。
2. 如果对无锁实现中线程间发生的数据竞争带来的后果不是特别关心，则可以放心使用 `Relaxed`，因为它性能最好。
3. 当多个线程之间操作内存中同一个位置有因果关系时，适合使用 `Acquire / Release / AcqRel` 来配对。比如，线程 A 写 (`Release`) 内存中的一个位置，然后线程 B 随后读 (`Acquire`) 内存中一个相同的位置，就会产生一个因果关系，所以为了保证 A 的每次写入都能在 B 读取之前被观察到。如果 A 和 B 访问不同内存位置，则没有因果关系。

**【正例】**

自旋锁中 `Acquire / Release` 搭配使用的简易示例：

```rust
use std::sync::Arc;
use std::sync::atomic::{AtomicBool, Ordering};
use std::thread;

fn main() {
    let lock = Arc::new(AtomicBool::new(false)); // 这个原子类型的值用来表示 "是否获取到锁"

    // ... distribute lock to threads somehow ...

    // 线程 A 尝试通过设置为 ture 来获取锁
    //
    // Acquire 内存顺序:
    // 当与 load 结合使用时，若 load 的值是由具有 Release (或更强) 排序的 store 操作写入的，则所有后续操作
    // 都将在该 store 之后进行排序。特别是，所有后续load都将看到在 store 之前写入的数据。
    while lock.compare_and_swap(false, true, Ordering::Acquire) { }
    // 在循环外，意味着已经拿到了锁！

    // ...访问/操作数据...

    // 线程A完成了数据操作，释放锁。
    // 此处用 Release 内存顺序可以确保线程B在获取锁时能看到线程A释放了锁（对内存写入 false）
    //
    // Release 内存顺序：
    // 当与 store 结合使用时，所有先前的操作都会在使用 Acquire（或更强）排序的任何 load 此值之前排序。
    // 特别是，所有先前的写入对执行 Acquire 此值（或更强）load 的线程都可见。
    lock.store(false, Ordering::Release);
}
```
