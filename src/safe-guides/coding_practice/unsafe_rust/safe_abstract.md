# 安全抽象规范

使用 Unsafe Rust 的一种方式是将 Unsafe 的方法或函数进行安全抽象，将其变成安全的方法或函数。

Unsafe Rust 中 API 的安全性设计通常有两种方式：

1.  将内部的 unsafe API 直接暴露给 API 的使用者，并且使用 `unsafe` 关键字来声明该 API 是非安全的，同时也需要对安全边界条件添加注释。
2. 对 API 进行安全封装，即，安全抽象。在内部使用断言来保证当越过安全边界时可以 Panic，从而避免 UB 产生。

第二种方式，对 Unsafe 代码进行安全抽象，是 Rust 生态的一种约定俗成。

## 列表

- [P.UNS.SAS.01 代码中要注意是否会因为 Panic 发生而导致内存安全问题](./safe_abstract/P.UNS.SAS.01.md)
- [P.UNS.SAS.02 Unsafe 代码编写者有义务检查代码是否满足安全不变式](./safe_abstract/P.UNS.SAS.02.md)
- [P.UNS.SAS.03 不要随便在公开的 API 中暴露未初始化内存](./uafe_abstract/P.UNS.SAS.03.md)
- [P.UNS.SAS.04 要考虑 Panic Safety 的情况](./safe_abstract/P.UNS.SAS.04.md)
- [G.UNS.SAS.01 在公开的 unsafe 函数的文档中必须增加 Safety 注释](./safe_abstract/G.UNS.SAS.01.md)
- [G.UNS.SAS.02 在 Unafe 函数中应该使用 `assert!` 而非 `debug_assert!` 去校验边界条件](./safe_abstract/G.UNS.SAS.02.md)
- [G.UNS.SAS.03 Unsafe 代码中手动实现 `auto trait` 需要注意](./safe_abstract/G.UNS.SAS.03.md)
- [G.UNS.SAS.04 不要随便在公开的 API 中暴露裸指针](./safe_abstract/G.UNS.SAS.04.md)
- [G.UNS.SAS.05 在抽象安全方法的同时，也建议为性能考虑而增加相应的 Unsafe 方法](./safe_abstract/G.UNS.SAS.05.md)
- [G.UNS.SAS.06 函数参数是不可变借用的时候，返回值不应该是可变借用](./safe_abstract/G.UNS.SAS.06.md)