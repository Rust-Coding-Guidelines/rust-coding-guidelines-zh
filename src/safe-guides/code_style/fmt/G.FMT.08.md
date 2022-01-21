## G.FMT.08  换行样式以文件自动检测为主

**【级别】** 建议

**【描述】**

换行样式是基于每个文件自动检测的。 具有混合行尾的文件将转换为第一个检测到的行尾样式。

不同平台换行符不同：

- `Windows` 以 `\r\n`结尾。
- `Unix` 以 `\n` 结尾。

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`newline_style`](https://rust-lang.github.io/rustfmt/?#newline_style) | Auto（默认） | Yes| 换行样式以文件自动检测为主 |