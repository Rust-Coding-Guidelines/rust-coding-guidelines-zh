# 格式

Rust 有自动化格式化工具 rustfmt ，可以帮助开发者摆脱手工调整代码格式的工作，提升生产力。但是基本的格式原则和规范，作为开发者应该去了解。

## P.03 始终使用 rustfmt 进行自动格式化代码

### 【描述】

应该总是在项目中添加 `rustfmt.toml` 文件，即使它是空文件。这是向潜在的合作者表明你希望代码是自动格式化的。

### 【例外】

在特殊的情况下，可以通过条件编译属性 `#[cfg_attr(rustfmt, rustfmt_skip)]` 或 `#[rustfmt::skip]` 来关闭自动格式化。

比如下面示例：

`vec!` 中的元素排布是固定格式，这样有助于开发的便利。

```rust
fn main() {
    let got = vec![
    		0x00, 0x05, 0x01, 0x00,
    		0xff,
    		0x00,
    		0x00,
    
    		0x01, 0x0c, 0x02, 0x00,
    		0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
    		b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
    		0x00,
    
    		127, 0x06, 0x03, 0x00,
    		0x01, 0x02,
    		b'a', b'b', b'c', b'd', 0x00,
    		b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}
```

如果使用 自动格式化，会变成：

```rust
fn main() {
    let got = vec![
        0x00, 0x05, 0x01, 0x00, 0xff, 0x00, 0x00, 0x01, 0x0c, 0x02, 0x00, 0xde, 0xad, 0xbe, 0xef,
        0xde, 0xad, 0xbe, 0xef, b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00, 0x00, 127,
        0x06, 0x03, 0x00, 0x01, 0x02, b'a', b'b', b'c', b'd', 0x00, b'1', b'2', b'3', b'4', 0x00,
        0x00,
    ];
}
```

但是加上 `#[rustfmt::skip]` 就不会被自动格式化影响：

```rust
fn main() {
    #[rustfmt::skip] 
    let got = vec![
    		0x00, 0x05, 0x01, 0x00,
    		0xff,
    		0x00,
    		0x00,
    
    		0x01, 0x0c, 0x02, 0x00,
    		0xde, 0xad, 0xbe, 0xef, 0xde, 0xad, 0xbe, 0xef,
    		b'd', b'e', b'a', b'd', b'b', b'e', b'e', b'f', 0x00,
    		0x00,
    
    		127, 0x06, 0x03, 0x00,
    		0x01, 0x02,
    		b'a', b'b', b'c', b'd', 0x00,
    		b'1', b'2', b'3', b'4', 0x00,
            0x00,
    ];
}

```

## G.FMT.01 缩进始终使用空格（space）而非制表符（tab）

### 【级别：必须】

必须严格按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 默认值 | 是否 stable | 说明|
| ------ | ---- | ---- | ---- | 
| [`tab_spaces`](https://rust-lang.github.io/rustfmt/#tab_spaces) | 4| yes| 缩进空格数｜
|[`hard_tabs`](https://rust-lang.github.io/rustfmt/#hard_tabs)| false| yes| 禁止使用tab缩进｜

### 【描述】

1. 缩进要使用 四个 空格，不要使用制表符（`\t`）代替。
2. 通过 IDE/Editor 为缩进默认好设置值。

## G.FMT.02 每行最大宽度为 100 个字符

### 【级别：建议】

建议按此规范执行。

### 【rustfmt 配置】

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 默认值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`max_width`](https://rust-lang.github.io/rustfmt/?#max_width) | 100 | yes| 行最大宽度默认值|
|[`error_on_line_overflow`](https://rust-lang.github.io/rustfmt/?#error_on_line_overflow)| false| No (tracking issue: #3391)| 如果超过最大行宽设置则报错|

### 【描述】

代码行宽不宜过长，否则不利于阅读。
建议每行字符数不要超过 100 个字符。

