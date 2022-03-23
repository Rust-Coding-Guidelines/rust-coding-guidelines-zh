## G.CMT.04  在注释中使用 `FIXME` 和 `TODO` 来帮助任务协作

**【级别】** 建议

**【描述】**

通过在注释中开启 `FIXME` 和 `TODO` 可以方便协作。rustfmt 默认不开启该项，所以需要配置。

但是配置为 `Always` 没必要，只需要配置为 `Unnumbered` 针对编号的 `FXIME` 和 `TODO` 报告即可。

这两个配置目前有 Bug ，无法正确识别报告，但不影响这个规则的应用。

**【正例】**

```rust

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

**【rustfmt 配置】**

此规则 Clippy 不可检测，由 rustfmt 自动格式化。

rustfmt 配置：

| 对应选项 | 可选值 | 是否 stable | 说明 |
| ------ | ---- | ---- | ---- | 
| [`report_fixme`](https://rust-lang.github.io/rustfmt/?#report_fixme) | Never（默认），Unnumbered（推荐） | No|  是否报告 FIXME 注释 |
| [`report_todo`](https://rust-lang.github.io/rustfmt/?#report_todo) | Never（默认），Unnumbered（推荐） | No|  是否报告 FIXME 注释 |