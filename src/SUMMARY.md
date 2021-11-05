# Rust 编码规范

# 介绍

- [Rust 编码规范介绍](./readme.md)
- [贡献说明](./contribution.md)

# 安全编码规范

- [前言](./safe-guides/intro.md)
- [开发环境](./safe-guides/dev_env.md)
- [代码风格](./safe-guides/code_style.md)
    - [命名](./safe-guides/code_style/naming.md)
    - [格式](./safe-guides/code_style/fmt.md)
    - [注释](./safe-guides/code_style/comments.md)
- [编程实践](./safe-guides/coding_practice.md)
    - [常量](./safe-guides/coding_practice/consts.md)
    - [静态变量](./safe-guides/coding_practice/statics.md)
    - [本地变量](./safe-guides/coding_practice/variables.md)
    - [数据类型](./safe-guides/coding_practice/data-type.md)
        - [单元类型](./safe-guides/coding_practice/data-type/unit.md)
        - [布尔](./safe-guides/coding_practice/data-type/bool.md)
        - [字符](./safe-guides/coding_practice/data-type/char.md)
        - [整数](./safe-guides/coding_practice/data-type/int.md)
        - [浮点数](./safe-guides/coding_practice/data-type/float.md)
        - [引用](./safe-guides/coding_practice/data-type/ref.md)
        - [切片](./safe-guides/coding_practice/data-type/slice.md)
        - [元组](./safe-guides/coding_practice/data-type/tuple.md)
        - [固定长度数组](./safe-guides/coding_practice/data-type/array.md)
        - [动态数组](./safe-guides/coding_practice/data-type/vec.md)
        - [结构体](./safe-guides/coding_practice/data-type/struct.md)
        - [枚举体](./safe-guides/coding_practice/data-type/enum.md)
    - [表达式](./safe-guides/coding_practice/expr.md)
    - [流程控制](./safe-guides/coding_practice/control-flow.md)
    - [字符串](./safe-guides/coding_practice/strings.md)
    - [集合容器](./safe-guides/coding_practice/collections.md)
    - [函数与闭包](./safe-guides/coding_practice/fn-and-closure.md)
    - [泛型](./safe-guides/coding_practice/generic.md)
    - [特质](./safe-guides/coding_practice/traits.md)
        - [标准库内置 trait](./safe-guides/coding_practice/traits/std-buildin.md)
        - [trait 对象](./safe-guides/coding_practice/traits/trait-object.md)
    - [错误处理](./safe-guides/coding_practice/error-handle.md)
    - [内存管理](./safe-guides/coding_practice/memory.md)
        - [生存期](./safe-guides/coding_practice/memory/lifetime.md)
        - [`Box<T>`](./safe-guides/coding_practice/memory/box.md)
        - [Drop 析构](./safe-guides/coding_practice/memory/drop.md)
    - [模块](./safe-guides/coding_practice/module.md)
    - [包管理](./safe-guides/coding_practice/cargo.md)
    - [宏](./safe-guides/coding_practice/macros.md)
        - [声明宏](./safe-guides/coding_practice/macros/decl.md)
        - [过程宏](./safe-guides/coding_practice/macros/proc.md)
    - [多线程](./safe-guides/coding_practice/threads.md)
        - [锁同步](./safe-guides/coding_practice/threads/lock.md)
        - [无锁](./safe-guides/coding_practice/threads/lock-free.md)
    - [异步编程](./safe-guides/coding_practice/async-await.md)
    - [Unsafe Rust](./safe-guides/coding_practice/unsafe_rust.md)
        - [安全抽象](./safe-guides/coding_practice/unsafe_rust/safe_abstract.md)
        - [裸指针操作](./safe-guides/coding_practice/unsafe_rust/raw_ptr.md)
        - [联合体](./safe-guides/coding_practice/unsafe_rust/union.md)
        - [内存](./safe-guides/coding_practice/unsafe_rust/mem.md)
        - [FFi 规范](./safe-guides/coding_practice/unsafe_rust/ffi.md)
        - [I/O](./safe-guides/coding_practice/unsafe_rust/io.md)
    - [no-std](./safe-guides/coding_practice/no-std.md)
    - [I/O](./safe-guides/coding_practice/io.md)
    - [信息安全](./safe-guides/coding_practice/security.md)
    - [其他](./safe-guides/coding_practice/others.md)
- [附录](./safe-guides/Appendix/toc.md)
    - [测试](./safe-guides/Appendix/test.md)
        - [单元测试](./safe-guides/Appendix/test/unit_test.md)
        - [基准测试](./safe-guides/Appendix/test/benchmark.md)
        - [模糊测试](./safe-guides/Appendix/test/fuzz.md)
    - [术语解释](./safe-guides/Appendix/terms.md)
    - [模板](./safe-guides/Appendix/templates/intro.md)
        - [rustfmt 模板](./safe-guides/Appendix/templates/rustfmt.toml.md)
        - [clippy 模板](./safe-guides/Appendix/templates/clippy.toml.md)
        - [deny 模板](./safe-guides/Appendix/templates/deny.toml.md)
    - [工具链](./safe-guides/Appendix/tools/intro.md)
        - [rustfmt](./safe-guides/Appendix/tools/rustfmt.md)
        - [noisy-clippy](./safe-guides/Appendix/tools/noisy-clippy.md)
        - [cargo-udeps](./safe-guides/Appendix/tools/cargo-udeps.md) 

# 领域最佳实践

- [介绍](./best-practices/intro.md)
- [高性能计算](./best-practices/hpc.md)
- [嵌入式 Rust](./best-practices/embedded.md)
- [数据库](./best-practices/databases.md)
- [游戏](./best-practices/games.md)
- [Cli App](./best-practices/cli_app.md)
- [GUI](./best-practices/gui.md)
- [Web 开发](./best-practices/web.md)
- [网络服务](./best-practices/networks.md)
- [WebAssembly](./best-practices/webassembly.md)



# Cheat Sheet 

- [介绍](./cheat-sheet/README.md)



# Rust 优化指南

- [介绍](./optimizing/intro.md)


# Rust Cookbook

- [介绍](./cookbook/intro.md)



