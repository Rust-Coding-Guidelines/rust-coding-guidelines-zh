# Drop 析构

在 Safe Rust 中 ，Drop 比较安全。在 Unsafe Rust 中则需要注意更多关于 Drop 的问题。 

---
<!-- toc -->
---

## P.MEM.Drop.01 要注意防范内存泄漏

**【描述】**

Rust 语言并不保证避免内存泄漏，内存泄漏不属于 Rust 安全职责范围。使用 Rust 的时候需要注意下面情况可能会发生内存泄漏：

1. 循环引用
2. 使用 `forget`  / `leak` 等函数主动跳过析构
3. 使用 `std::mem::ManuallyDrop` 构建数据结构而忘记析构
4. 析构函数内部发生了 panic
5. 程序中止（abort on panic）


