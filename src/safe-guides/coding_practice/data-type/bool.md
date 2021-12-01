# 布尔

---
<!-- toc -->
---

## P.TYP.Bool.01  不应使用数字来代替布尔值

**【描述】**

Rust 中布尔值就是 `true`  和 `false`。 不要试图使用数字 `1` 和 `0` 来代替布尔值。

虽然 布尔值 可以强转为 对应的数字，但是反之则不行。

不要通过判断数字来代替 布尔值，除非是 FFi 场景通过 C-ABI 和其他语言打交道。

【反例】

```rust
let a = 1;
let b = 0;
assert_eq!(true, a == 1);  
assert_eq!(false, b == 0);
```

【正例】

```rust
let a = true;
let b = false;
assert_eq!(1, a as u32);
assert_eq!(0, b as u32);
```

---

## G.TYP.Bool.01 返回为布尔值的表达式或函数值不应和布尔字面量进行比较

**【级别】** 要求

**【描述】**

在 Rust 中，返回为布尔值的表达式或函数值可以直接当作布尔值使用。

总之，使用布尔表达式的时候，要尽可能地简洁明了。

【反例】

```rust
if x == true {}
if y == false {}

assert_eq!("a".is_empty(), false);
assert_ne!("a".is_empty(), true);
```

【正例】

```rust
if x {}
if !y {}

assert!(!"a".is_empty());
```

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bool_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_comparison ) | yes| no | complexity | warn |
| [bool_assert_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_assert_comparison ) | yes| no | style | warn |
| [needless_bool ](https://rust-lang.github.io/rust-clippy/master/#needless_bool ) | yes| no | complexity | warn |
| [nonminimal_bool  ](https://rust-lang.github.io/rust-clippy/master/#nonminimal_bool  ) | yes| no | complexity | warn |
| [needless_bitwise_bool  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | pedantic | allow |
| [assertions_on_constants  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | pedantic | warn |

## G.TYP.Bool.02 使用多个布尔表达式条件时避免引入冗余条件

**【级别】** 要求

**【描述】**

略

【反例】

```rust
if a && b || a { ... }
```

该示例中，条件 `b` 是不需要的，它等价于 `if a {...}`。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes| no | correctness | deny |


## G.TYP.Bool.03 如果 match 匹配表达式为布尔类型，应使用 `if` 表达式来代替

**【级别】** 建议

**【描述】**

对于布尔表达式更倾向于使用 `if ... else ...`，相比较 `match` 模式匹配更有利于代码可读性。

【反例】

```rust
# fn foo() {}
# fn bar() {}
let condition: bool = true;
match condition {
    true => foo(),
    false => bar(),
}
```

【正例】

```rust
# fn foo() {}
# fn bar() {}
let condition: bool = true;
if condition {
    foo();
} else {
    bar();
}
```

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes | no | correctness | deny |
| [match_bool](https://rust-lang.github.io/rust-clippy/master/#match_bool) | yes | no | pedantic | allow |


## G.TYP.Bool.04 禁止将数字类型转换为布尔值

**【级别】** 要求

**【描述】**

这可能会让布尔值在内存中的表示无效。

【反例】

```rust
let x = 1_u8;
unsafe {
    let _: bool = std::mem::transmute(x); // where x: u8
}

```

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [transmute_int_to_bool ](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_bool ) | yes| no | complexity | warn |


## G.TYP.Bool.05 禁止在if表达式条件中使用块（block）结构

**【级别】** 要求

**【描述】**

为了增加可读性。

【反例】

```rust
if { true } { /* ... */ }

# fn somefunc() -> bool { true };
if { let x = somefunc(); x } { /* ... */ }
```

【正例】

```rust
if true { /* ... */ }

# fn somefunc() -> bool { true };
let res = { let x = somefunc(); x };
if res { /* ... */ }
```

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [blocks_in_if_conditions  ](https://rust-lang.github.io/rust-clippy/master/#blocks_in_if_conditions  ) | yes| no | style | warn |


## G.TYP.Bool.06 非必要时，布尔运算应使用逻辑运算符( `&&/||`)而非位运算符 (`&/|`)

**【级别】** 要求

**【描述】**

位运算不支持短路（short-circuiting），所以会影响性能。逻辑运算符则支持短路。

【反例】

```rust
let (x,y) = (true, false);
if x & !y {} //  位运算符，不支持短路
```

【正例】

```rust
let (x,y) = (true, false);
if x && !y {} //  逻辑运算符，支持短路
```

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [needless_bitwise_bool](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool) | yes           | no           | pedantic   | allow |

