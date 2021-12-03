# Unsafe Rust

Unsafe Rust 是 Safe Rust 的超集，意味着在 Unsafe Rust 中也会有 Safe Rust的安全检查。但是 Unsafe Rust 中下面五件事是Safe Rust 的检查鞭长莫及的地方：

1. 解引用裸指针
2. 调用 `unsafe`函数（C函数，编译器内部函数或原始分配器）
3. 实现 `unsafe` trait
4. 可变静态变量
5. 访问  `union` 的字段

使用 Unsafe Rust 的时候，需要遵守一定的规范，这样可以避免未定义行为的发生。

关于 Unsafe Rust 下的一些专用术语可以查看 [Unsafe 代码术语指南](./unsafe_rust/glossary.md) 。

**Unsafe Rust 的语义：这是编译器无法保证安全的地方，需要程序员来保证安全。** 

---



## 本节包含内容如下：

- [内存](./unsafe_rust/mem.md)
- [裸指针](./unsafe_rust/raw_ptr.md)
- [安全抽象](./unsafe_rust/safe_abstract.md)
- [联合体](./unsafe_rust/union.md)
- [FFi](./unsafe_rust/ffi.md)
- [IO](./unsafe_rust/io.md)
- [Unsafe 代码术语指南](./unsafe_rust/glossary.md)

---
<!-- toc -->
---



## P.UNS.01  不要为了逃避 编译器安全检查而滥用 Unsafe Rust

**【描述】**

Unsafe Rust 有其应用范围和目标，不要为了逃避 编译器安全检查而随便滥用 Unsafe Rust。

## P.UNS.02  不要为了提升性能而盲目使用 Unsafe Rust

**【描述】**

对比 Safe 代码的性能看是否够用，就可以减少不必要的 Unsafe。

---

## G.UNS.01  不要随便为 带有 `unsafe`命名的 类型或方法创建别名

**【级别：建议】**

建议按此规范执行。

**【描述】**

 Rust 里  `unsafe` 字样用于提醒开发者在编写代码的时候注意保证安全。如果修改别名，隐藏了这种提醒，不利于展示这种信息。

不利于开发者去保证安全。

**【反例】**

```rust
use std::cell::{UnsafeCell as TotallySafeCell};

extern crate crossbeam;
use crossbeam::{spawn_unsafe as spawn};
```

**【正例】**

```rust
use std::cell::{UnsafeCell  };

extern crate crossbeam;
use crossbeam::{spawn_unsafe  };
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [unsafe_removed_from_name](https://rust-lang.github.io/rust-clippy/master/#unsafe_removed_from_name) | yes           | no           | style      | warn  |
