# 布尔



## P.TYP.Bool.01  不要使用数字来代替 布尔值

**【描述】**

Rust 中布尔值就是 `true`  和 `false`。 不要试图使用数字 `1` 和 `0` 来代替布尔值。

虽然 布尔值 可以强转为 对应的数字，但是反之则不行。

不要通过判断数字来代替 布尔值，除非是 FFi 场景通过 C-ABI 和其他语言打交道。

【正例】

```rust
let a = true;
let b = false;
assert_eq!(1, a as u32);
assert_eq!(0, b as u32);
```

【反例】

```rust
let a = 1;
let b = 0;
assert_eq!(true, a == 1);  
assert_eq!(false, b == 0);
```



---

## G.TYP.Bool.01 返回为布尔值的表达式或函数值不需要和布尔字面量进行比较

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [bool_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_comparison ) | yes| no | complexity | warn |
| [bool_assert_comparison ](https://rust-lang.github.io/rust-clippy/master/#bool_assert_comparison ) | yes| no | complexity | warn |
| [needless_bool ](https://rust-lang.github.io/rust-clippy/master/#needless_bool ) | yes| no | complexity | warn |
| [nonminimal_bool  ](https://rust-lang.github.io/rust-clippy/master/#nonminimal_bool  ) | yes| no | complexity | warn |
| [needless_bitwise_bool  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | pedantic | allow |
| [assertions_on_constants  ](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool  ) | yes| no | style | warn |



### 【描述】

在 Rust 中，返回为布尔值的表达式或函数值可以直接当作布尔值使用。

总之，使用布尔表达式的时候，要尽可能地简洁明了。


【正例】

```rust
if x {}
if !y {}

assert!(!"a".is_empty());
```

【反例】

```rust
if x == true {}
if y == false {}

assert_eq!("a".is_empty(), false);
assert_ne!("a".is_empty(), true);
```

## G.TYP.Bool.02 使用多个布尔表达式条件的时候要避免引入不必要的条件

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes| no | correctness | deny |

### 【描述】

【反例】

```rust
if a && b || a { ... }
```

该示例中，条件 `b` 是不需要的，它等价于 `if a {...}`。

## G.TYP.Bool.03 如果 match 匹配表达式为布尔类型，建议使用 `if` 表达式来代替

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes | no | pedantic | allow |
| [match_bool](https://rust-lang.github.io/rust-clippy/master/#match_bool) | yes | no | pedantic | allow |

### 【描述】

对于布尔表达式更倾向于使用 `if ... else ...`，相比较 `match` 模式匹配更有利于代码可读性。

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


## G.TYP.Bool.04 不要尝试将数字类型转换为布尔值

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [transmute_int_to_bool ](https://rust-lang.github.io/rust-clippy/master/#transmute_int_to_bool ) | yes| no | complexity | warn |

### 【描述】

这可能会让布尔值在内存中的表示无效。

【反例】

```rust
let x = 1_u8;
unsafe {
    let _: bool = std::mem::transmute(x); // where x: u8
}

```


## G.TYP.Bool.05 不要在 if 表达式条件中使用块（block）结构

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [blocks_in_if_conditions  ](https://rust-lang.github.io/rust-clippy/master/#blocks_in_if_conditions  ) | yes| no | style | warn |

### 【描述】

为了增加可读性。

【正例】

```rust
if true { /* ... */ }

# fn somefunc() -> bool { true };
let res = { let x = somefunc(); x };
if res { /* ... */ }
```

【反例】

```rust
if { true } { /* ... */ }

# fn somefunc() -> bool { true };
if { let x = somefunc(); x } { /* ... */ }
```


## G.TYP.Bool.06       非必要时，布尔运算优先使用 逻辑运算符( `&&/||`)而非 位运算符 (`&/|`)

### 【级别：必须】

必须按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [needless_bitwise_bool](https://rust-lang.github.io/rust-clippy/master/#needless_bitwise_bool) | yes           | no           | pedantic   | allow |

### 【描述】

位运算不支持短路（short-circuiting），所以会影响性能。逻辑运算符则支持短路。

【正例】

```rust
let (x,y) = (true, false);
if x && !y {} //  逻辑运算符，支持短路
```

【反例】

```rust
let (x,y) = (true, false);
if x & !y {} //  位运算符，不支持短路
```

