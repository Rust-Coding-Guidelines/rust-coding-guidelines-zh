# 枚举体

Rust 的枚举是一种带 Tag 的联合体。 一般分为三类：空枚举、无字段（fieldless）枚举和数据承载（data carrying）枚举。

【示例】

```rust
enum Empty {}

enum Fieldless {
    A,
    B,
    C = 42, // 可以自定义判别式
}

enum DataCarrying {
    Foo(i32, i32),
    Bar(String)
}
```

Rust 中枚举体用处很多，你甚至可以将其作为一种接口使用。






---


## G.TYP.Enum.01 要避免使用`and_then`而使用`map`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bind_instead_of_map ](https://rust-lang.github.io/rust-clippy/master/#bind_instead_of_map ) | yes| no | complexity | warn |

### 【描述】

为了让代码更加简单明了增强可读性，建议使用 `map`。

【正例】

```rust
# fn opt() -> Option<&'static str> { Some("42") }
# fn res() -> Result<&'static str, &'static str> { Ok("42") }
let _ = opt().map(|s| s.len());
let _ = res().map(|s| if s.len() == 42 { 10 } else { 20 });
let _ = res().map_err(|s| if s.len() == 42 { 10 } else { 20 });
```

【反例】

```rust
# fn opt() -> Option<&'static str> { Some("42") }
# fn res() -> Result<&'static str, &'static str> { Ok("42") }
let _ = opt().and_then(|s| Some(s.len()));
let _ = res().and_then(|s| if s.len() == 42 { Ok(10) } else { Ok(20) });
let _ = res().or_else(|s| if s.len() == 42 { Err(10) } else { Err(20) });

```



## G.TYP.Enum.02  除非必要，不要自己创建空枚举

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [empty_enum](https://rust-lang.github.io/rust-clippy/master/#empty_enum) | yes           | no           | **pedantic** | allow |

### 【描述】

在 Rust 中 只有 `never` 类型（`!`）才是唯一合法表达 无法被实例化类型 的类型。但目前 `never` 类型还未稳定，只能在 Nightly 下使用。

【正例】

所以，如果想在 稳定版 Rust 中使用，建议使用[`std::convert::Infallible`](https://doc.rust-lang.org/std/convert/enum.Infallible.html#) 。 `Infallible` 枚举是一个合法的 空枚举，常用于错误处理中，表示永远不可能出现的错误。但是目前也可以用于在稳定版中替代  `never`   类型。

```rust
// 未来 never 类型稳定的话，将会把 Infallible 设置为 never 类型的别名
pub type Infallible = !;
```

【反例】

```rust
enum Test {}
```

【例外】

因为 [`std::convert::Infallible`](https://doc.rust-lang.org/std/convert/enum.Infallible.html#) 默认实现了很多 trait，如果不想依赖其他 trait ，那么可以用 空枚举。

```rust
pub enum NoUserError {}

impl Display for NoUserError {
    fn fmt(&self, _formatter: &mut fmt::Formatter) -> fmt::Result {
        match *self {}
    }
}

```



## G.TYP.Enum.03  在使用类似 C 语言的枚举写法且使用`repr(isize/usize)` 布局时要注意 32位架构上截断的问题

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [enum_clike_unportable_variant](https://rust-lang.github.io/rust-clippy/master/#enum_clike_unportable_variant) | yes           | no           | correctness | deny  |

### 【描述】

 在使用类似 C 语言的枚举写法且使用`repr(isize/usize)` 布局时，在32位架构上会截断变体值，但在64位上工作正常。

但是没有这种风险的时候，可以正常使用。

【正例】

因为当前 lint 默认是` deny`，所以需要将其配置为 `allow`。

```rust
#![allow(clippy::enum_clike_unportable_variant)]

#[repr(isize)]
pub enum ZBarColor {
    ZBarSpace = 0,
    ZBarBar   = 1,
}

// 或者，没有指定 repr(isize/usize)

#[allow(clippy::enum_clike_unportable_variant)]
pub(crate) enum PropertyType {
    ActionItemSchemaVersion = 0x0C003473,
    ActionItemStatus = 0x10003470,
    ActionItemType = 0x10003463,
    Author = 0x1C001D75,
}
```

【反例】

```rust
#[cfg(target_pointer_width = "64")]
#[repr(usize)]
enum NonPortable {
    X = 0x1_0000_0000,
    Y = 0,
}
```



## G.TYP.Enum.04    一般情况下，不要使用  `use` 对 Enum 的全部变体（variants）

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [enum_glob_use](https://rust-lang.github.io/rust-clippy/master/#enum_glob_use) | yes           | no           | pedantic   | allow |

### 【描述】

因为使用 Enum 的类型前缀可以使代码更加可读。

【正例】

```rust
use std::cmp::Ordering;
foo(Ordering::Less)
```

【反例】

```rust
use std::cmp::Ordering::*; // 这里导入了全部变体
foo(Less);
```

【例外】

当枚举体非常多的时候，比如 [ glutin::event::VirtualKeyCode](https://docs.rs/glutin/0.27.0/glutin/event/enum.VirtualKeyCode.html) 这类对应键盘按键的枚举，并且上下文比较明确，都是在处理和 Key 相关的内容时，可以直接全部导入。

```rust
// From:  https://github.com/alacritty/alacritty/blob/master/alacritty/src/config/bindings.rs#L368
#![allow(clippy::enum_glob_use)]

pub fn default_key_bindings() -> Vec<KeyBinding> {
    let mut bindings = bindings!(
        KeyBinding;
        Copy;  Action::Copy;
        Copy,  +BindingMode::VI; Action::ClearSelection;
        Paste, ~BindingMode::VI; Action::Paste;
        L, ModifiersState::CTRL; Action::ClearLogNotice;
        L,    ModifiersState::CTRL,  ~BindingMode::VI, ~BindingMode::SEARCH;
            Action::Esc("\x0c".into());
        Tab,  ModifiersState::SHIFT, ~BindingMode::VI, ~BindingMode::SEARCH;
            Action::Esc("\x1b[Z".into());
        // ...
    }
```

## G.TYP.Enum.05    对外导出的公开的 Enum，建议增加 `#[non_exhaustive]`属性

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [exhaustive_enums](https://rust-lang.github.io/rust-clippy/master/#exhaustive_enums) | yes           | no           | restriction | allow |
| [manual_non_exhaustive](https://rust-lang.github.io/rust-clippy/master/#manual_non_exhaustive) | yes           | no           | style       | warn  |

### 【描述】

作为对外公开的 Enum，为了保持稳定性，应该使用 `#[non_exhaustive]`属性，避免因为将来Enum 枚举变体的变化而影响到下游的使用。

【正例】

```rust
#[non_exhaustive]
enum E {
    A,
    B,
}
```

【反例】

在 `#[non_exhaustive]` 属性稳定之前，社区内还有一种约定俗成的写法来达到防止下游自定义枚举方法。通过 `manual_non_exhaustive` 可以监控这类写法。

```rust
enum E {
    A,
    B,
    #[doc(hidden)]
    _C, // 这里用 下划线作为前缀定义的变体，作为隐藏的变体，不对外展示
}
// 用户无法自定义实现该 枚举的方法，达到一种稳定公开枚举的目的。
```



## G.TYP.Enum.06    注意 Enum 内变体的大小差异不要过大

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_enum_variant](https://rust-lang.github.io/rust-clippy/master/#large_enum_variant) | yes           | no           | perf       | warn  |

该 lint 可以通过 clippy 配置项 `enum-variant-size-threshold = 200` 来配置，默认是 `200` 字节。

### 【描述】

要注意 Enum 内 变体 的大小差异不要过大，因为 Enum 内存布局是以最大的那个变体进行对齐。根据场景，如果该Enum 实例中小尺寸变体的实例使用很多的话，那内存就会有所浪费。但是如果小尺寸变体的实例使用很少，则无所谓。

解决办法就是把大尺寸变体包到 `Box<T>`中。

【正例】

```rust
enum Test {
    A(i32),
    B(Box<[i32; 1000]>),
    C(Box<[i32; 8000]>),
}
```

【反例】

```rust
enum Test {
    A(i32),
    B([i32; 1000]),
    C([i32; 8000]),
}
```

