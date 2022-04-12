## G.TYP.SCT.03 宜使用结构体功能更新语法来提升代码可读性

**【级别】** 建议

**【描述】**

更改结构体最好提供结构体方法进行更改，而不是直接构建结构体内容。

但是需要多次修改结构体内容的时候，宜使用结构体更新语法来提升代码可读性。

**【反例】**

```rust
// 不符合
impl<'a> Colorize for &'a str {
    fn red(self) -> ColoredString {
        ColoredString {
            fgcolor: String::from("31"),
            input: String::from(self), // 该方法只更新 fgcolor 和 input
            bgcolor: String::default(); // 如果该结构体字段比较多的话，此处就需要指派很多字段，不太方便
        }   
    }
    fn on_yellow(self) -> ColoredString {
        ColoredString {
            bgcolor: String::from("43"),
            input: String::from(self),
            fgcolor: String::default();
        }   
    }
}
```

**【正例】**

```rust
// 符合
impl<'a> Colorize for &'a str {
    fn red(self) -> ColoredString {
        ColoredString {
            fgcolor: String::from("31"),
            input: String::from(self),
            ..ColoredString::default() // 通过该语法，开发者可以快速了解该方法只更新 fgcolor 和 input，也不需要指派其他不需要更新的字段，更加方便
        }   
    }
    fn on_yellow(self) -> ColoredString {
        ColoredString {
            bgcolor: String::from("43"),
            input: String::from(self),
            ..ColoredString::default()
        }
    }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [field_reassign_with_default](https://rust-lang.github.io/rust-clippy/master/#field_reassign_with_default) | yes           | no           | style      | warn  |


