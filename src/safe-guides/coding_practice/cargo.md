# 3.15 包管理

Cargo 不仅仅是包管理，它还是一个 Workflow 工具。这一节包含 Cargo 和 Crate 相关内容。

## 列表

- [P.CAR.01 应该尽量把项目划分为合理的 crate 组合](./cargo/P.CAR.01.md)
- [P.CAR.02 不要滥用 Features](./cargo/P.CAR.02.md)
- [P.CAR.03 使用 `cargo features` 来代替 `--cfg` 条件编译参数](./cargo/P.CAR.03.md)
- [P.CAR.04 如果可能的话，使用 `cfg!` 来代替 `#[cfg]`](./cargo/P.CAR.04.md)
- [G.CAR.01 当项目是可执行程序而非库时，建议使用 `src/main.rs` 和 `src/lib.rs` 模式](./cargo/G.CAR.01.md)
- [G.CAR.02 Crate 的 `Cargo.toml` 中应该包含必要的元信息](./cargo/G.CAR.02.md)
- [G.CAR.03 Feature 命名应该避免否定式或多余的前后缀](./cargo/G.CAR.03.md)
- [G.CAR.04 `Cargo.toml` 中依赖包版本不应使用通配符](./cargo/G.CAR.04.md)