# 结构体

Rust 包含了三种结构体： 命名结构体、元组结构体、单元结构体。

## 列表

- [P.TYP.SCT.01 为结构体实现构造性方法时，避免构造后再初始化的情况](./struct/P.TYP.SCT.01.md)
- [P.TYP.SCT.02 结构体实例需要默认实现时，宜使用Default特质](./struct/P.TYP.SCT.02.md)
- [G.TYP.SCT.01 对外导出的公开的 Struct，宜添加`#[non_exhaustive]`属性](./struct/G.TYP.SCT.01.md)
- [G.TYP.SCT.02 当结构体中有超过三个布尔类型的字段，宜将其独立为一个枚举](./struct/G.TYP.SCT.02.md)
- [G.TYP.SCT.03 宜使用结构体功能更新语法来提升代码可读性](./struct/G.TYP.SCT.03.md)