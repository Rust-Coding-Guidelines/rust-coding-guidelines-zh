## G.TYP.ENM.06  Enum内变体的大小差异不宜过大

**【级别】** 建议

**【描述】**

要注意 Enum 内变体的大小差异不要过大，因为 Enum 内存布局是以最大的变体进行对齐。根据场景，如果该Enum 实例中小尺寸变体的实例使用很多的话，内存就会有所浪费。如果小尺寸变体的实例使用很少，则影响不大。

解决办法就是把大尺寸变体包到 `Box<T>`中。

**【反例】**

```rust
enum Test {
    A(i32),
    B([i32; 1000]),
    C([i32; 8000]),
}
```

**【正例】**

```rust
enum Test {
    A(i32),
    B(Box<[i32; 1000]>),
    C(Box<[i32; 8000]>),
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_enum_variant](https://rust-lang.github.io/rust-clippy/master/#large_enum_variant) | yes           | no           | perf       | warn  |

该 lint 可以通过 clippy 配置项 `enum-variant-size-threshold = 200` 来配置，默认是 `200` 字节。

