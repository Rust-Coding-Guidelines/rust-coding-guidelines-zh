## P.CMT.05  在注释中使用 `FIXME` 和 `TODO` 来帮助任务协作

**【描述】**

通过在注释中开启 `FIXME` 和 `TODO` 可以方便协作。正式发布版本可以不做此类标注。

注意：此条目不适于使用 `rustfmt`相关配置项 `report_fixme` 和 `report_todo`，在 `rustfmt` v2.0 中已经移除这两项配置。

**【正例】**

```rust
// 符合
// TODO(calebcartwright): consider enabling box_patterns feature gate
fn annotation_type_for_level(level: Level) -> AnnotationType {
    match level {
        Level::Bug | Level::Fatal | Level::Error => AnnotationType::Error,
        Level::Warning => AnnotationType::Warning,
        Level::Note => AnnotationType::Note,
        Level::Help => AnnotationType::Help,
        // FIXME(#59346): Not sure how to map these two levels
        Level::Cancelled | Level::FailureNote => AnnotationType::Error,
        Level::Allow => panic!("Should not call with Allow"),
    }
}
```

