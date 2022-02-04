## G.CTF.02 控制流程的分支逻辑要保持精炼

**【级别】** 建议

**【描述】**

略

**【反例】**

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

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    "bar" | _ => {},
}

```

**【正例】**

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

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    _ => {},
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [collapsible_else_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_else_if) | yes           | no           | style          | warn  |
| [collapsible_if](https://rust-lang.github.io/rust-clippy/master/#collapsible_if) | yes           | no           | style          | warn  |
| [collapsible_match](https://rust-lang.github.io/rust-clippy/master/#collapsible_match) | yes           | no           | style          | warn  |
| [double_comparisons](https://rust-lang.github.io/rust-clippy/master/#double_comparisons) | yes           | no           | **complexity** | warn  |
| [wildcard_in_or_patterns](https://rust-lang.github.io/rust-clippy/master/#wildcard_in_or_patterns) | yes           | no           | **complexity** | warn  |



