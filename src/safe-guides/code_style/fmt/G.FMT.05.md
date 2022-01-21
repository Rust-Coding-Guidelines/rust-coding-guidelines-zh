## G.FMT.05  语言项（Item) 定义时花括号（brace）位置应该与语言项保持同一行

**【级别】** 建议

**【描述】**

花括号的位置风格默认使用 `SameLineWhere`，但是也根据不同的语言项略有区别。

#### 函数 

**【反例】**

如果设置 `brace_style = "AlwaysNextLine"`，则：

```rust
fn lorem()
{
    // body
}

fn lorem(ipsum: usize)
{
    // body
}

fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div,
{
    // body
}
```

如果设置 `brace_style = "PreferSameLine"`，则：

```rust
fn lorem() {
    // body
}

fn lorem(ipsum: usize) {
    // body
}

fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div, { // 注意这里和 `SameLineWhere`的区别
    // body
}
```

**【正例】**

```rust
fn lorem() { // 花括号和fn定义在同一行
    // body
}

fn lorem(ipsum: usize) { // 花括号和fn定义在同一行
    // body
}

// 当有 `where` 子句的时候，花括号换行
// 并且，`where` 子句和 `where` 关键字不在同一行
fn lorem<T>(ipsum: T)
where
    T: Add + Sub + Mul + Div,
{
    // body
}
```

通过配置 `where_single_line` 为 true，方可设置 `where`子句在同一行，如下：

```rust
// 当有 `where` 子句的时候，花括号换行
// 设置了 `where_single_line=true` ，则`where` 子句和 `where` 关键字在同一行
fn lorem<T>(ipsum: T)
where T: Add + Sub + Mul + Div,
{
    // body
}
```

#### 结构体与枚举

**【反例】**

如果设置 `brace_style = "AlwaysNextLine"`，则：


```rust
struct Lorem
{
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq,
{
    sit: T,
}
```

如果设置 `brace_style = "PreferSameLine"`，则：

```rust
struct Lorem {
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq, {
    sit: T,
}
```

**【正例】**

```rust
struct Lorem {
    ipsum: bool,
}

struct Dolor<T>
where
    T: Eq,
{
    sit: T,
}
```

#### 流程控制

流程控制倾向于默认使用 `AlwaysSameLine`，即，总在同一行。因为流程控制没有`where`子句。

**【反例】**

如果设置 `brace_style = "AlwaysNextLine"`，则：

```rust
fn main() {
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

如果设置 `brace_style = "ClosingNextLine"`，则：

```rust
fn main() {
    if lorem {
        println!("ipsum!");
    } // 注意这里 if 分支结尾处，else 换行
    else { 
        println!("dolor!");
    }
}
```

**【正例】**

```rust
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

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style) | SameLineWhere （默认）| No| 应该与语言项保持同一行，但是 where 语句例外 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| AlwaysNextLine | No | 应该在语言项的下一行 |
|[`brace_style`](https://rust-lang.github.io/rustfmt/?#brace_style)| PreferSameLine | No | 总是优先与语言项保持同一行，where 语句也不例外 |
|[`where_single_line`](https://rust-lang.github.io/rustfmt/?#where_single_line)| false（默认）| No |  强制将 `where` 子句放在同一行上 |
|[`brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#AlwaysSameLine)| AlwaysSameLine （默认） | No |  总在同一行上，用于控制流程中默认值 |
|[[`brace_style` in control-flow](https://rust-lang.github.io/rustfmt/?#ClosingNextLine)| ClosingNextLine| No |  用于控制流程中 else 分支在 if 分支结尾处换行|
