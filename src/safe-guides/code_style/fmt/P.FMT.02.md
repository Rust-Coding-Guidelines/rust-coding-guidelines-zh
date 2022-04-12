## P.FMT.02  缩进使用空格而非制表符

**【描述】**

缩进要使用四个空格，不要使用制表符（`\t`）代替。可以通过 IDE 或编辑器把缩进设置为四个空格。

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明|
| ------ | ---- | ---- | ---- | 
| [`tab_spaces`](https://rust-lang.github.io/rustfmt/#tab_spaces) | 4| yes（默认）| 缩进空格数｜
|[`hard_tabs`](https://rust-lang.github.io/rustfmt/#hard_tabs)| false| yes（默认）| 禁止使用tab缩进｜