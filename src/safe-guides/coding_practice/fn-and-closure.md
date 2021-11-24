# 函数与闭包



---
<!-- toc -->
---

## P.FUD.01  函数参数建议使用借用类型

**【描述】**

这里是指 借用类型，而非 借用有所有权的类型。比如：`&str` 优于 `&String`，`&[T]` 优于`&Vec<T>`，`&T` 优于 `&Box<T>` 等。

使用 借用类型 可以利用 `Deref` 隐式转换让函数参数更加灵活。

【正例】

```rust
// 这里的参数可以接受 &String / &'str/ &'static str 三种类型参数
fn three_vowels(word: &str) -> bool {
    let mut vowel_count = 0;
    for c in word.chars() {
        match c {
            'a' | 'e' | 'i' | 'o' | 'u' => {
                vowel_count += 1;
                if vowel_count >= 3 {
                    return true
                }
            }
            _ => vowel_count = 0
        }
    }
    false
}

fn main() {
    let sentence_string =
        "Once upon a time, there was a friendly curious crab named Ferris".to_string();
    for word in sentence_string.split(' ') {
        if three_vowels(word) {
            println!("{} has three consecutive vowels!", word);
        }
    }
}
```

## P.FUD.02   传递到闭包的变量建议单独重新绑定

**【描述】**

默认情况下，闭包通过借用来捕获环境变量。或者，可以使用 `move` 关键字来移动环境变量到闭包中。

将这些要在闭包内用的变量，重新进行分组绑定，可读性更好。

【正例】

```rust
use std::rc::Rc;

let num1 = Rc::new(1);
let num2 = Rc::new(2);
let num3 = Rc::new(3);
// 单独对要传递到闭包的变量重新绑定
let num2_cloned = num2.clone();
let num3_borrowed = num3.as_ref();
let closure = move || {
    *num1 + *num2_cloned + *num3_borrowed;
};
```

【反例】

```rust
use std::rc::Rc;

let num1 = Rc::new(1);
let num2 = Rc::new(2);
let num3 = Rc::new(3);
let closure = {
    // `num1` is moved
    let num2 = num2.clone();  // `num2` is cloned
    let num3 = num3.as_ref();  // `num3` is borrowed
    move || {
        *num1 + *num2 + *num3;
    }
};
```

## P.FUD.03   函数返回值不要使用 `return`

**【描述】**

Rust 中函数块会自动返回最后一个表达式的值，不需要显式地指定 Return。

只有在函数过程中需要提前返回的时候再加 Return。

【正例】

```rust
fn foo(x: usize) -> usize {
    if x < 42{
        return x;
    }
    x + 1
}
```

【反例】

```rust
fn foo(x: usize) -> usize {
    if x < 42{
        return x;
    }
    return x + 1;
}
```





---

## G.FUD.01  函数参数最长不要超过 五 个

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [too_many_arguments](https://rust-lang.github.io/rust-clippy/master/#too_many_arguments) | yes           | no           | complexity | warn  |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 函数参数最长不要超过5个
too-many-arguments-threshold=5
```

### 【描述】

为了提升代码可读性，函数的参数最长不宜超过五个。

【正例】

想办法把过长的参数缩短。

```rust
struct Color;
// 此处使用 常量泛型（const generic） 来接收后面多个 u32 类型的参数
// 使用元组 缩短 2~3 个参数为一个参数
fn foo<T, const N: usize>(x: (f32, f32), name: &str, c: Color, last: [T; N]) {
    ;
}

fn main(){
    let arr = [1u32, 2u32];
    foo((1.0f32, 2.0f32), "hello", Color, arr);
    let arr = [1.0f32, 2.0f32, 3.0f32];
    foo((1.0f32, 2.0f32), "hello", Color, arr);
}
```

【反例】

```rust
struct Color;
fn foo(x: f32, y: f32, name: &str, c: Color, w: u32, h: u32, a: u32, b: u32) {
    // ..
}
```

## G.FUD.02   当函数参数实现了 Copy，并且是按值传入，如果值可能会太大，则应该考虑按引用传递

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [large_types_passed_by_value](https://rust-lang.github.io/rust-clippy/master/#large_types_passed_by_value) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api=true 
```

### 【描述】

通过值传递的参数可能会导致不必要的 `memcpy` 拷贝，这可能会造成性能损失。

【正例】

```rust
#[derive(Clone, Copy)]
struct TooLarge([u8; 2048]);

// Good
fn foo(v: &TooLarge) {}
```

【反例】

```rust
#[derive(Clone, Copy)]
struct TooLarge([u8; 2048]);

// Bad
fn foo(v: TooLarge) {}
```

## G.FUD.03   当函数参数出现太多 bool 类型的参数时，应该考虑将其封装为自定义的结构体或枚举

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [fn_params_excessive_bools](https://rust-lang.github.io/rust-clippy/master/#fn_params_excessive_bools) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 用于配置函数可以拥有的 bool 类型参数最大数量，默认为 3。
max-fn-params-bools=3 
```

### 【描述】

布尔类型的参数过多，很难让人记住，容易出错。将其封装为枚举或结构体，可以更好地利用类型系统的检查而避免出错。

【正例】

```rust
enum Shape {
    Round,
    Spiky,
}

enum Temperature {
    Hot,
    IceCold,
}

fn f(shape: Shape, temperature: Temperature) { ... }
```

【反例】

```rust
fn f(is_round: bool, is_hot: bool) { ... }
```

## G.FUD.04   当Copy 类型的足够小的值作为函数参数时，应该按值（by-value）传入，而不是引用(by-ref)

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [trivially_copy_pass_by_ref](https://rust-lang.github.io/rust-clippy/master/#trivially_copy_pass_by_ref) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 如果函数是被导出的 API，则该 lint 不会被触发，是防止 lint 建议对 API 有破坏性的改变。默认为 true
avoid-breaking-exported-api=true
# 考虑Copy按值而不是按引用传递的类型的最大大小（以字节为单位）。默认是None
trivial-copy-size-limit=None
```

**注意**，该 lint 没有考虑指针相关的情况，见例外示例。需要酌情考虑使用。例外示例来自  [rust-clippy/issues/5953](https://github.com/rust-lang/rust-clippy/issues/5953) 。

### 【描述】

在函数参数为 Copy 类型 且 其值足够小的时候，一般情况下，会避免传引用。因为对于这种小的值，性能上和按引用传递是一样快的，并且在代码更容易编写和可读。包括一些小的 结构体，也推荐按值传递，但要注意【例外】示例所示的情况。

【正例】

```rust
fn foo(v: u32) {}
```

【反例】

```rust
fn foo(v: &u32) {}
```

【例外】

```rust
#[derive(Clone, Copy)]
struct RawPoint {
    pub x: u8,
}

#[derive(Clone, Copy)]
struct Point {
    pub raw: RawPoint,
}

impl Point {
    pub fn raw(&self) -> *const RawPoint {
        &self.raw
    }
    // 如果听信 lint 的建议，将上面的 raw 函数参数 self 的引用去掉就是 raw_linted 函数
    pub fn raw_linted(self) -> *const RawPoint {
        &self.raw
    }
}

fn main() {
    let p = Point { raw: RawPoint { x: 10 } };

    // This passes
    assert_eq!(p.raw(), p.raw());
    // This fails 事实上，如果去掉那个 self 的引用，该函数的行为就变了
    // 因为 结构体 Point 是 Copy 的，每次调用 raw_linted 方法，结构体实例就会被复制一次，得到的结果就不一样了
    assert_eq!(p.raw_linted(), p.raw_linted());
}
```

## G.FUD.05   函数参数是不可变借用的时候，返回值不应该是可变借用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [mut_from_ref](https://rust-lang.github.io/rust-clippy/master/#mut_from_ref) | yes           | no           | correctness | deny  |

### 【描述】

【正例】

```rust
fn foo(&Foo) -> &Bar { .. }
```

【反例】

```rust
fn foo(&Foo) -> &mut Bar { .. }
```

## G.FUD.06   不要为函数指定  `inline(always)` 

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [inline_always](https://rust-lang.github.io/rust-clippy/master/#inline_always) | yes           | no           | pedantic   | allow |

### 【描述】

`inline` 虽然可以提升性能，但也会增加编译时间和编译大小。

Rust 中性能、编译时间和编译大小之间需要权衡。根据需要再 `inline` 即可。

【反例】

```rust
#[inline(always)]
fn not_quite_hot_code(..) { ... }
```

