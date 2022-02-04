# 错误处理

Rust 为了保证系统健壮性，将系统中出现的非正常情况划分为三大类：

1. 失败
2. 错误
3. 异常

Rust 语言针对这三类非正常情况分别提供了专门的处理方式，让开发者可以分情况去选择。

- 对于失败的情况，可以使用断言工具。
- 对于错误，Rust 提供了基于返回值的分层错误处理方式，比如 Option 可以用来处理可能存在空值的情况，而 Result 就专门用来处理可以被合理解决并需要传播的错误。
- 对于异常，Rust 将其看作无法被合理解决的问题，提供了线程恐慌机制，在发生异常的时候，线程可以安全地退出。

## 列表

- [P.ERR.01 当传入函数的参数值因为超出某种限制可能会导致函数调用失败，应该使用断言](./error-handle/P.ERR.01.md)
- [P.ERR.02 当函数的返回值或者结构体字段的值可能为空时，请使用`Option<T>`](./error-handle/P.ERR.02.md)
- [P.ERR.03 当程序中有不可恢复的错误时，应该让其 Panic](./error-handle/P.ERR.03.md)
- [P.ERR.04 当程序中需要处理错误时，应该使用 `Result<T, E>` 和 `?` 操作符](./error-handle/P.ERR.04.md)
- [P.ERR.05 在确定 `Option<T>` 和 `Result<T, E>`类型的值不可能是 `None` 或 `Err `时，请用 `expect` 代替 `unwrap()`](./error-handle/P.ERR.05.md)
- [P.ERR.06 根据应用还是库来选择不同的错误处理方式](./error-handle/P.ERR.06.md)
- [G.ERR.01 在处理 `Option<T>` 和 `Result<T, E>` 类型时，不要随便使用 `unwrap`](./error-handle/G.ERR.01.md)
- [G.ERR.02 不要滥用 `expect`，请考虑用 `unwrap_or_` 系列方法代替](./error-handle/G.ERR.02.md)