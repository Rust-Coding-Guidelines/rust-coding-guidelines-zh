## G.UNS.PTR.04  建议使用 `NonNull<T>` 来替代 `*mut T`

**【级别】** 建议

**【描述】**

尽量使用 [`NonNull`](https://doc.rust-lang.org/stable/std/ptr/struct.NonNull.html) 来包装 `*mut T`。

`NonNull` 的优势：

1. 非空指针。会自动检查包装的指针是否为空。
2. 协变。方便安全抽象。如果用裸指针，则需要配合 `PhantomData`类型来保证协变。

**【正例】**

```rust
use std::ptr::NonNull;

let mut x = 0u32;
let ptr = NonNull::<u32>::new(&mut x as *mut _).expect("ptr is null!");

if let Some(ptr) = NonNull::<u32>::new(std::ptr::null_mut()) {
    unreachable!();
}
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| --------- | ------------- | ------------ | ---------- | ---------- |
| _         | no            | no           | _          | yes        |

【定制参考】

检测到包含 `*mut T`类型的结构体，应该给予开发者警告或建议去使用 `NonNull` 。
