## G.UNS.Union.01  除了与 C 打交道，尽量不要使用 Union

**【级别】** 要求

**【描述】**

Rust 支持 Union 就是为了调用 C 接口。如果不是 FFi ，就避免使用 Union。

一般情况下请使用 枚举 或 结构体代替。

使用 Copy 类型的值和 `ManuallyDrop` 来初始化 Union 的变体，不需要使用 Unsafe 块。

**【反例】**

```rust
union MyUnion {
    f1: u32,
    f2: f32,
}
```

**【正例】**

```rust
#[repr(C)]
union MyUnion {
    f1: u32,
    f2: f32,
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

这条规则如果需要定制 Lint，则可以检测 Union 联合体上方是否有 `#[repr(C)]`属性定义与C兼容的数据布局，如果没有则给予警告。
