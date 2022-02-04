# 内置 trait

Rust 标准库内置了很多 trait，在使用这些 trait 的时候也需要注意。

## 列表

- [P.TRA.BLN.01 在实现`Borrow`特质时，需要注意一致性](./std-builtin/P.TRA.BLN.01.md)
- [G.TRA.BLN.01 应该具体类型的 `default()` 方法代替 `Default::default()` 调用](./std-builtin/G.TRA.BLN.01.md)
- [G.TRA.BLN.02 不要为迭代器实现Copy特质](./std-builtin/G.TRA.BLN.02.md)
- [G.TRA.BLN.03 能使用派生宏（Derive）自动实现Default特质就不要用手工实现](./std-builtin/G.TRA.BLN.03.md)
- [G.TRA.BLN.04 在使用`#[derive(Hash)]`的时候，避免再手工实现`PartialEq`](./std-builtin/G.TRA.BLN.04.md)
- [G.TRA.BLN.05 在使用`#[derive(Ord)]` 的时候，避免再手工实现 `PartialOrd`](./std-builtin/G.TRA.BLN.05.md)
- [G.TRA.BLN.06 不要对实现 `Copy` 或引用类型调用 `std::mem::drop` 和 `std::mem::forgot`](./std-builtin/G.TRA.BLN.06.md)
- [G.TRA.BLN.07 对实现 `Copy` 的可迭代类型来说，要通过迭代器拷贝其所有元素时，应该使用 `copied`方法，而非`cloned`](./std-builtin/G.TRA.BLN.07.md)
- [G.TRA.BLN.08 实现 `From` 而不是 `Into`](./std-builtin/G.TRA.BLN.08.md)
- [G.TRA.BLN.09 一般情况下不要给 `Copy` 类型手工实现 `Clone`](./std-builtin/G.TRA.BLN.09.md)
- [G.TRA.BLN.10 不要随便使用Deref特质来模拟继承](./std-builtin/G.TRA.BLN.10.md)