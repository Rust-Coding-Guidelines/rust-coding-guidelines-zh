# 异步编程

`async / await` 是 Rust 语言用于编写像同步代码一样的异步函数的内置工具。`async` 将一个代码块转化为一个实现了名为 `Future` 的特质 (trait)
的状态机。虽然在同步方法中调用阻塞函数会阻塞整个线程，但阻塞的 `Future` 将让出线程控制权，允许其他 `Future` 运行。

Rust 异步编程需要依赖于异步运行时，生产环境中比较推荐的开源异步运行时是 [Tokio](https://github.com/tokio-rs/tokio)。

## 列表

- [P.ASY.01 异步编程并不适合所有场景，计算密集型场景应该考虑同步编程](./async-await/P.ASY.01.md)
- [G.ASY.01 在 async 块/函数中调用 async 函数/闭包请不要忘记添加.await](./async-await/G.ASY.01.md)
- [G.ASY.02 在跨 await 调用中，需要对其持有的同步互斥锁进行处理](./async-await/G.ASY.02.md)
- [G.ASY.03 在跨 await 调用中，需要对其持有 RefCell 的引用进行处理](./async-await/G.ASY.03.md)
- [G.ASY.04 避免定义不必要的异步函数](./async-await/G.ASY.04.md)
- [G.ASY.05 避免在异步处理过程中包含阻塞操作](./async-await/G.ASY.05.md)