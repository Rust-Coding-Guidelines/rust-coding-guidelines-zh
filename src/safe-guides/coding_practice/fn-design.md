# 函数设计

创建函数或使用闭包时需要注意的地方。

## 列表

- [P.FUD.01 传递到闭包的变量建议单独重新绑定](./fn-design/P.FUD.01.md)
- [P.FUD.02 函数返回值不要使用 return](./fn-design/P.FUD.02.md)
- [G.FUD.01 函数参数最长不要超过五个](./fn-design/G.FUD.01.md)
- [G.FUD.02 当函数参数实现了 Copy，并且是按值传入，如果值可能会太大，则宜考虑按引用传递](./fn-design/G.FUD.02.md)
- [G.FUD.03 当函数参数出现太多 bool 类型的参数时，应该考虑将其封装为自定义的结构体或枚举](./fn-design/G.FUD.03.md)
- [G.FUD.04 当Copy 类型的足够小的值作为函数参数时，应该按值（by-value）传入，而不是引用(by-ref)](./fn-design/G.FUD.04.md)
- [G.FUD.05 不要为函数指定 inline(always)](./fn-design/G.FUD.05.md)
- [G.FUD.06 函数参数建议使用借用类型](./fn-design/G.FUD.06.md)