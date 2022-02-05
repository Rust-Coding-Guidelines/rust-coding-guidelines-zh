# 过程宏

[过程宏（*Procedural macros*）](https://doc.rust-lang.org/reference/procedural-macros.html) 允许开发者来创建语法扩展。你可以通过过程宏创建类似函数的宏、派生宏以及属性宏。

广义上的"过程宏"指的是通过 syn/quote(毕竟几乎全部过程宏库都用 syn) 及 syn 生态(例如 darling) 进行代码生成等元编程操作。

syn/quote 不仅能用于过程宏，还广泛用于代码生成(*codegen*)、静态分析等用途，例如 tonic-build/prost 源码中也用到了 syn/quote 。

因此本过程宏规范不仅适用于过程宏，部分规范(例如 [P.MAC.Proc.06](./macros/proc/P.MAC.Proc.06.md))还适用于 prost 这种代码生成库

过程宏必须被单独定义在一个类型为`proc-macro` 的 crate 中。

过程宏有两类报告错误的方式：`panic` 或 通过 `compile_error`  宏调用发出错误。

过程宏不具有卫生性（hygiene），这意味着它会受到外部语法项（item）的影响，也会影响到外部导入。

过程宏可以在编译期执行任意代码。

## 列表

- [P.MAC.PRO.01 不要使用过程宏来规避静态分析检查](.proc/P.MAC.PRO.01.md)
- [P.MAC.PRO.02 实现过程宏时要对关键特性增加测试](.proc/P.MAC.PRO.02.md)
- [P.MAC.PRO.03 保证过程宏的卫生性](.proc/P.MAC.PRO.03.md)
- [P.MAC.PRO.04 给出正确的错误位置](.proc/P.MAC.PRO.04.md)
- [P.MAC.PRO.05 代码生成要按情况选择使用过程宏还是 build.rs](.proc/P.MAC.PRO.05.md)
- [P.MAC.PRO.06 build.rs 生成的代码要保证没有任何警告](.proc/P.MAC.PRO.06.md)