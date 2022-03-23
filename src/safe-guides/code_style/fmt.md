# 2.2 格式

制定统一的编码风格，是为了提升代码的可读性，让日常代码维护和团队之间审查代码更加方便。

Rust 有自动化格式化工具 rustfmt ，可以帮助开发者摆脱手工调整代码格式的工作，提升生产力。但是，rustfmt 遵循什么样的风格规范，作为开发者应该需要了解，在编写代码的时候可以主动按这样的风格编写。

说明：以下 `rustfmt` 配置中对应配置项如果 `Stable`为`No`，则表示该配置项不能用于 Stable Rust 下在 `rustfmt.toml` 中自定义，但其默认值会在`cargo fmt`时生效。在 Nightly Rust 下则都可以自定义。

在 Stable Rust 下使用未稳定配置项的方法、了解配置示例及其他全局配置项说明请参阅：[Rustfmt 配置相关说明](./../Appendix/tools/rustfmt.md) 。

注意： 以下规则 针对 rustfmt  version 1.4.36 版本。


## 列表

- [P.FMT.01 始终使用 rustfmt 进行自动格式化代码](./fmt/P.FMT.01.md)
- [P.FMT.02 缩进始终使用空格（space）而非制表符（tab）](./fmt/P.FMT.02.md)
- [P.FMT.03 行间距最大宽度空一行](./fmt/P.FMT.03.md)
- [P.FMT.04 语言项（Item) 定义时花括号（brace）位置应该与语言项保持同一行](./fmt/P.FMT.04.md)
- [P.FMT.05 存在多个标识符时应该保持块状（Block）缩进](./fmt/P.FMT.05.md)
- [P.FMT.06 当有多行表达式操作时，操作符应该置于行首](./fmt/P.FMT.09.md)
- [P.FMT.07 枚举变体和结构体字段相互之间默认左对齐](./fmt/P.FMT.10.md)
- [P.FMT.08 多个函数参数和导入模块的布局](./fmt/P.FMT.11.md)
- [P.FMT.09 空格使用规则](./fmt/P.FMT.12.md)
- [P.FMT.10 match 分支格式](./fmt/P.FMT.14.md)
- [P.FMT.11 导入模块分组规则](./fmt/P.FMT.15.md)
- [P.FMT.12 声明宏分支格式](./fmt/P.FMT.16.md)
- [P.FMT.13 具名结构体字段初始化时字段名最好不要省略](./fmt/P.FMT.17.md)
- [P.FMT.14 extern 外部函数需要显式指定 ABI](./fmt/P.FMT.18.md)
- [P.FMT.15 解构元组的时候允许使用`..`来指代剩余元素](./fmt/P.FMT.19.md)
- [P.FMT.16 不要将多个不相关的派生(Derive)宏合并为同一行](./fmt/P.FMT.20.md)