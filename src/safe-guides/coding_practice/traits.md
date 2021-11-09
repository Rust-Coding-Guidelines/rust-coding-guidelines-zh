# 特质

特质就是指 trait。在 Rust 中， trait 不是具体类型，而是一种抽象接口。但是通过 `impl Trait` 和 `dyn Trait` 也可以将 trait 作为类型使用。

- [内置trait](./trait/std-buildin.md)
- [trait 对象](./traits/trait-object.md)

---

## G.TRA.01  使用 trait 时要注意 trait 一致性规则

### 【级别：规则】

按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | yes          | _          | _          |

### **【描述】**

使用 trait 的时候，必须要满足 trait 一致性规则，即，**孤儿规则（orphans rule）**：类型和trait，必须有一个是在本地crate内定义的。

