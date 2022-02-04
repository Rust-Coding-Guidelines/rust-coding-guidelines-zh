## G.TYP.BOL.05 非必要时，布尔运算应使用逻辑运算符( `&&/||`)而非位运算符 (`&/|`)

**【级别】** 建议

**【描述】**

位运算不支持短路（short-circuiting），所以会影响性能。逻辑运算符则支持短路。

**【反例】**

```rust
let (x,y) = (true, false);
if x & !y {} //  位运算符，不支持短路
```

**【正例】**

```rust
let (x,y) = (true, false);
if x && !y {} //  逻辑运算符，支持短路
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [needless_bitwise_bool](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool) | yes           | no           | pedantic   | allow |


