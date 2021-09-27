# 控制流程

Rust 中 流程控制 也是属于 表达式，但在本规范中将其独立出来。


---


## G.CTL.01  避免在流程控制分支中使用重复代码

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------ | ---- | --------- | ------ | ------ | 
| [branches_sharing_code](https://rust-lang.github.io/rust-clippy/master/#branches_sharing_code) | yes| no | nursery | allow |

### 【描述】

【正例】

```rust
println!("Hello World");
let foo = if … {
    13
} else {
    42
};
```

【反例】

```rust
let foo = if … {
    println!("Hello World");
    13
} else {
    println!("Hello World");
    42
};
```

## G.CTL.02   控制流程的分支逻辑要保持精炼

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [collapsible_else_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_else_if) | yes           | no           | style          | warn  |
| [collapsible_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_if) | yes           | no           | style          | warn  |
| [collapsible_match](https://rust-lang.github.io/rust-clippy/master/#collapsible_match) | yes           | no           | style          | warn  |
| [double_comparisons](https://rust-lang.github.io/rust-clippy/master/#double_comparisons) | yes           | no           | **complexity** | warn  |

### 【描述】

【正例】

```rust
// else if
if x {
    …
} else if y {
    …
}

// Merge multiple conditions
if x && y {
    …
}

// match 
fn func(opt: Option<Result<u64, String>>) {
    let n = match opt {
        Some(Ok(n)) => n,
        _ => return,
    };
}

// comparisons
# let x = 1;
# let y = 2;
if x <= y {}
```

【反例】

```rust

if x {
    …
} else {     // collapsible_else_if
    if y {
        …
    }
}

if x {  // collapsible_if
    if y {
        …
    }
}

// collapsible_match
fn func(opt: Option<Result<u64, String>>) {
    let n = match opt {
        Some(n) => match n {
            Ok(n) => n,
            _ => return,
        }
        None => return,
    };
}

// double_comparisons
# let x = 1;
# let y = 2;
if x == y || x < y {}
```

## G.CTL.03   当需要通过比较大小来区分不同情况时，优先使用`match` 和 `cmp` 来代替 if 表达式

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [comparison_chain](https://rust-lang.github.io/rust-clippy/master/#comparison_chain) | yes           | no           | style      | warn  |

### 【描述】

这种情况下，使用 `match` 和 `cmp` 代替 `if` 的好处是语义更加明确，而且也能帮助开发者穷尽所有可能性。
但是这里需要注意这里使用 `match` 和 `cmp` 的性能要低于 `if`表达式，因为 一般的 `>` 或 `<` 等比较操作是内联的，而 `cmp`方法没有内联。

根据实际情况来选择是否设置 `comparison_chain` 为 `allow`。

【正例】

```rust
use std::cmp::Ordering;
# fn a() {}
# fn b() {}
# fn c() {}
fn f(x: u8, y: u8) {
     match x.cmp(&y) {
         Ordering::Greater => a(),
         Ordering::Less => b(),
         Ordering::Equal => c()
     }
}
```

【反例】

```rust
# fn a() {}
# fn b() {}
# fn c() {}
fn f(x: u8, y: u8) {
    if x > y {
        a()
    } else if x < y {
        b()
    } else {
        c()
    }
}
```

