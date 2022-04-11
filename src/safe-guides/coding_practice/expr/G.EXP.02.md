## G.EXP.02  不宜在比较中使用不兼容的位掩码

**【级别】** 要求

**【描述】**

如果比较的位总是被位掩码设置为零或一，则比较是常量true或 false（取决于掩码、比较值和运算符），这种代码是有误导性的，可能是故意这么写用于赢得一场性能竞赛或者是通过一个测试用例。

可以对照下面表格进行检查。

|Comparison	| Bit Op |Example	| is always	| Formula |
| ------ | ---- | --------- | ------ | ------ | 
|== or != |	& | x & 2 == 3 | false | c & m != c|
|< or >= |	& | x & 2 < 3 | true | m < c|
|> or <= |	& | x & 1 > 1 | false | m <= c|
|== or != |	&#124; | x &#124; 1 == 0 | false | c &#124; m != c|
|< or >= |	&#124; | x &#124; 1 < 1 | false | m >= c|
|<= or > |	&#124; | x &#124; 1 > 0 | true | m > c|

**【反例】**

```rust
let x = 2;
// 不符合：该表达式会永远是 false
if (x & 1 == 2) { }
```

**【正例】**

```rust
let x = 2;
// 符合
if (x == 2) { }
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bad_bit_mask](https://rust-lang.github.io/rust-clippy/master/#bad_bit_mask) | yes| no | correctness | deny |


