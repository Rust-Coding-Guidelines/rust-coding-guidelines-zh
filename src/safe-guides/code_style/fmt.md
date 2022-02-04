# 格式

Rust 有自动化格式化工具 rustfmt ，可以帮助开发者摆脱手工调整代码格式的工作，提升生产力。但是，rustfmt 遵循什么样的风格规范，作为开发者应该需要了解，在编写代码的时候可以主动按这样的风格编写。

说明：以下 `rustfmt` 配置中对应配置项如果 `Stable`为`No`，则表示该配置项不能用于 Stable Rust 下在 `rustfmt.toml` 中自定义，但其默认值会在`cargo fmt`时生效。在 Nightly Rust 下则都可以自定义。

在 Stable Rust 下使用未稳定配置项的方法、了解配置示例及其他全局配置项说明请参阅：[Rustfmt 配置相关说明](./../Appendix/tools/rustfmt.md) 。

注意： 以下规则 针对 rustfmt  version 1.4.36 版本。


## 列表

- [P.FMT.01 代码格式以保证可读性为前提](./fmt/P.FMT.01.md)
- [G.FMT.01 始终使用 rustfmt 进行自动格式化代码](./fmt/G.FMT.01.md)
- [G.FMT.02 缩进始终使用空格（space）而非制表符（tab）](./fmt/G.FMT.02.md)
- [G.FMT.03 每行最大宽度为 100 个字符](./fmt/G.FMT.03.md)
- [G.FMT.04 行间距最大宽度空一行](./fmt/G.FMT.04.md)
- [G.FMT.05 语言项（Item) 定义时花括号（brace）位置应该与语言项保持同一行](./fmt/G.FMT.05.md)
- [G.FMT.06 单行规则](./fmt/G.FMT.06.md)
- [G.FMT.07 存在多个标识符时应该保持块状（Block）缩进](./fmt/G.FMT.07.md)
- [G.FMT.08 换行样式以文件自动检测为主](./fmt/G.FMT.08.md)
- [G.FMT.09 当有多行表达式操作时，操作符应该置于行首](./fmt/G.FMT.09.md)
- [G.FMT.10 枚举变体和结构体字段相互之间默认左对齐](./fmt/G.FMT.10.md)
- [G.FMT.11 多个函数参数和导入模块的布局](./fmt/G.FMT.11.md)
- [G.FMT.12 空格使用规则](./fmt/G.FMT.12.md)
- [G.FMT.13 结尾逗号规则](./fmt/G.FMT.13.md)
- [G.FMT.14 match 分支格式](./fmt/G.FMT.14.md)
- [G.FMT.15 导入模块分组规则](./fmt/G.FMT.15.md)
- [G.FMT.16 声明宏分支格式](./fmt/G.FMT.16.md)
- [G.FMT.17 具名结构体字段初始化时字段名最好不要省略](./fmt/G.FMT.17.md)
- [G.FMT.18 extern 外部函数需要显式指定 ABI](./fmt/G.FMT.18.md)
- [G.FMT.19 解构元组的时候允许使用`..`来指代剩余元素](./fmt/G.FMT.19.md)
- [G.FMT.20 不要将多个不相关的派生(Derive)宏合并为同一行](./fmt/G.FMT.20.md)