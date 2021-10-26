# 函数与闭包



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

