# FFi 规范

Rust 可以通过C-ABI无缝与C语言打交道，也可以通过暴露 C-ABI 接口供其他语言调用。但是跨边界本质上是不安全的。

一般来说，FFi 是指在其他语言中调用 Rust 代码，Rust代码会按 C-ABI 来暴露接口。这类 Rust crate或模块，常以 `-ffi`后缀结尾。

另一类是 Rust 去调用 C-ABI 接口，相关代码通常被封装到以 `-sys` 为后缀命名的 crate 或 模块中。 

本小节内容，包含以上两种情况。  

## 列表

- [P.UNS.FFI.01 避免从公开的 Rust API 直接传字符串到 C 中](./ffi/P.UNS.FFI.01.md)
- [P.UNS.FFI.02 在使用标准库 std::ffi 模块提供的类型时需要仔细查看其文档](./ffi/P.UNS.FFI.02.md)
- [P.UNS.FFI.03 当使用来自 C 的指针时，如果该指针需要管理内存，则需要为包装该指针的 Rust 类型实现 Drop 特质](./ffi/P.UNS.FFI.03.md)
- [P.UNS.FFI.04 如果一个函数正在跨越 FFi 边界，那么需要处理 Panic](./ffi/P.UNS.FFI.04.md)
- [P.UNS.FFI.05 建议使用诸如标准库或 `libc crate` 所提供的可移植类型别名，而不是特定平台的类型](./ffi/P.UNS.FFI.05.md)
- [P.UNS.FFI.06 Rust 和 C 之间传递字符或字符串时需要注意字符串要符合 C-ABI 以及 字符串的编码](./ffi/P.UNS.FFI.06.md)
- [P.UNS.FFI.07 不要为任何传入到外部的类型实现 Drop](./ffi/P.UNS.FFI.07.md)
- [P.UNS.FFI.08 FFi 中要进行合理的错误处理](./ffi/P.UNS.FFI.08.md)
- [P.UNS.FFI.09 当 Rust 调用外部 C 函数时，如果可以确认安全，可以通过引用来代替裸指针](./ffi/P.UNS.FFI.09.md)
- [P.UNS.FFI.10 当 Rust 函数导出外部函数时，必须从设计上保证被跨线程调用的安全性](./ffi/P.UNS.FFI.10.md)
- [P.UNS.FFI.11 如需引用指定为 `#[repr(packed)]` 内存布局的结构体成员字段要注意合理规避未定义行为](./ffi/P.UNS.FFI.11.md)
- [P.UNS.FFI.12 当依赖 C 端传入参数时，需要在文档注释中不变性声明，根据不同的调用场景选择合适的安全抽象方式](./ffi/P.UNS.FFI.12.md)
- [G.UNS.FFI.01 自定义数据类型要保证一致的数据布局](./ffi/G.UNS.FFI.01.md)
- [G.UNS.FFI.02 在 FFi 中使用的类型应该拥有稳定布局](./ffi/G.UNS.FFI.02.md)
- [G.UNS.FFI.03 从外部传入的不健壮类型的外部值要进行检查](./ffi/G.UNS.FFI.03.md)
