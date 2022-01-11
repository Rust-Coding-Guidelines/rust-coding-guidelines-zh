## G.VAR.01  交换两个变量的值应使用 `swap` 而非赋值

**【级别】** 建议

**【描述】**

对于包含 `swap` 方法的类型，如 `ptr`、`slice`、`Cell`、`RefCell`、`VecDeque` 等建议使用该类型的 `swap` 方法进行交换。

对其他类型可以使用函数 `std::mem::swap` 进行变量值的交换。

**【反例】**

```rust
let mut a = 1;
let mut b = 2;
let mut c = 0; // 辅助交换的变量
c = a;
a = b;
b = c;  
```

**【正例】**

```rust
let mut a = 1;
let mut b = 2;
std::mem::swap(&mut a, &mut b);
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

**【定制化参考】**

这条规则如果需要定制Lint，则可以检测变量赋值操作，识别交换语义，推荐用户使用 `swap` 函数。

