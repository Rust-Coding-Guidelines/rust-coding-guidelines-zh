# 3.19 Unsafe Rust

Unsafe Rust 是 Safe Rust 的超集，意味着在 Unsafe Rust 中也会有 Safe Rust的安全检查。但是 Unsafe Rust 中下面五件事是Safe Rust 的检查鞭长莫及的地方：

1. 解引用裸指针
2. 调用 `unsafe`函数（C函数，编译器内部函数或原始分配器）
3. 实现 `unsafe` trait
4. 可变静态变量
5. 访问  `union` 的字段

使用 Unsafe Rust 的时候，需要遵守一定的规范，这样可以避免未定义行为的发生。

关于 Unsafe Rust 下的一些专用术语可以查看 [Unsafe 代码术语指南](./unsafe_rust/glossary.md) 。

**Unsafe Rust 的语义：这是编译器无法保证安全的地方，需要程序员来保证安全。** 

## 列表

- [P.UNS.01 不要为了逃避 编译器安全检查而滥用 Unsafe Rust](./unsafe_rust/P.UNS.01.md)
- [P.UNS.02 不要为了提升性能而盲目使用 Unsafe Rust](./unsafe_rust/P.UNS.02.md)
- [G.UNS.01 不要随便为 带有 unsafe命名的 类型或方法创建别名](./unsafe_rust/G.UNS.01.md)
- [安全抽象](./unsafe_rust/safe_abstract.md)
    - [P.UNS.SAS.01 代码中要注意是否会因为 Panic 发生而导致内存安全问题](./unsafe_rust/safe_abstract/P.UNS.SAS.01.md)
    - [P.UNS.SAS.02 Unsafe 代码编写者有义务检查代码是否满足安全不变式](./unsafe_rust/safe_abstract/P.UNS.SAS.02.md)
    - [P.UNS.SAS.03 不要随便在公开的 API 中暴露未初始化内存](./unsafe_rust/safe_abstract/P.UNS.SAS.03.md)
    - [P.UNS.SAS.04 要考虑 Panic Safety 的情况](./unsafe_rust/safe_abstract/P.UNS.SAS.04.md)
    - [G.UNS.SAS.01 在公开的 unsafe 函数的文档中必须增加 Safety 注释](./unsafe_rust/safe_abstract/G.UNS.SAS.01.md)
    - [G.UNS.SAS.02 在 Unafe 函数中应该使用 `assert!` 而非 `debug_assert!` 去校验边界条件](./unsafe_rust/safe_abstract/G.UNS.SAS.02.md)
    - [G.UNS.SAS.03 Unsafe 代码中手动实现 `auto trait` 需要注意](./unsafe_rust/safe_abstract/G.UNS.SAS.03.md)
    - [G.UNS.SAS.04 不要随便在公开的 API 中暴露裸指针](./unsafe_rust/safe_abstract/G.UNS.SAS.04.md)
    - [G.UNS.SAS.05 在抽象安全方法的同时，也建议为性能考虑而增加相应的 Unsafe 方法](./unsafe_rust/safe_abstract/G.UNS.SAS.05.md)
    - [G.UNS.SAS.06 函数参数是不可变借用的时候，返回值不应该是可变借用](./unsafe_rust/safe_abstract/G.UNS.SAS.06.md)
- [裸指针操作](./unsafe_rust/raw_ptr.md)
    - [P.UNS.PTR.01 不要将裸指针在多线程间共享](./unsafe_rust/raw_ptr/P.UNS.PTR.01.md)
    - [G.UNS.PTR.01 当指针类型被强转为和当前内存对齐不一致的指针类型时，禁止对其解引用](./unsafe_rust/raw_ptr/G.UNS.PTR.01.md)
    - [G.UNS.PTR.02 禁止将不可变指针手工转换为可变指针](./unsafe_rust/raw_ptr/G.UNS.PTR.02.md)
    - [G.UNS.PTR.03 尽量使用 pointer::cast 来代替 使用 as 强转指针](./unsafe_rust/raw_ptr/G.UNS.PTR.03.md)
    - [G.UNS.PTR.04 建议使用 `NonNull<T>` 来替代 `*mut T`](./unsafe_rust/raw_ptr/G.UNS.PTR.04.md)
    - [G.UNS.PTR.05 使用指针类型构造泛型结构体时，需要使用 PhantomData<T> 来指定 T上的协变和所有权](./unsafe_rust/raw_ptr/G.UNS.PTR.05.md)
- [联合体](./unsafe_rust/union.md)
    - [G.UNS.UNI.01 除了与 C 交互，尽量不要使用 Union](./unsafe_rust/union/G.UNS.UNI.01.md)
    - [G.UNS.UNI.02 不要把联合体的不同变体用在不同生命周期内](./unsafe_rust/union/G.UNS.UNI.02.md)
- [内存](./unsafe_rust/mem.md)
    - [P.UNS.MEM.01 要注意选择合适的结构体、元组、枚举的数据布局](./unsafe_rust/mem/P.UNS.MEM.01.md)
    - [P.UNS.MEM.02 不能修改其它进程/动态库的内存变量](./unsafe_rust/mem/P.UNS.MEM.02.md)
    - [P.UNS.MEM.03 不能让 `String/Vec` 自动 `Drop` 其它进程/动态库的内存数据](./unsafe_rust/mem/P.UNS.MEM.03.md)
    - [P.UNS.MEM.04 尽量用可重入(reentrant)版本的 C-API 或系统调用](./unsafe_rust/mem/P.UNS.MEM.04.md)
    - [P.UNS.MEM.05 如果需要使用位域，推荐使用第三方库](./unsafe_rust/mem/P.UNS.MEM.05.md)
    - [G.UNS.MEM.01 使用 `MaybeUninit<T>` 来处理未初始化的内存](./unsafe_rust/mem/G.UNS.MEM.01.md)
- [FFi](./unsafe_rust/ffi.md)
    - [P.UNS.FFI.01 避免从公开的 Rust API 直接传字符串到 C 中](./unsafe_rust/ffi/P.UNS.FFI.01.md)
    - [P.UNS.FFI.02 在使用标准库 std::ffi 模块提供的类型时需要仔细查看其文档](./unsafe_rust/ffi/P.UNS.FFI.02.md)
    - [P.UNS.FFI.03 当使用来自 C 的指针时，如果该指针需要管理内存，则需要为包装该指针的 Rust 类型实现 Drop 特质](./unsafe_rust/ffi/P.UNS.FFI.03.md)
    - [P.UNS.FFI.04 如果一个函数正在跨越 FFi 边界，那么需要处理 Panic](./unsafe_rust/ffi/P.UNS.FFI.04.md)
    - [P.UNS.FFI.05 建议使用诸如标准库或 `libc crate` 所提供的可移植类型别名，而不是特定平台的类型](./unsafe_rust/ffi/P.UNS.FFI.05.md)
    - [P.UNS.FFI.06 Rust 和 C 之间传递字符或字符串时需要注意字符串要符合 C-ABI 以及 字符串的编码](./unsafe_rust/ffi/P.UNS.FFI.06.md)
    - [P.UNS.FFI.07 不要为任何传入到外部的类型实现 Drop](./unsafe_rust/ffi/P.UNS.FFI.07.md)
    - [P.UNS.FFI.08 FFi 中要进行合理的错误处理](./unsafe_rust/ffi/P.UNS.FFI.08.md)
    - [P.UNS.FFI.09 当 Rust 调用外部 C 函数时，如果可以确认安全，可以通过引用来代替裸指针](./unsafe_rust/ffi/P.UNS.FFI.09.md)
    - [P.UNS.FFI.10 当 Rust 函数导出外部函数时，必须从设计上保证被跨线程调用的安全性](./unsafe_rust/ffi/P.UNS.FFI.10.md)
    - [P.UNS.FFI.11 如需引用指定为 `#[repr(packed)]` 内存布局的结构体成员字段要注意合理规避未定义行为](./unsafe_rust/ffi/P.UNS.FFI.11.md)
    - [P.UNS.FFI.12 当依赖 C 端传入参数时，需要在文档注释中不变性声明，根据不同的调用场景选择合适的安全抽象方式](./unsafe_rust/ffi/P.UNS.FFI.12.md)
    - [G.UNS.FFI.01 自定义数据类型要保证一致的数据布局](./unsafe_rust/ffi/G.UNS.FFI.01.md)
    - [G.UNS.FFI.02 在 FFi 中使用的类型应该拥有稳定布局](./unsafe_rust/ffi/G.UNS.FFI.02.md)
    - [G.UNS.FFI.03 从外部传入的不健壮类型的外部值要进行检查](./unsafe_rust/ffi/G.UNS.FFI.03.md)
- [I/O](./unsafe_rust/io.md)
    - [G.UNS.FIO.01 在使用原始句柄的时候，要注意 I/O 安全性](./unsafe_rust/io/G.UNS.FIO.01.md)
- [Unsafe 代码术语指南](./unsafe_rust/glossary.md)