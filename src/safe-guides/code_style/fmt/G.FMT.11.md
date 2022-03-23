## G.FMT.11  导入模块分组规则

**【级别】** 建议

**【描述】**

1. 导入同一模块的类型，应该置于同一个块内（`imports_granularity="Crate"`）。
2. 模块导入应该按以下规则进行分组（`group_imports="StdExternalCrate"`）：
    - 导入来自 `std`、`core` 和 `alloc`的模块需要置于前面一组。
    - 导入来自 第三方库的模块 应该置于中间一组。
    - 导入来自本地 `self`、`super`和`crate`前缀的模块，置于后面一组。
3. 分组内使用字典序进行排序（`reorder_imports=true`）。

说明： 默认 rustfmt 不会对导入的模块自动分组，而是保留开发者的导入顺序。所以，这里需要修改rustfmt 默认配置，但因为这几个配置项暂时未稳定，所以需要在 Nightly 下使用。

**【反例】**

```rust

// 当 `imports_granularity="Preserve"`
use foo::b;
use foo::b::{f, g};
use foo::{a, c, d::e};
use qux::{h, i};


// 当按默认值设置时，模块导入比较乱，影响可读性
use super::update::convert_publish_payload;
use chrono::Utc;

use alloc::alloc::Layout;
use juniper::{FieldError, FieldResult};
use uuid::Uuid;

use std::sync::Arc;

use broker::database::PooledConnection;

use super::schema::{Context, Payload};
use crate::models::Event;
use core::f32;
```

**【正例】**

```rust
// 当 `imports_granularity="Crate"`
use foo::{
    a, b,
    b::{f, g},
    c,
    d::e,
};
use qux::{h, i};


// 当 `group_imports="StdExternalCrate` 且 `reorder_imports=true`
use alloc::alloc::Layout;
use core::f32;
use std::sync::Arc;

use broker::database::PooledConnection;
use chrono::Utc;
use juniper::{FieldError, FieldResult};
use uuid::Uuid;

use super::schema::{Context, Payload};
use super::update::convert_publish_payload;
use crate::models::Event;
```

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`imports_granularity`](https://rust-lang.github.io/rustfmt/?#imports_granularity) | （Preserve（默认），Crate(推荐)）| No | 默认保留开发者的模块导入顺序|
| [`reorder_imports`](https://rust-lang.github.io/rustfmt/?#reorder_imports) | true（默认） | No| 模块分组内根据模块首字母按字典序进行排序 |
| [`group_imports`](https://rust-lang.github.io/rustfmt/?#group_imports) | （Preserve（默认）， StdExternalCrate（建议）） | No| 默认保留开发者的模块导入分组 |
