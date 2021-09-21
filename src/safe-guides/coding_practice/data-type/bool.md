# 布尔

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

## G.TYP.Bool.03 对于布尔表达式要避免使用`match`模式匹配

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [logic_bug ](https://rust-lang.github.io/rust-clippy/master/#logic_bug ) | yes| no | pedantic | allow |

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

该示例中，条件 `b` 是不需要的，它等价于 `if a {...}`。


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