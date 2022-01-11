## G.UNS.FFi.01  自定义数据类型要保证一致的数据布局

**【级别】** 要求

**【描述】**

Rust 编译器为了优化内存布局，会对结构体字段进行重排。所以在 FFi 边界，应该注意结构体内存布局和 C 的一致。

关于 如何选择合适的`repr` 属性可参考：[P.UNS.MEM.01](../mem.md) 

以下是不合适用于和 C 语言交互的类型：

1. 没有使用任何 `#[repr( )]` 属性修饰的自定义类型
2. 动态大小类型 (dynamic sized type)
3. 指向动态大小类型对象的指针或引用 (fat pointers)
4. str 类型、tuple 元组、闭包类型

**【正例】**

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

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

检测 `-sys` 或 `-ffi` 后缀的crate 或 模块内的自定义结构体、enum、union 有没有指定 `repr` 布局
