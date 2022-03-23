## P.FMT.02  缩进始终使用空格（space）而非制表符（tab）


**【描述】**

1. 缩进要使用 四个 空格，不要使用制表符（`\t`）代替。
2. 通过 IDE/Editor 为缩进默认好设置值。

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明|
| ------ | ---- | ---- | ---- | 
| [`tab_spaces`](https://rust-lang.github.io/rustfmt/#tab_spaces) | 4| yes（默认）| 缩进空格数｜
|[`hard_tabs`](https://rust-lang.github.io/rustfmt/#hard_tabs)| false| yes（默认）| 禁止使用tab缩进｜