## G.NAM.09  变量的命名中不需要添加类型标识
### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | 是否可定制 |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| _ | no           | no           | _   | yes |

【定制化参考】
这条规则如果需要定制Lint，则可以获取变量命名的结尾部分和变量类型，进行匹配，判断是否重复。

### 【描述】

因为 Rust 语言类型系统崇尚显式的哲学，所以不需要在变量命名中也添加关于类型的标识。

【正例】

```rust
let account: Vec<u8> = read_some_input();   // account 的类型很清楚
let account = String::from_utf8(account)?;  // account 的类型很清楚
let account: Account = account.parse()?;   // account 的类型很清楚
```

【反例】

```rust
let account_bytes: Vec<u8> = read_some_input();   // account 的类型很清楚，没必要在命名中加 `_bytes`
let account_str = String::from_utf8(account_bytes)?; // account 的类型很清楚，没必要在命名中加 `_str`
let account: Account = account_str.parse()?;   // account 的类型很清楚，没必要在命名中加 `_str`
```

---
