## G.TYP.SCT.03 宜使用结构体功能更新语法来提升代码可读性

**【级别】** 建议

**【描述】**

更改结构体最好提供结构体方法进行更改，而不是直接构建结构体内容。

但是需要多次修改结构体内容的时候，宜使用结构体更新语法来提升代码可读性。

**【反例】**

```rust
// 不符合
let mut a: A = Default::default();
a.i = 42;
```

**【正例】**

```rust
// 符合
let a = A {
    i: 42,
    .. Default::default()
};
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [field_reassign_with_default](https://rust-lang.github.io/rust-clippy/master/#field_reassign_with_default) | yes           | no           | style      | warn  |


