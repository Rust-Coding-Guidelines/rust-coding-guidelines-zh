# 异步编程

`async / await` 是 Rust 语言用于编写像同步代码一样的异步函数的内置工具。`async` 将一个代码块转化为一个实现了名为 `Future` 的特质 (trait)
的状态机。虽然在同步方法中调用阻塞函数会阻塞整个线程，但阻塞的 `Future` 将让出线程控制权，允许其他 `Future` 运行。

Rust 异步编程需要依赖于异步运行时，生产环境中比较推荐的开源异步运行时是 [Tokio](https://github.com/tokio-rs/tokio)。
