## G.TYP.ENM.04 不宜在`use`语句中引入Enum的全部变体（variants）

**【级别】** 建议

**【描述】**

使用 Enum 的类型前缀可以使代码更加可读。

**【反例】**

```rust
#![warn(clippy::enum_glob_use)]

// 不符合
use std::cmp::Ordering::*; // 这里导入了全部变体
foo(Less);
```

**【正例】**

```rust
#![warn(clippy::enum_glob_use)]

// 符合
use std::cmp::Ordering;
foo(Ordering::Less)


```

**【例外】**

当枚举体非常多的时候，比如 [oci_spec::Arch](https://docs.rs/crate/oci-spec/0.5.1/source/src/runtime/linux.rs#:~:text=clippy%3a%3aenum_clike_unportable_variant) 中对应平台架构的枚举值，直接用 `*` 导入会更加方便。

```rust
// From:  https://github.com/alacritty/alacritty/blob/master/alacritty/src/config/bindings.rs#L368
#![allow(clippy::enum_glob_use)]
use oci_spec::Arch::*;

pub enum Arch {
    /// The native architecture.
    ScmpArchNative = 0x00000000,

    /// The x86 (32-bit) architecture.
    ScmpArchX86 = 0x40000003,

    /// The x86-64 (64-bit) architecture.
    ScmpArchX86_64 = 0xc000003e,

    // ... more 
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [enum_glob_use](https://rust-lang.github.io/rust-clippy/master/#enum_glob_use) | yes           | no           | pedantic   | allow |


