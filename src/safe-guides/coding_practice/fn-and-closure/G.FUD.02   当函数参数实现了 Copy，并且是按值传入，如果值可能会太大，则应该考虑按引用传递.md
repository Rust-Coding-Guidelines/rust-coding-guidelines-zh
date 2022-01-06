## G.FUD.02   当函数参数实现了 Copy，并且是按值传入，如果值可能会太大，则宜考虑按引用传递

**【级别】** 建议

**【描述】**

通过值传递的参数可能会导致不必要的 `memcpy` 拷贝，这可能会造成性能损失。

**【反例】**

```rust
#[derive(Clone, Copy)]
struct TooLarge([u8; 2048]);

// Bad
fn foo(v: TooLarge) {}
```

**【正例】**

```rust
#[derive(Clone, Copy)]
struct TooLarge([u8; 2048]);

// Good
fn foo(v: &TooLarge) {}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_types_passed_by_value](https://rust-lang.github.io/rust-clippy/master/#large_types_passed_by_value) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api=true 
```