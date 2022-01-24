## G.FMT.11  多个函数参数和导入模块的布局

**【级别】** 建议

**【描述】**

1. 五个以内函数参数可以置于一行，超过五个则使用「块」状缩进。
2. 导入模块每行超过四个，则换行。

**【反例】**

当 `fn_args_layout` 和 `imports_layout` 被设置为其他值时：

```rust
trait Lorem {
    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet);

    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet) {
        // body
    }

    fn lorem(
        ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur,
        adipiscing: Adipiscing, elit: Elit,
    );

    fn lorem(
        ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur,
        adipiscing: Adipiscing, elit: Elit,
    ) {
        // body
    }
}

use foo::{xxxxxxxxxxxxxxxxxx, yyyyyyyyyyyyyyyyyy, zzzzzzzzzzzzzzzzzz};

use foo::{
    aaaaaaaaaaaaaaaaaa,
    bbbbbbbbbbbbbbbbbb,
    cccccccccccccccccc,
    dddddddddddddddddd,
    eeeeeeeeeeeeeeeeee,
    ffffffffffffffffff,
};

```

**【正例】**

```rust

trait Lorem {
    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet, consectetur: Consectetur);

    fn lorem(ipsum: Ipsum, dolor: Dolor, sit: Sit, amet: Amet) {
        // body
    }

    fn lorem(
        ipsum: Ipsum,
        dolor: Dolor,
        sit: Sit,
        amet: Amet,
        consectetur: Consectetur,
        adipiscing: Adipiscing,
        elit: Elit,
    );

    fn lorem(
        ipsum: Ipsum,
        dolor: Dolor,
        sit: Sit,
        amet: Amet,
        consectetur: Consectetur,
        adipiscing: Adipiscing,
        elit: Elit,
    ) {
        // body
    }
}

use foo::{xxxxxxxxxxxxxxxxxx, yyyyyyyyyyyyyyyyyy, zzzzzzzzzzzzzzzzzz};

use foo::{
    aaaaaaaaaaaaaaaaaa, bbbbbbbbbbbbbbbbbb, cccccccccccccccccc, dddddddddddddddddd,
    eeeeeeeeeeeeeeeeee,
};
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`fn_args_layout`](https://rust-lang.github.io/rustfmt/?#fn_args_layout) | Tall（默认） | Yes|  函数参数五个或以内可以一行，超过五个则使用块状缩进|
| [`imports_layout`](https://rust-lang.github.io/rustfmt/?#imports_layout) | Mixed（默认） | No| 导入模块每行超过四个则换行 |
