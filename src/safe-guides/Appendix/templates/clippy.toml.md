# Clippy 模板

有些 Clippy 的 Lint，依赖于一些配置项，如果不想要默认值，可以在 `clippy.toml` 中进行设置。

```toml
# for `disallowed_method`:
# https://rust-lang.github.io/rust-clippy/master/index.html#disallowed_method
disallowed-methods = []

# 函数参数最长不要超过5个
too-many-arguments-threshold=5
```



## Clippy lint 配置模板

```rust
// 参考： https://github.com/serde-rs/serde/blob/master/serde/src/lib.rs
#![allow(unknown_lints, bare_trait_objects, deprecated)]
#![cfg_attr(feature = "cargo-clippy", allow(renamed_and_removed_lints))]
#![cfg_attr(feature = "cargo-clippy", deny(clippy, clippy_pedantic))]
// Ignored clippy and clippy_pedantic lints
#![cfg_attr(
    feature = "cargo-clippy",
    allow(
        // clippy bug: https://github.com/rust-lang/rust-clippy/issues/5704
        unnested_or_patterns,
        // clippy bug: https://github.com/rust-lang/rust-clippy/issues/7768
        semicolon_if_nothing_returned,
        // not available in our oldest supported compiler
        checked_conversions,
        empty_enum,
        redundant_field_names,
        redundant_static_lifetimes,
        // integer and float ser/de requires these sorts of casts
        cast_possible_truncation,
        cast_possible_wrap,
        cast_sign_loss,
        // things are often more readable this way
        cast_lossless,
        module_name_repetitions,
        option_if_let_else,
        single_match_else,
        type_complexity,
        use_self,
        zero_prefixed_literal,
        // correctly used
        enum_glob_use,
        let_underscore_drop,
        map_err_ignore,
        result_unit_err,
        wildcard_imports,
        // not practical
        needless_pass_by_value,
        similar_names,
        too_many_lines,
        // preference
        doc_markdown,
        unseparated_literal_suffix,
        // false positive
        needless_doctest_main,
        // noisy
        missing_errors_doc,
        must_use_candidate,
    )
)]
// Rustc lints.
#![deny(missing_docs, unused_imports)]
```

