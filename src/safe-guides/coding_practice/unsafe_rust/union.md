# 联合体（Union）

Union 是没有 tag 的 Enum，Enum 是有 tag 的Union 。

内存布局 Union 和 Enum 相似。

正因为没有 tag，Rust 编译器无法检查当前使用的正是哪个变体，所以，访问 Union 的变体是 Unsafe 的。

## 列表

- [G.UNS.UNI.01 除了与 C 交互，尽量不要使用 Union](./union/G.UNS.UNI.01.md)
- [G.UNS.UNI.02 不要把联合体的不同变体用在不同生命周期内](./union/G.UNS.UNI.02.md)
