## G.MOD.01   使用导入模块中的类型或函数，在某些情况下需要带 模块名前缀

**【级别】** 建议

**【描述】**

对于标准库中，很多人都熟知的类型 ，比如 `Arc`/ `Rc`/ `Cell`/ `HashMap` 等 ， 可以导入它们直接使用。

但是对于可能引起困惑的函数，比如 `std::ptr::replace` 和 `std::mem::replace` ，在使用它们的时候，就必须得带上模块前缀。

使用一些第三方库中定义的类型或函数，也建议带上crate或模块前缀。如果太长的话，可以考虑使用 `as`  或  `type` 来定义别名。

以上考虑都是为了增强代码的可读性、可维护性。

**【正例】**

```rust
use std::sync::Arc;
let foo = Arc::new(vec![1.0, 2.0, 3.0]); // 直接使用 Arc
let a = foo.clone();

// 需要带上 ptr 前缀
use std::ptr;
let mut rust = vec!['b', 'u', 's', 't'];
// `mem::replace` would have the same effect without requiring the unsafe
// block.
let b = unsafe {
    ptr::replace(&mut rust[0], 'r')
};
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制化参考】

可以检测外部模块导入的自定义的类型是否带模块前缀，给予建议。
