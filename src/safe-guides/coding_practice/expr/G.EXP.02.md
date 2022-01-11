## G.EXP.02  不宜在比较中使用不兼容的位掩码

**【级别】** 建议

**【描述】**

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
if (x & 1 == 2) { }
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bad_bit_mask](https://rust-lang.github.io/rust-clippy/master/#bad_bit_mask) | yes| no | correctness | deny |


