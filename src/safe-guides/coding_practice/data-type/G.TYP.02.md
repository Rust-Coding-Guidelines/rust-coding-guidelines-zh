## G.TYP.02  数字字面量在使用的时候应该明确标注类型

**【级别】** 建议

**【描述】**

如果数字字面量没有被指定具体类型，那么单靠类型推导，整数类型会被默认绑定为 `i32` 类型，而浮点数则默认绑定为 `f64`类型。这可能导致某些运行时的意外。

**【反例】**

```rust
#![warn(clippy::default_numeric_fallback)]
// 不符合
let i = 10; // i32
let f = 1.23; // f64
```

**【正例】**

```rust
#![warn(clippy::default_numeric_fallback)]

// 符合
let i = 10u32;
let f = 1.23f32;
```

**【Lint 检测】**

| lint name                                                                                            | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| ---------------------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [default_numeric_fallback](https://rust-lang.github.io/rust-clippy/master/#default_numeric_fallback) | yes           | no           | restriction | allow     |

