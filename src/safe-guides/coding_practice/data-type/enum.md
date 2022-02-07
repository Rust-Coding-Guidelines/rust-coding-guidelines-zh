# 枚举体

Rust 的枚举是一种带 Tag 的联合体。 一般分为三类：空枚举、无字段（fieldless）枚举和数据承载（data carrying）枚举。

## 列表

- [G.TYP.ENM.01 合理选择`map`和`and_then`](./enum/G.TYP.ENM.01.md)
- [G.TYP.ENM.02 非必要时不应自行创建空枚举](./enum/G.TYP.ENM.02.md)
- [G.TYP.ENM.03 在使用类似 C 语言的枚举写法且使用`repr(isize/usize)` 布局时注意 32位架构上截断的问题](./enum/G.TYP.ENM.03.md)
- [G.TYP.ENM.04 不宜在`use`语句中引入Enum的全部变体（variants）](./enum/G.TYP.ENM.04.md)
- [G.TYP.ENM.05 对外导出的公开Enum，宜添加`#[non_exhaustive]`属性](./enum/G.TYP.ENM.05.md)
- [G.TYP.ENM.06 Enum内变体的大小差异不宜过大](./enum/G.TYP.ENM.06.md)
