# 表达式

Rust 中几乎一切皆表达式。

---

## P.EXP.01    

【描述】



---


## G.EXP.01 当需要对表达式求值之后重新赋值时尽量使用复合赋值模式

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [assign_op_pattern](https://rust-lang.github.io/rust-clippy/master/#assign_op_pattern) | yes| no | style | warn |

### 【描述】

【正例】

```rust
let mut a = 5;
let b = 0;
a += b;
```

【反例】

```rust
let mut a = 5;
let b = 0;
a = a + b;
```


## G.EXP.02  避免在比较中使用不兼容的位掩码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bad_bit_mask](https://rust-lang.github.io/rust-clippy/master/#bad_bit_mask) | yes| no | correctness | deny |

### 【描述】

可以对照下面表格进行检查。

|Comparison	| Bit Op |Example	| is always	| Formula |
| ------ | ---- | --------- | ------ | ------ | 
|== or != |	& | x & 2 == 3 | false | c & m != c|
|< or >= |	& | x & 2 < 3 | true | m < c|
|> or <= |	& | x & 1 > 1 | false | m <= c|
|== or != |	| | x | 1 == 0 | false | c | m != c|
|< or >= |	| | x | 1 < 1 | false | m >= c|
|<= or > |	| | x | 1 > 0 | true | m > c|

## G.EXP.03   不要使用子表达式调用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [unnecessary_operation](https://rust-lang.github.io/rust-clippy/master/#unnecessary_operation) | yes           | no           | **complexity** | warn  |

### 【描述】

这样会影响代码可读性。

【正例】

```rust
let arr = compute_array();
let first = arr[0];
```

【反例】

```rust
let first = compute_array()[0];
```

