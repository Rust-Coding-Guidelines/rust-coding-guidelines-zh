# 1.2 编码规范基本约定

## 编码规范的内容组织说明

**编程规范绝不是为了增加开发者的负担而编写的，目的是为了帮助开发者写出高质量的 Rust 代码。**

为了达成这个目的，规范条款分为原则和规则两个类别：**原则** 与 **规则**。

- 原则，就是编程开发时指导的一个大方向，或是指一类情况。也有少部分原则是 Rust 编译器可检测的情况，但是因为编译器诊断信息比较迷惑，所以增加了原则，帮助开发者去避免这类情况。

- 规则，相对原则来说，更加具体，包含正例和反例来进一步说明。有些规则也会增加例外的情况。规则基本都是可以通过 lint 进行检测的。


#### 规则内容 与 rustfmt 和 clippy 的关系

规范主要分为两大部分内容：代码风格 和 代码实践。

**代码风格**

在代码风格中包含代码命名、格式和注释：

- 命名部分，主要是通过 clippy lint 来检查，有些命名规则 clippy lint未提供检测，则需要自定义lint来支持。
- 格式部分，主要用 rustfmt 来自动修改，编码规范中的规则对 rustfmt 的大部分配置项进行了分类描述，为了方便开发者进行参考，制定自己的配置项。编码规范中也提供了配置模版。
- 注释部分，其中包括普通注释和文档注释，规则条目通过 rustfmt 和 clippy 合作来进行规范。

**代码实践**

代码实践的内容是按照 Rust 语言特性进行分类，每个语言特性都尽量针对日常编码最佳实践进行总结，提取为一条条的原则和规则，方便开发者进行参考。其中大部分规则都是建议，涉及要求的规则基本都是和安全相关。

这部分内容的规则条目基本都依赖 Clippy lint 去检测，但并非是把 Clippy 500 多条 lint 都一一对应为规则。Clippy lint 中涉及很多技巧类的lint，就没有放到规范中。

**规则主要是侧重于通用场景下，代码可读性、维护性、安全性、性能这四方面的考量，它仅仅覆盖一小部分（不到 1/5）clippy lint**。另外还有一些规则是clippy lint没有的，需要自定义lint。

代码实践内容的重点在于 Unsafe Rust 的编码规范，其中编码原则多于规则，并且 Clippy lint 也很少能检测这部分内容。其中要求类的规则更多一些。

希望通过这部分内容，让开发者在编写 Rust 代码过程中，避开一些常见的坑。


## 编码规范内容约定

通过标题前的编号来标识：

- 标识为`P`为原则（Principle）。编号方式为`P.Element.Number`。
- 标识为`G`为规则（Guideline）。编号方式为`G.Element.Number`。
- 当有子目录时。编号方式为 `P.Element.SubElement.Number` 或 `G.Element.SubElement.Number`。

Number 从`01`开始递增。其中 `Element` 为领域知识中关键元素（本规范中对应的二级目录）的三位英文字母缩略语。（术语参考： [SEI CERT C Coding Standard](https://wiki.sei.cmu.edu/confluence/display/c/SEI+CERT+C+Coding+Standard)）


| Element | 解释   | Element | 解释     |
| ------- | ------ | ------- | -------- |
| NAM     | 命名 (Naming)   | CMT     | 注释 (Comment)    |
| FMT     | 格式 (Format)  | TYP     | 数据类型 (Data Type) |
| CNS     | 常量 (Const)   | VAR     | 变量  (Variables)   |
| EXP     | 表达式 (Expression) | CTF     | 控制流程 (Control Flow) |
| RFE     | 引用 (Reference)  | PTR     | 指针  (Pointer)   |
| STR     | 字符串 (String) | INT     | 整数 (Integer)    |
| MOD     | 模块  (Module) | CAR     | 包管理  (Cargo) |
| MEM     | 内存 (Memory)  | FUD     | 函数设计 (Function Design) |
| MAC     | 宏  (Macro) | STV     | 静态变量 (Static Variables) |
| GEN    | 泛型 (Generic)  | TRA     | 特质 (Trait) |
| ASY    | 异步 (Async)  | UNS | 非安全 (Unsafe Rust) |
| SAS | 安全抽象 (Safety Abstract) | FFI | 外部函数调用接口 ( Foreign Function Interface ) |
| LAY | 内存布局 (Layout) | ERR | 错误处理 (Error Handle) |
| CLT | 集合  (Collection)  | MTH | 多线程 (Multi Threads) |
| EMB | 嵌入式Rust (Embedded Rust) | FIO      | 输入输出 (In/Out)     |
| SEC | 信息安全 (Security) | SPT | 智能指针 (Smart Pointer) |
| UNT | 单元类型 (Unit) | BOL | 布尔 (Bool) |  
| CHR | 字符类型 (Char) |  FLT | 浮点数 (Float) |  
| SLC | 切片类型 (Slice) |  TUP | 元组 (Tuple) |  
| ARR | 固定长度数组类型 (Array) |  VEC  | 动态长度数组 (Vector)  |  
| SCT | 结构体 (Struct) |  ENM  | 枚举体 (Enum)  |  
| UNI|  联合体 (Union) |   BLN | 标准库内置（BuiltIn）  |  
| OBJ | Trait 对象 (Trait Object)| LFT| 生命周期 (Lifetime) |  
| BOX | `Box<T>` 类型 | DRP  |  析构函数 (Drop) |  
| DCL | 声明宏 (Declarative)  | PRO  |  过程宏 (Procedural) |  
| LCK | 锁同步 (Lock)  | LKF |  无锁 (Lock Free) |  
|  |  | OTH | 其他 (Ohters) |  


## 引用代码开源许可证说明

本规范中引用外部代码，均满足 `MIT/Apache/Mozilla public licenses` 开源许可证！

## 特别鸣谢

本指南参考《华为 C 语言编程指南 V 1.0》，感谢华为 开源能力中心 提供编程指南规范协助！



