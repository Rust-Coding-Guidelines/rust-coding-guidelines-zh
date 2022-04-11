## G.TYP.BOL.04 禁止在if表达式条件中使用块结构

**【级别】** 要求

**【描述】**

为了增加可读性。

**【反例】**

```rust
// 不符合
if { true } { /* ... */ }

# fn somefunc() -> bool { true };
// 不符合
if { let x = somefunc(); x } { /* ... */ }
```

**【正例】**

```rust
// 符合
if true { /* ... */ }

# fn somefunc() -> bool { true };
let res = { let x = somefunc(); x };
// 符合
if res { /* ... */ }
```

**【Lint 检测】**

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [blocks_in_if_conditions  ](https://rust-lang.github.io/rust-clippy/master/#blocks_in_if_conditions  ) | yes| no | style | warn |


