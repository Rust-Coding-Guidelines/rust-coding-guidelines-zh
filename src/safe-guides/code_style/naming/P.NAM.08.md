## P.NAM.08  避免在变量的命名中添加类型标识

**【描述】**

因为 Rust 语言类型系统崇尚显式的哲学，所以不需要在变量命名中也添加关于类型的标识。

**【反例】**

```rust
let account_bytes: Vec<u8> = read_some_input();   // 不符合：account 的类型很清楚，没必要在命名中加 `_bytes`
let account_str = String::from_utf8(account_bytes)?; // 不符合：account 的类型很清楚，没必要在命名中加 `_str`
let account: Account = account_str.parse()?;   // 不符合：account 的类型很清楚，没必要在命名中加 `_str`
```

**【正例】**

```rust
let account: Vec<u8> = read_some_input();   // 符合
let account = String::from_utf8(account)?;  // 符合
let account: Account = account.parse()?;   // 符合
```
