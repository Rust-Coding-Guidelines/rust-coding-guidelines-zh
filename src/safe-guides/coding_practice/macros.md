# 3.16 宏

Rust 通过宏来支持元编程。其中宏有很多种，按实现方式可以分为两大类：声明宏（Declarative）和 过程宏（Procedural）。

按功能效果，过程宏又可以分为三类：

1.  Function-like 宏。类似于声明宏那样，像函数调用一样去使用的宏。
2.  Derive 宏。用于为数据类型自动生成一些 语法项（item），比如 trait 、结构体、方法等。
3.  Attribute 宏。用于更加通用的代码生成功能。

Rust 语言核心库和标准库都内置了一些声明宏和过程宏，以方便开发者使用。

内置的属性宏按功能大体又可以分为四类：
> 注：属性宏固定语法为 `#[attr]` 或 `#![attr]`，以下使用用例均已简化为 `attr` 的形式。即 `test`, `allow(c)` 代表其在 Rust 内的实现可分别表现为 `#[test]` 及 `#[allow(c)]`。

1. 测试属性。`test` 属性宏用于将某个函数标记为单元测试函数。
2. 诊断（[Diagnostic](https://doc.rust-lang.org/reference/attributes/diagnostics.html#diagnostic-attributes)）属性。用于在编译过程中控制和生成诊断信息。包括：
   1. `allow(c)` / `warn(c)` / `deny(c)` / `forbid(c)`  等。
   2. `must_use` 。
3. [代码生成属性](https://doc.rust-lang.org/reference/attributes/codegen.html)。包括：`inline` / `cold` / `target_feature` 等。
4. [编译时限制属性](https://doc.rust-lang.org/reference/attributes/limits.html)。包括：`recursion_limit ` / `type_length_limit` 。
5. [类型系统属性](https://doc.rust-lang.org/reference/attributes/type_system.html)。包括：`non_exhaustive` 。

**宏编程规范：**

使用宏时，需要从 `声明宏` 和 `过程宏` 各自的特性为出发点，来安全使用它。

- [声明宏规范](./macros/decl.md)
- [过程宏规范](./macros/proc.md)

**宏展开命令：**

```text
# 对单个 rs 文件
rustc -Z unstable-options --pretty expanded hello.rs
# 对项目里的二进制 rs 文件
cargo rustc --bin hello -- -Z unstable-options --pretty=expanded
```
