# 控制流程

Rust中流程控制也是属于表达式，但在本规范中将其独立出来。

## 列表

- [P.CTF.01 不要过度使用迭代器](./control-flow/P.CTF.01.md)
- [P.CTF.02 优先使用模式匹配而不是判断后再取值](./control-flow/P.CTF.02.md)
- [G.CTF.01 当需要通过多个if判断来比较大小来区分不同情况时，优先使用match和cmp来代替if表达式](./control-flow/G.CTF.01.md)
- [G.CTF.02 if条件表达式分支中如果包含了else if分支也应该包含else分支](./control-flow/G.CTF.02.md)
- [G.CTF.03 如果要通过 if 条件表达式来判断是否panic，请优先使用断言](./control-flow/G.CTF.03.md)
- [G.CTF.04 在Match分支的Guard语句中不要使用带有副作用的条件表达式](./control-flow/G.CTF.04.md)