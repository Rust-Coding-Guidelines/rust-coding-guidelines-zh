# FFi 规范

Rust 可以通过C-ABI无缝与C语言打交道，也可以通过暴露 C-ABI 接口供其他语言调用。但是跨边界本质上是不安全的。

一般来说，FFi 是指在其他语言中调用 Rust 代码，Rust代码会按 C-ABI 来暴露接口。这类 Rust crate或模块，常以 `-ffi`后缀结尾。

另一类是 Rust 去调用 C-ABI 接口，相关代码通常被封装到以 `-sys` 为后缀命名的 crate 或 模块中。 

本小节内容，包含以上两种情况。  

---

## P.UNS.FFi.01   自定义数据类型要保证一致的数据布局

**【描述】**

Rust 编译器为了优化内存布局，会对结构体字段进行重排。所以在 FFi 边界，应该注意结构体内存布局和 C 的一致。

关于 如何选择合适的`repr` 属性可参考：[P.UNS.MEM.01](./mem.md) 

以下是不合适用于和 C 语言交互的类型：

1. 没有使用任何 `#[repr( )]` 属性修饰的自定义类型
2. 动态大小类型 (dynamic sized type)
3. 指向动态大小类型对象的指针或引用 (fat pointers)
4. str 类型、tuple 元组、闭包类型

【正例】

```rust
#[repr(C)]
struct Data {
    a: u32,
    b: u16,
    c: u64,
}
#[repr(C, packed)]
struct PackedData {
    a: u32,
    b: u16,
    c: u64,
}
```

## P.UNS.FFi.02    避免从公开的 Rust API  直接传字符串到 C  中

**【描述】**

在跨越 C 边界的时候，应该对 字符串进行边界检查，避免传入一些非法字符串。

【示例】

这个示例中，从公开的 Rust API 传入非法字符串到 C ，导致字符串格式化漏洞。

```rust
// From: https://github.com/RustSec/advisory-db/issues/106

extern crate pancurses;

use pancurses::{initscr, endwin};

fn main() {
    let crash = "!~&@%+ S"; //  特意构造非法字符串

    let window = initscr();
    window.printw(crash); // 通过该函数跨 C边界传入非法字符串，引起字符串格式化漏洞
    window.refresh();
    window.getch();
    endwin();
}
```

## P.UNS.FFi.03    在使用标准库 `std::ffi`模块提供的类型时需要仔细查看其文档

**【描述】**

因为该模块中提供了用于和其他语言类 C 字符串打交道的 FFi 绑定和类型，在使用前务必要看清楚它们的文档，否则会因为所有权管理不当而导致无效内存访问、内存泄漏和其他内存错误。



## P.UNS.FFi.04    当使用 来自 C 的指针时，如果该指针需要管理内存，则需要为包装该指针的 Rust 类型实现 Drop trait

**【描述】**

Rust 里通过结构体包装该指针，并且为该结构体实现 Drop 来保证相关资源可以安全释放。

## P.UNS.FFi.05    如果一个函数正在跨越 FFi 边界，那么需要处理恐慌

**【描述】**

如果让恐慌在跨越 FFi 边界时发生，可能会产生未定义行为。

处理恐慌可以使用 `catch_unwind`，但是它只对实现了 `UnwindSafe` trait 的类型起作用。另外一种方法就是避免恐慌，而返回错误码。

【示例】

```rust
use std::panic::catch_unwind;

#[no_mangle]
pub extern fn oh_no() -> i32 {
    let result = catch_unwind(|| {
        panic!("Oops!"); // 这里会发生恐慌，需要处理
    });
    match result {
        Ok(_) => 0,
        Err(_) => 1,
    }
}

fn main() {}
```

## P.UNS.FFi.05    不要在 FFi 中使用 任何零大小类型

**【描述】**

零大小类型在 C 中是无效的。也不要叫 Rust 中的单元类型 `()` 和 C 中的 `void` 混为一谈。

## P.UNS.FFi.06    建议使用诸如标准库或 `libc` crate所提供的可移植类型别名，而不是特定平台的类型

**【描述】**

当与外部(如C或c++)接口交互时，通常需要使用平台相关的类型，如C的`int`、`long`等。除了`std::ffi`(或`core::ffi`)中的c void外，标准库还在`std:os::raw`(或`core::os::raw`)中提供了可移植类型别名。`libc` crate 基本覆盖了所有的C标准库中的C兼容类型。

这样有助于编写跨平台的代码。

## P.UNS.FFi.07 Rust 和 C 之间传递字符或字符串时需要注意字符串要符合 C-ABI 以及 字符串的编码

**【描述】**

注意要使用 `c_char` 对应 C 语言的字符。`libc::c_char` 和 `std::os::raw::c_char` 在大多数 64位 linux 上都是相同的。

FFi 接口使用的字符串要符合 C 语言约定，即使用 `\0` 结尾且中间不要包含 `\0`字符的字符串。

Rust 中字符串要求 `utf-8 `编码，而 C 字符串则没有这个要求。所以需要注意编码。

## P.UNS.FFi.08   从外部传入的不健壮类型的外部值要进行检查

**【描述】**

Safe Rust 会保证类型的有效性和安全性，但是 Unsafe Rust 中，特别是编写 FFi 的时候，很容易从外部传入无效值。

Rust 中很多类型都不太健壮：

- 布尔类型。外部传入的 布尔类型可能是数字也可能是字符串。
- 引用类型。Rust 中的引用仅允许执行有效的内存对象，但是在Unsafe 中使用引用，任何偏差都可能引起未定义行为。
- 函数指针。跨越 FFi 边界的函数指针可能导致任意代码执行。
- Enum。 跨 FFi 边界两端的 枚举值要经过合法转换。
- 浮点数。
- 包含上述类型的复合类型

## P.UNS.FFi.09   不要为任何传入到外部的类型实现 Drop

**【描述】**

因为有可能在传出去之前被析构。需要明确是由哪种语言负责分配和释放内存，谁分配内存，谁来释放。

