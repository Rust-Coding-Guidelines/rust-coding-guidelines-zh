## G.TRA.BLN.06   不要对实现 `Copy` 或引用类型调用 `std::mem::drop` 和 `std::mem::forgot`

**【级别】** 要求

**【描述】**

`std::mem::drop` 函数只是利用 Rust 所有权的一个技巧，对于 实现了 Copy 的类型或引用，是无效的。如果使用它，对导致代码可读方便产生误导作用。

另外`std::mem::drop` 也无法 Drop 掉 `ManuallyDrop` 类型。

`std::mem::forgot`   同理。

但是也存在例外的情况。

**【反例】**

```rust
// 不符合
let x: i32 = 42; // i32 implements Copy
std::mem::drop(x) // A copy of x is passed to the function, leaving the
                  // original unaffected
```

**【例外】**

在某些情况下，虽然不会有实际效果，但是为了提升语义，也可以使用。

下面代码中，为了防止自引用的问题，使用 `drop(self)` ，提升了代码语义，但实际并不会 drop。

```rust
// From: https://docs.rs/crate/dhall/0.10.1/source/src/error/builder.rs

#[allow(clippy::drop_ref)]
pub fn format(&mut self) -> String {
    if self.consumed {
        panic!("tried to format the same ErrorBuilder twice")
    }
    let this = std::mem::take(self);
    self.consumed = true;
    drop(self); // 显式 drop self，避免后面误用它
    // ...
}
```

**【Lint 检测】**

| lint name                                                                                            | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| ---------------------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [drop_copy](https://rust-lang.github.io/rust-clippy/master/#drop_copy)                               | yes           | no           | correctness | deny      |
| [drop_ref](https://rust-lang.github.io/rust-clippy/master/#drop_ref)                                 | yes           | no           | correctness | deny      |
| [forget_copy](https://rust-lang.github.io/rust-clippy/master/#forget_copy)                           | yes           | no           | correctness | deny      |
| [forget_ref](https://rust-lang.github.io/rust-clippy/master/#forget_ref)                             | yes           | no           | correctness | deny      |
| [undropped_manually_drops](https://rust-lang.github.io/rust-clippy/master/#undropped_manually_drops) | yes           | no           | correctness | deny      |





