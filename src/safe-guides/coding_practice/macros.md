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

## 列表

- [P.MAC.01 不要轻易使用宏](./macros/P.MAC.01.md)
- [P.MAC.02 实现宏语法的时候，应该尽量贴近 Rust 语法](./macros/P.MAC.02.md)
- [G.MAC.01 dbg!() 宏只应该在 Debug 模式下使用](./macros/G.MAC.01.md)
- [G.MAC.02 使用宏时应该考虑宏展开会让编译文件膨胀的影响](./macros/G.MAC.02.md)
- [声明宏](./macros/decl.md)
    - [P.MAC.DCL.01 不要将声明宏内的变量作为外部变量使用](./macros/decl/P.MAC.DCL.01.md)
    - [P.MAC.DCL.02 在编写多个宏规则时，应该先从匹配粒度最小的开始写](./macros/decl/P.MAC.DCL.02.md)
    - [P.MAC.DCL.03 不要在片段分类符跟随它不匹配的符号](./macros/decl/P.MAC.DCL.03.md)
    - [P.MAC.DCL.04 匹配规则要精准，不要模糊不清](./macros/decl/P.MAC.DCL.04.md)
    - [P.MAC.DCL.05 使用宏替换（substitution）元变量的时候要注意选择合适的片段分类符](./macros/decl/P.MAC.DCL.05.md)
    - [P.MAC.DCL.06 当宏需要接收 self 时需要注意](./macros/decl/P.MAC.DCL.06.md)
    - [P.MAC.DCL.07 确保在宏定义之后再去调用宏](./macros/decl/P.MAC.DCL.07.md)
    - [P.MAC.DCL.08 同一个 crate 内定义的宏相互调用时，需要注意卫生性](./macros/decl/P.MAC.DCL.08.md)
- [过程宏](./macros/proc.md)
    - [P.MAC.PRO.01 不要使用过程宏来规避静态分析检查](./macros/proc/P.MAC.PRO.01.md)
    - [P.MAC.PRO.02 实现过程宏时要对关键特性增加测试](./macros/proc/P.MAC.PRO.02.md)
    - [P.MAC.PRO.03 保证过程宏的卫生性](./macros/proc/P.MAC.PRO.03.md)
    - [P.MAC.PRO.04 给出正确的错误位置](./macros/proc/P.MAC.PRO.04.md)
    - [P.MAC.PRO.05 代码生成要按情况选择使用过程宏还是 build.rs](./macros/proc/P.MAC.PRO.05.md)
    - [P.MAC.PRO.06 build.rs 生成的代码要保证没有任何警告](./macros/proc/P.MAC.PRO.06.md)