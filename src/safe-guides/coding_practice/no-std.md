# no-std 

`no-std` 是指 被标示为 `#![no_std]` 的 crate，意味着该 crate 将链接到 `core` crate 而非 `std` crate。

`no-std`  代表 裸机编程，嵌入式 Rust。

Rust 也有 `#![no_core]` 属性，但是还未稳定，不建议使用。

>  参考数据： `core` 在编译后文件大小中只占大约 3k 大小。

---
<!-- toc -->
---

## P.EMB.01   `no-std` 下必须定义一个恐慌处理程序（`#[panic_handler]`）以确保安全

【描述】

恐慌处理程序的编写应该非常谨慎，以确保程序的安全。

## P.EMB.02   要确保程序中的类型有正确的内存布局

【描述】

链接器决定 no-std 程序的最终内存布局，但我们可以使用[链接器脚本](https://sourceware.org/binutils/docs/ld/Scripts.html)对其进行一些控制。链接器脚本给我们的布局控制粒度是在 段（ Section） 级别。段是在连续内存中布置的 符号 集合。反过来，符号可以是数据（静态变量）或指令（Rust 函数）。

这些编译器生成的符号和段名称不能保证在 Rust 编译器的不同版本中保持不变。但是，Rust 允许我们通过以下属性控制符号名称和部分位置：

- `#[export_name = "foo"]`将符号名称设置为`foo`.
- `#[no_mangle]`意思是：使用函数或变量名（不是它的完整路径）作为它的符号名。 `#[no_mangle] fn bar()`将产生一个名为 的符号`bar`。
- `#[link_section = ".bar"]`将符号放置在名为 的部分中`.bar`。

通过这些属性，我们可以公开程序的稳定 ABI 并在链接描述文件中使用它。

## P.EMB.03   将一些公用的类型、函数、宏等集中到一个自定义的 `baremetal-std`

【描述】

虽然 `no-std` 下不能用Rust 的标准库，但是可以自定义 `no-std` 下的标准库 `baremetal-std`，用于积累 `no-std` 下常用的公共库。

