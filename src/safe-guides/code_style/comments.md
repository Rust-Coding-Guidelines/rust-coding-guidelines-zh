# 2.3 注释与文档

在 Rust 中，注释分为两类：普通注释和文档注释。普通注释使用 `//` 或 `/* ... */`，文档注释使用 `///`、`//!` 或 `/** ... **/`。

在原则和规则中提到「注释」时，包括普通注释和文档注释。当提到「文档」时，特指文档注释。


## 列表

- [P.CMT.01 代码能做到自注释，文档要干练简洁](./comments/P.CMT.01.md)
- [G.CMT.01 注释应该有一定宽度限制](./comments/G.CMT.01.md)
- [G.CMT.02 使用行注释而避免使用块注释](./comments/G.CMT.02.md)
- [G.CMT.03 文件头注释包含版权说明](./comments/G.CMT.03.md)
- [G.CMT.04 在注释中使用 FIXME 和 TODO 来帮助任务协作](./comments/G.CMT.04.md)
- [G.CMT.05 在公开的返回Result类型返回的函数文档中增加 Error 注释](./comments/G.CMT.05.md)
- [G.CMT.06 如果公开的API在某些情况下会发生Panic，则相应文档中需增加 Panic 注释](./comments/G.CMT.06.md)
- [G.CMT.07 在文档注释中要使用空格代替 tab](./comments/G.CMT.07.md)


## 参考

1. [RFC 505: API 注释约定](https://github.com/rust-lang/rfcs/blob/master/text/0505-api-comment-conventions.md)
2. [RFC 1574: API 文档约定](https://github.com/rust-lang/rfcs/blob/master/text/1574-more-api-documentation-conventions.md)
3. [Making Great Docs with Rustdoc](https://www.tangramvision.com/blog/making-great-docs-with-rustdoc)
4. [Rust Doc book](https://doc.rust-lang.org/rustdoc/what-is-rustdoc.html)

