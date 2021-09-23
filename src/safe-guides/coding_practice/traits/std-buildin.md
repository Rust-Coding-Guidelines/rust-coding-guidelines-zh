# 内置 trait

Rust 标准库内置了很多 trait，在使用这些 trait 的时候也需要注意。

---

## G.TRA.Buitin.01   应该具体类型的 `default()` 方法代替 ` Default::default()` 调用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [default_trait_access](https://rust-lang.github.io/rust-clippy/master/#default_trait_access) | yes           | no           | **pedantic** | allow |

### 【描述】

为了增强可读性。

【正例】

```rust
let s = String::default();
```

【反例】

```rust
let s: String = Default::default();
```

