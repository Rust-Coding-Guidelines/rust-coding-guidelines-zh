
## G.NAM.01 使用统一的命名风格

**【级别】** 要求

**【描述】**

Rust 倾向于在“类型”级的结构中使用大驼峰（`UpperCamelCase`） 命名风格，在 “变量、值（实例）、函数名”等结构中使用蛇形（`snake_case`）命名风格。

下面是汇总信息：

| Item                                    | 规范                                                                                       |
| --------------------------------------- | ------------------------------------------------------------------------------------------ |
| 包（Crates）                            | [通常使用 snake_case](https://github.com/rust-lang/api-guidelines/issues/29) [^crate-name] |
| 模块（Modules）                         | `snake_case`                                                                               |
| 类型（Types）                           | `UpperCamelCase`                                                                           |
| 特质（Traits）                          | `UpperCamelCase`                                                                           |
| 枚举体（Enum variants）                 | `UpperCamelCase`                                                                           |
| 函数（Functions）                       | `snake_case`                                                                               |
| 方法（Methods）                         | `snake_case`                                                                               |
| 通用构造函数（General constructors）    | `new` 或者 `with_more_details`                                                             |
| 转换构造函数（Conversion constructors） | `from_some_other_type`                                                                     |
| 宏（Macros）                            | `snake_case!`                                                                              |
| 本地变量（Local variables）             | `snake_case`                                                                               |
| 静态变量（Statics）                     | `SCREAMING_SNAKE_CASE`                                                                     |
| 常量（Constants）                       | `SCREAMING_SNAKE_CASE`                                                                     |
| 类型参数（Type parameters）             | 简明的 `UpperCamelCase` ，通常使用单个大写字母： `T`                                       |
| 生命周期（Lifetimes）                   | 简短的 `lowercase`，通常使用单个小写字母 `'a`, `'de`, `'src`，尽量保持语义                 |
| 特性（Features）                        | `snake_case`                                                                               |

说明 :

1. 在 `UpperCamelCase`情况下，由首字母缩写组成的缩略语和 复合词的缩写，算作单个词。比如，应该使用 `Uuid` 而非 `UUID`，使用 `Usize` 而不是 `USize`，或者是 `Stdin` 而不是 `StdIn`。
2. 在`snake_case`中，首字母缩写和缩略词是小写的 `is_xid_start`。
3. 在 `snake_case` 或者 `SCREAMING_SNAKE_CASE` 情况下，每个词不应该由单个字母组成——除非这个字母是最后一个词。比如，使用 `btree_map` 而不使用 `b_tree_map`，使用 `PI_2` 而不使用 `PI2` 。


关于包命名：

- 由于历史问题，包名有两种形式 `snake_case` 或 `kebab-case` ，但实际在代码中需要引入包名的时候，Rust 只能识别 `snake_case`，也会自动将 `kebab-case` 识别为  `kebab_case`。所以建议使用`snake_case`。
- Crate 的名称通常不应该使用 `-rs` 或者 `-rust` 作为后缀或者前缀。但是有些情况下，比如是其他语言移植的同名 Rust 实现，则可以使用 `-rs` 后缀来表明这是 Rust 实现的版本。

**【参考】**

Rust 命名规范在 [RFC 0430](https://github.com/rust-lang/rfcs/blob/master/text/0430-finalizing-naming-conventions.md) 中有也描述。

**【Lint 检测】**

| lint name                                                                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group |
| ------------------------------------------------------------------------------------------------------------------------ | ------------- | ------------ | ---------- |
| [`Rustc: non_camel_case_types`](https://doc.rust-lang.org/rustc/lints/listing/warn-by-default.html#non-camel-case-types) | no            | yes          | Style      |
| [`Rustc: non_snake_case`](https://doc.rust-lang.org/rustc/lints/listing/warn-by-default.html#non-snake-case)             | no            | yes          | Style      |
