## G.FUD.03   当函数参数出现太多 bool 类型的参数时，应该考虑将其封装为自定义的结构体或枚举

**【级别】** 建议

**【描述】**

布尔类型的参数过多，很难让人记住，容易出错。将其封装为枚举或结构体，可以更好地利用类型系统的检查而避免出错。
其他类型参数过多时，也可以考虑是否可以用自定义结构体或枚举进行封装。

**【反例】**

```rust
#![warn(clippy::fn_params_excessive_bools)]

// 不符合
fn f(is_round: bool, is_hot: bool) { ... }
```

**【正例】**

```rust
#![warn(clippy::fn_params_excessive_bools)]

enum Shape {
    Round,
    Spiky,
}

enum Temperature {
    Hot,
    IceCold,
}

// 符合
fn f(shape: Shape, temperature: Temperature) { ... }
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [fn_params_excessive_bools](https://rust-lang.github.io/rust-clippy/master/#fn_params_excessive_bools) | yes           | no           | pedantic   | allow |

该 lint 对应 `clippy.toml` 配置项：

```toml
# 用于配置函数可以拥有的 bool 类型参数最大数量，默认为 3。
max-fn-params-bools=3 
```