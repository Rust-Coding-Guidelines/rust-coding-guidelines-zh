## G.FMT.03  每行最大宽度为 100 个字符

**【级别】** 建议

**【描述】**

代码行宽不宜过长，否则不利于阅读。
建议每行字符数不要超过 100 个字符。

`rustfmt` 还提供很多其他宽度设置：

- fn_call_width, 函数调用最大宽度设置，其默认值是 `max_width`的 `60%`。
- attr_fn_like_width, 像函数那样使用的属性宏最大宽度，其默认值是 `max_width`的 `70%`。
- struct_lit_width,  结构体字面量最大宽度，其默认值是 `max_width`的 `18%`。
- struct_variant_width, 结构体变量最大宽度，其默认值是 `max_width`的 `35%`。
- array_width, 数组最大宽度，其默认值是 `max_width`的 `60%`。
- chain_width, 链式结构最大宽度，其默认值是 `max_width`的 `60%`。
- single_line_if_else_max_width，单行 `if-else` 最大宽度，其默认值是 `max_width`的 `50%`。

这么多宽度设置管理起来比较麻烦，所以使用 `use_small_heuristics` 来管理更好。

**【反例】**

`use_small_heuristics` 默认配置示例。

当`use_small_heuristics` 配置为 `Off` :

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit {
        amet: Consectetur,
        adipiscing: Elit,
    },
}

fn main() {
    lorem("lorem", "ipsum", "dolor", "sit", "amet", "consectetur", "adipiscing");

    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };

    let lorem = if ipsum {
        dolor
    } else {
        sit
    };
}
```

当`use_small_heuristics` 配置为 `Max` :

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit { amet: Consectetur, adipiscing: Elit },
}

fn main() {
    lorem("lorem", "ipsum", "dolor", "sit", "amet", "consectetur", "adipiscing");

    let lorem = Lorem { ipsum: dolor, sit: amet };

    let lorem = if ipsum { dolor } else { sit };
}
```

**【正例】**

```rust
enum Lorem {
    Ipsum,
    Dolor(bool),
    Sit { amet: Consectetur, adipiscing: Elit },
}

fn main() {
    lorem(
        "lorem",
        "ipsum",
        "dolor",
        "sit",
        "amet",
        "consectetur",
        "adipiscing",
    );

    let lorem = Lorem {
        ipsum: dolor,
        sit: amet,
    };
    let lorem = Lorem { ipsum: dolor };

    let lorem = if ipsum { dolor } else { sit };
}
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- |
| [`max_width`](https://rust-lang.github.io/rustfmt/?#max_width) | 100 | yes（默认）| 行最大宽度默认值|
|[`error_on_line_overflow`](https://rust-lang.github.io/rustfmt/?#error_on_line_overflow)| false（默认）| No (tracking issue: #3391)| 如果超过最大行宽设置则报错|
|[`use_small_heuristics`](https://rust-lang.github.io/rustfmt/?#use_small_heuristics)| Default（默认）Max（推荐） | Yes| 统一管理宽度设置|