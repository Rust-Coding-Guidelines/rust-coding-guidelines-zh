## P.FMT.05 存在多个标识符时应该保持块状（Block）缩进

**【描述】**

当在表达式或语言项定义中出现多个标识符，则应该让其保持块状风格缩进。

**【反例】**

```rust
fn main() {
    // 不符合：缩进不符合标准，只是为了对齐
    let lorem = vec!["ipsum",
                     "dolor",
                     "sit",
                     "amet",
                     "consectetur",
                     "adipiscing",
                     "elit"];
}
```

流程控制

```rust
fn main() {
    // 不符合：缩进不符合标准，只是为了对齐
    if lorem_ipsum
       && dolor_sit // 注意：这里缩进只是三个空格，仅仅是和前一行 `lorem_ipsum`对齐
       && amet_consectetur
       && lorem_sit
       && dolor_consectetur
       && amet_ipsum
       && lorem_consectetur
    {
        // ...
    }
}
```

函数参数


```rust
fn lorem() {}

fn lorem(ipsum: usize) {}
// 不符合
fn lorem(ipsum: usize,
         dolor: usize,
         sit: usize,
         amet: usize,
         consectetur: usize,
         adipiscing: usize,
         elit: usize) {
    // body
}
```

函数调用

```rust
fn main() {
    // 不符合
    lorem("lorem",
          "ipsum",
          "dolor",
          "sit",
          "amet",
          "consectetur",
          "adipiscing",
          "elit");
}
```

泛型


```rust
// 不符合
fn lorem<Ipsum: Eq = usize,
         Dolor: Eq = usize,
         Sit: Eq = usize,
         Amet: Eq = usize,
         Adipiscing: Eq = usize,
         Consectetur: Eq = usize,
         Elit: Eq = usize>(
    ipsum: Ipsum,
    dolor: Dolor,
    sit: Sit,
    amet: Amet,
    adipiscing: Adipiscing,
    consectetur: Consectetur,
    elit: Elit)
    -> T {
    // body
}
```

结构体

```rust
fn main() {
    let lorem = Lorem { ipsum: dolor,
                        sit: amet };
}
```


**【正例】**

数组：

```rust
fn main() {
    // 符合： 缩进四个空格
    let lorem = vec![
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
        "elit",
    ];
}
```

流程控制

```rust
fn main() {
    // 符合： 缩进四个空格
    if lorem_ipsum
        && dolor_sit
        && amet_consectetur
        && lorem_sit
        && dolor_consectetur
        && amet_ipsum
        && lorem_consectetur
    {
        // ...
    }
}
```

函数参数

```rust
fn lorem() {}

fn lorem(ipsum: usize) {}

// 符合： 缩进四个空格
fn lorem(
    ipsum: usize,
    dolor: usize,
    sit: usize,
    amet: usize,
    consectetur: usize,
    adipiscing: usize,
    elit: usize,
) {
    // body
}

```

函数调用

```rust
fn main() {
    // 符合： 缩进四个空格
    lorem(
        "lorem",
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
        "elit",
    );
}
```

泛型

```rust
// 符合： 缩进四个空格
fn lorem<
    Ipsum: Eq = usize,
    Dolor: Eq = usize,
    Sit: Eq = usize,
    Amet: Eq = usize,
    Adipiscing: Eq = usize,
    Consectetur: Eq = usize,
    Elit: Eq = usize,
>(
    ipsum: Ipsum,
    dolor: Dolor,
    sit: Sit,
    amet: Amet,
    adipiscing: Adipiscing,
    consectetur: Consectetur,
    elit: Elit,
) -> T {
    // body
}
```

结构体

```rust
fn main() {
    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };
}
```

**【rustfmt 配置】**

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`indent_style`](https://rust-lang.github.io/rustfmt/?#indent_style) | Block（默认） | No| 多个标识符定义保持块状风格，缩进符合标准 |
| [`indent_style`](https://rust-lang.github.io/rustfmt/?#indent_style) | Visual | No| 多个标识符定义保持对齐风格，但不符合缩进标准 |
