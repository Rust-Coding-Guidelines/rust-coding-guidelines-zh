## G.TRA.Buitin.07   对实现 `Copy` 的可迭代类型来说，要通过迭代器拷贝其所有元素时，应该使用 `copied`方法，而非`cloned`

**【级别】** 建议

**【描述】**

`copied` 方法在语义层面，是针对实现 `Copy` 的类型，所以应该使用 `copied` 来增加代码可读性。

**【反例】**

```rust
let a = [1, 2, 3];

let v_copied: Vec<_> = a.iter().cloned().collect();
```

**【正例】**

```rust
let a = [1, 2, 3];

let v_copied: Vec<_> = a.iter().copied().collect();
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cloned_instead_of_copied](https://rust-lang.github.io/rust-clippy/master/#cloned_instead_of_copied) | yes           | no           | pedantic   | allow |
