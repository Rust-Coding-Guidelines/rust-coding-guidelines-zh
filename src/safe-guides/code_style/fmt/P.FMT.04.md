## P.FMT.04  语言项（Item) 定义时左花括号（brace）位置应该与语言项保持同一行

**【描述】**

为了保持代码结构的良好可读性，Rust 中定义各种语言项，包括控制结构（`if / match` 等）、函数、结构体、枚举等，要求左花括号与其定义保持同一行。

但是如果携带 `where`语句，则要求换行，并且`where` 子句和 `where` 关键字不在同一行。

`rustfmt` 提供三个配置项对不同的语言项进行格式化：

- `brace_style` 配置项对应于大部分语言项，包括函数、结构体等，但是控制结构（`if / match`等）除外，默认值为`SameLineWhere`，代表左花括号与语言项定义保持同一行。
- `where_single_line` 配置项对应于 `where` 语句，默认值是 `false`，表示 `where`语句的花括号是换行。
- `control_brace_style` 配置项对应于控制结构（`if / match`等），默认值为`AlwaysSameLine`，表示左花括号与语言项定义保持同一行。

所以，只需要使用 `rustfmt` 默认配置即可。

**【反例】**

如果设置 `brace_style = "AlwaysNextLine"`，则不符合。

```rust
// 不符合： 左花括号与函数语言项定义未保持同一行
fn lorem()
{
    // body
}
```

如果设置 `brace_style = "PreferSameLine"`，则符合：

```rust
// 不符合： 左花括号与 where 语句 应该换行
fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div, { // 注意这里和 `SameLineWhere`的区别
    // body
}
```

结构体与枚举：


如果设置 `brace_style = "AlwaysNextLine"`，则不符合：


```rust
// 不符合： 左花括号与结构体语言项定义未保持同一行
struct Lorem
{
    ipsum: bool,
}
```

如果设置 `brace_style = "PreferSameLine"`，则符合：

```rust
// 不符合： 左花括号与 where 语句应该换行
struct Dolor<T>
where
    T: Eq, {
    sit: T,
}
```

流程控制倾向于默认使用 `AlwaysSameLine`，即，总在同一行。因为流程控制没有`where`子句。

如果设置 `brace_style = "AlwaysNextLine"`，则不符合：

```rust
fn main() {
    // 不符合： 左花括号与控制结构未保持同一行
    if lorem
    {
        println!("ipsum!");
    }
    else
    {
        println!("dolor!");
    }
}
```


**【正例】**

函数：

```rust
// 符合： 左花括号和 函数语言项定义在同一行
fn lorem() { 
    // body
}

fn lorem<T>(ipsum: T)
where // 符合：`where` 子句和 `where` 关键字不在同一行
    T: Add + Sub + Mul + Div,
{ // 符合：当有 `where` 子句的时候，花括号换行
    // body
}
```

 结构体与枚举

```rust
// 符合
struct Lorem {
    ipsum: bool,
}

// 符合
struct Dolor<T>
where 
    T: Eq,
{
    sit: T,
}
```

流程控制倾向于默认使用 `AlwaysSameLine`，即，总在同一行。因为流程控制没有`where`子句。

```rust
// 符合
// "AlwaysSameLine" (default)
fn main() {
    if lorem {
        println!("ipsum!");
    } else {
        println!("dolor!");
    }
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style) | SameLineWhere （默认）| No| 应该与语言项保持同一行，但是 where 语句例外 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| AlwaysNextLine | No | 应该在语言项的下一行 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| PreferSameLine | No | 总是优先与语言项保持同一行，where 语句也不例外 |
|[`where_single_line`](https://rust-lang.github.io/rustfmt/?#where_single_line)| false（默认）| No |  强制将 `where` 子句放在同一行上 |
|[`control_brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#control_brace_style)| AlwaysSameLine （默认） | No |  总在同一行上，用于控制流程中默认值 |
|[`control_brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#control_brace_style)| ClosingNextLine| No |  用于控制流程中 else 分支在 if 分支结尾处换行|
