## G.TYP.ENM.04 不宜在`use`语句中引入Enum的全部变体（variants）

**【级别】** 建议

**【描述】**

使用 Enum 的类型前缀可以使代码更加可读。

**【反例】**

```rust
use std::cmp::Ordering::*; // 这里导入了全部变体
foo(Less);
```

**【正例】**

```rust
use std::cmp::Ordering;
foo(Ordering::Less)
```

**【例外】**

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

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [enum_glob_use](https://rust-lang.github.io/rust-clippy/master/#enum_glob_use) | yes           | no           | pedantic   | allow |


