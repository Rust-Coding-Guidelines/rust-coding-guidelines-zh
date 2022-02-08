# 3.11 特质

特质就是指 trait。在 Rust 中， trait 不是具体类型，而是一种抽象接口。但是通过 `impl Trait` 和 `dyn Trait` 也可以将 trait 作为类型使用。

## 列表

- [P.TRA.01 使用 trait 时要注意 trait 一致性规则](./traits/P.TRA.01.md)
- [标准库内置 trait](./traits/std-builtin.md)
    - [P.TRA.BLN.01 在实现`Borrow`特质时，需要注意一致性](./traits/std-builtin/P.TRA.BLN.01.md)
    - [G.TRA.BLN.01 应该具体类型的 `default()` 方法代替 `Default::default()` 调用](./traits/std-builtin/G.TRA.BLN.01.md)
    - [G.TRA.BLN.02 不要为迭代器实现Copy特质](./traits/std-builtin/G.TRA.BLN.02.md)
    - [G.TRA.BLN.03 能使用派生宏（Derive）自动实现Default特质就不要用手工实现](./traits/std-builtin/G.TRA.BLN.03.md)
    - [G.TRA.BLN.04 在使用`#[derive(Hash)]`的时候，避免再手工实现`PartialEq`](./traits/std-builtin/G.TRA.BLN.04.md)
    - [G.TRA.BLN.05 在使用`#[derive(Ord)]` 的时候，避免再手工实现 `PartialOrd`](./traits/std-builtin/G.TRA.BLN.05.md)
    - [G.TRA.BLN.06 不要对实现 `Copy` 或引用类型调用 `std::mem::drop` 和 `std::mem::forgot`](./traits/std-builtin/G.TRA.BLN.06.md)
    - [G.TRA.BLN.07 对实现 `Copy` 的可迭代类型来说，要通过迭代器拷贝其所有元素时，应该使用 `copied`方法，而非`cloned`](./traits/std-builtin/G.TRA.BLN.07.md)
    - [G.TRA.BLN.08 实现 `From` 而不是 `Into`](./traits/std-builtin/G.TRA.BLN.08.md)
    - [G.TRA.BLN.09 一般情况下不要给 `Copy` 类型手工实现 `Clone`](./traits/std-builtin/G.TRA.BLN.09.md)
    - [G.TRA.BLN.10 不要随便使用Deref特质来模拟继承](./traits/std-builtin/G.TRA.BLN.10.md)
- [trait 对象](./traits/trait-object.md)
    - [P.TRA.OBJ.01 根据场景合理选择使用trait对象或泛型静态分发](./traits/trait-object/P.TRA.OBJ.01.md)
    - [P.TRA.OBJ.02 除非必要，避免自定义虚表](./traits/trait-object/P.TRA.OBJ.02.md)