## G.MOD.02  如果是作为库供别人使用，在 `lib.rs`中重新导出对外类型、函数和 trait 等

**【级别】** 建议

**【描述】**

这样使用方在使用的时候，就不需要`use crate::mod::mod::struct`，可以直接使用`use crate::struct`，好处是使用方`use`的时候会比较方便和直观。

**【正例】**

```rust
// 符合
// From syn crate
pub use crate::data::{
    Field, Fields, FieldsNamed, FieldsUnnamed, Variant, VisCrate, VisPublic, VisRestricted,
    Visibility,
};
```