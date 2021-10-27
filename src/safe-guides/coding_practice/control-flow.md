

# 控制流程

Rust 中 流程控制 也是属于 表达式，但在本规范中将其独立出来。


---

## P.CTL.01 不要过度使用迭代器

**【描述】**

迭代器虽然是 Rust 中比较推崇的方式，但也没必要过度使用它。总之，如果使用迭代器让代码太复杂，就考虑换个非迭代器的方式实现吧。

【正例】

创建一个 Matrix变换的函数，使用命令式风格，代码功能比较明确，更加直观。

```rust
// From: https://adventures.michaelfbryan.com/posts/rust-best-practices/bad-habits/#overusing-iterators
pub fn imperative_blur(input: &Matrix) -> Matrix {
    assert!(input.width >= 3);
    assert!(input.height >= 3);

    // allocate our output matrix, copying from the input so
    // we don't need to worry about the edge cases.
    let mut output = input.clone();

    for y in 1..(input.height - 1) {
        for x in 1..(input.width - 1) {
            let mut pixel_value = 0.0;

            pixel_value += input[[x - 1, y - 1]];
            pixel_value += input[[x, y - 1]];
            pixel_value += input[[x + 1, y - 1]];

            pixel_value += input[[x - 1, y]];
            pixel_value += input[[x, y]];
            pixel_value += input[[x + 1, y]];

            pixel_value += input[[x - 1, y + 1]];
            pixel_value += input[[x, y + 1]];
            pixel_value += input[[x + 1, y + 1]];

            output[[x, y]] = pixel_value / 9.0;
        }
    }

    output
}
```

【反例】

创建一个 Matrix变换的函数，但是这种迭代器的方式，代码可读性相比于命令式更困难。

```rust
// From : https://adventures.michaelfbryan.com/posts/rust-best-practices/bad-habits/#overusing-iterators
pub fn functional_blur(input: &Matrix) -> Matrix {
    assert!(input.width >= 3);
    assert!(input.height >= 3);

    // Stash away the top and bottom rows so they can be
    // directly copied across later
    let mut rows = input.rows();
    let first_row = rows.next().unwrap();
    let last_row = rows.next_back().unwrap();

    let top_row = input.rows();
    let middle_row = input.rows().skip(1);
    let bottom_row = input.rows().skip(2);

    let blurred_elements = top_row
        .zip(middle_row)
        .zip(bottom_row)
        .flat_map(|((top, middle), bottom)| blur_rows(top, middle, bottom));

    let elements: Vec<f32> = first_row
        .iter()
        .copied()
        .chain(blurred_elements)
        .chain(last_row.iter().copied())
        .collect();

    Matrix::new_row_major(elements, input.width, input.height)
}

fn blur_rows<'a>(
    top_row: &'a [f32],
    middle_row: &'a [f32],
    bottom_row: &'a [f32],
) -> impl Iterator<Item = f32> + 'a {
    // stash away the left-most and right-most elements so they can be copied across directly.
    let &first = middle_row.first().unwrap();
    let &last = middle_row.last().unwrap();

    // Get the top, middle, and bottom row of our 3x3 sub-matrix so they can be
    // averaged.
    let top_window = top_row.windows(3);
    let middle_window = middle_row.windows(3);
    let bottom_window = bottom_row.windows(3);

    // slide the 3x3 window across our middle row so we can get the average
    // of everything except the left-most and right-most elements.
    let averages = top_window
        .zip(middle_window)
        .zip(bottom_window)
        .map(|((top, middle), bottom)| top.iter().chain(middle).chain(bottom).sum::<f32>() / 9.0);

    std::iter::once(first)
        .chain(averages)
        .chain(std::iter::once(last))
}
```

## P.CTL.02   优先使用模式匹配而不是 `if` 判断值是否相等

**【描述】**

Rust 中 模式匹配 是惯用法，而不是通过 `if` 判断值是否相等。

【正例】

```rust
if let Some(value) = opt {
  ...
}
// or
if let [first, ..] = list {
  ...
}
```

【反例】

 ```rust
 let opt: Option<_> = ...;
 
 if opt.is_some() {
   let value = opt.unwrap();
   ...
 }
 
 // or
 let list: &[f32] = ...;
 
 if !list.is_empty() {
   let first = list[0];
   ...
 }
 
 ```







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
| [wildcard_in_or_patterns](https://rust-lang.github.io/rust-clippy/master/#wildcard_in_or_patterns) | yes           | no           | **complexity** | warn  |

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

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    _ => {},
}
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

// wildcard_in_or_patterns    
match "foo" {
    "a" => {},
    "bar" | _ => {},
}

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



## G.CTL.04    `if` 条件表达式分支中如果包含了 `else if`  分支也应该包含 `else` 分支

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [else_if_without_else](https://rust-lang.github.io/rust-clippy/master/#else_if_without_else) | yes           | no           | **restriction** | allow |

### 【描述】

【正例】

```rust
# fn a() {}
# fn b() {}
# let x: i32 = 1;
if x.is_positive() {
    a();
} else if x.is_negative() {
    b();
} else {
    // We don't care about zero.
}
```

【反例】

```rust
# fn a() {}
# fn b() {}
# let x: i32 = 1;
if x.is_positive() {
    a();
} else if x.is_negative() {
    b();
}
```



## G.CTL.05    如果要通过 `if` 条件表达式来判断是否panic，请优先使用断言

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [if_then_panic](https://rust-lang.github.io/rust-clippy/master/#if_then_panic) | yes           | no           | Style   |warn|

### 【描述】

【正例】

```rust
let sad_people: Vec<&str> = vec![];
assert!(sad_people.is_empty(), "there are sad people: {:?}", sad_people);
```

【反例】

```rust
let sad_people: Vec<&str> = vec![];
if !sad_people.is_empty() {
    panic!("there are sad people: {:?}", sad_people);
}
```

## G.CTL.06   善用标准库中提供的迭代器适配器方法来满足自己的需求

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [explicit_counter_loop](https://rust-lang.github.io/rust-clippy/master/#explicit_counter_loop) | yes           | no           | complexity | warn  |
| [filter_map_identity](https://rust-lang.github.io/rust-clippy/master/#filter_map_identity) | yes           | no           | complexity | warn  |
| [filter_next](https://rust-lang.github.io/rust-clippy/master/#filter_next) | yes           | no           | complexity | warn  |
| [flat_map_identity](https://rust-lang.github.io/rust-clippy/master/#flat_map_identity) | yes           | no           | complexity | warn  |
| [flat_map_option](https://rust-lang.github.io/rust-clippy/master/#flat_map_option) | yes           | no           | complexity | warn  |

### 【描述】

Rust 标准库中提供了很多迭代器方法，要学会使用它们，选择合适的方法来满足自己的需求。

下面示例中，反例中的迭代器适配器方法，都可以用对应的正例中的方法代替。

【正例】

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
for (i, item) in v.iter().enumerate() { bar(i, *item); }

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.flatten();

// filter_next
let vec = vec![1];
vec.iter().find(|x| **x == 0);

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flatten();

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().filter_map(|x| x.parse().ok()).collect();

```

【反例】

```rust
// explicit_counter_loop
let v = vec![1];
fn bar(bar: usize, baz: usize) {}
let mut i = 0;
for item in &v {
    bar(i, *item);
    i += 1;
}

// filter_map_identity
let iter = vec![Some(1)].into_iter();
iter.filter_map(|x| x);

// filter_next
let vec = vec![1];
vec.iter().filter(|x| **x == 0).next();

// flat_map_identity
let iter = vec![vec![0]].into_iter();
iter.flat_map(|x| x);

// flat_map_option
let nums: Vec<i32> = ["1", "2", "whee!"].iter().flat_map(|x| x.parse().ok()).collect();
```







































