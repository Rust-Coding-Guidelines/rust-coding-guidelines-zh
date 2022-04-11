## G.TYP.ENM.03  在使用类似 C 语言的枚举写法且使用`repr(isize/usize)` 布局时注意 32位架构上截断的问题

**【级别】** 建议

**【描述】**

在使用类似 C 语言的枚举写法且使用`repr(isize/usize)` 布局时，在32位架构上会截断变体值，但在64位上工作正常。

**【反例】**

```rust

#[repr(usize)]
enum NonPortable {
    X = 0x1_0000_0000, // 不符合：如果在 32位架构上会截断变体值，导致该指针地址变化
    Y = 0,
}
```

**【正例】**

因为当前 lint 默认是` deny`，所以需要将其配置为 `allow`。

```rust
#![allow(clippy::enum_clike_unportable_variant)]

#[repr(isize)]
pub enum ZBarColor {
    ZBarSpace = 0, // 符合：因为值足够小，没有截断风险
    ZBarBar   = 1,
}

// 符合：没有指定 repr(isize/usize)
#[allow(clippy::enum_clike_unportable_variant)]
pub(crate) enum PropertyType {
    ActionItemSchemaVersion = 0x0C003473,
    ActionItemStatus = 0x10003470,
    ActionItemType = 0x10003463,
    Author = 0x1C001D75,
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [enum_clike_unportable_variant](https://rust-lang.github.io/rust-clippy/master/#enum_clike_unportable_variant) | yes           | no           | correctness | deny  |


