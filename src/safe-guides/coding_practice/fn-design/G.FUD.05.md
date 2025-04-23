## G.FUD.05   不要总是为函数指定 `inline(always)` 

**【级别】** 建议

**【描述】**

`inline` 虽然可以提升性能，但也会增加编译时间和编译大小。

Rust 中性能、编译时间和编译大小之间需要权衡。根据需要再 `inline` 即可。

**【反例】**

```rust
#![warn(clippy::inline_always)]

// 不符合
#[inline(always)]
fn not_quite_hot_code(..) { ... }
```

**【例外】**

根据需要再inline即可，比如明确知道某个函数被调用次数非常频繁，这个时候为了性能考虑要为其手工指定内联。

```rust
// 符合：实现内存回收功能，调用非常频繁。性能优先。
#[inline(always)]
pub fn buf_recycle(buf_id: usize) {
    // ...
}
```

**【Lint 检测】**

| lint name                                                                      | Clippy 可检测 | Rustc 可检测 | Lint Group | 默认level |
| ------------------------------------------------------------------------------ | ------------- | ------------ | ---------- | --------- |
| [inline_always](https://rust-lang.github.io/rust-clippy/master/#inline_always) | yes           | no           | pedantic   | allow     |

