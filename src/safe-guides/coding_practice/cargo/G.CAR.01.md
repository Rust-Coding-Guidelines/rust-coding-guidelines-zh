## G.CAR.01 当项目是可执行程序而非库时，建议使用 `src/main.rs` 和 `src/lib.rs` 模式

**【级别】** 建议

**【描述】**

`crate` 结构类似于：

```text
src/
  -- lib.rs
  -- main.rs
```

或

```text
src/
  -- lib.rs
bin/
  -- main.rs
```

这样的好处有：

1. 便于单元测试。
2. 有利于面向接口思考，让代码架构和逻辑更加清晰。

若编写的可执行程序比较复杂，在 `main.rs` 里需要依赖太多东西时，那就需要创建 Workspace 把 `main.rs` 独立为一个 crate，而在这个 crate 内也没有必要再拆分为 `main` 和 `lib` 了。
