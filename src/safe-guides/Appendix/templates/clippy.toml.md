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

##  Embark Studios 的标准 Lint 配置

```rust
// BEGIN - Embark standard lints v5 for Rust 1.55+
// do not change or add/remove here, but one can add exceptions after this section
// for more info see: <https://github.com/EmbarkStudios/rust-ecosystem/issues/59>
#![deny(unsafe_code)]
#![warn(
    clippy::all,
    clippy::await_holding_lock,
    clippy::char_lit_as_u8,
    clippy::checked_conversions,
    clippy::dbg_macro,
    clippy::debug_assert_with_mut_call,
    clippy::disallowed_method,
    clippy::disallowed_type,
    clippy::doc_markdown,
    clippy::empty_enum,
    clippy::enum_glob_use,
    clippy::exit,
    clippy::expl_impl_clone_on_copy,
    clippy::explicit_deref_methods,
    clippy::explicit_into_iter_loop,
    clippy::fallible_impl_from,
    clippy::filter_map_next,
    clippy::flat_map_option,
    clippy::float_cmp_const,
    clippy::fn_params_excessive_bools,
    clippy::from_iter_instead_of_collect,
    clippy::if_let_mutex,
    clippy::implicit_clone,
    clippy::imprecise_flops,
    clippy::inefficient_to_string,
    clippy::invalid_upcast_comparisons,
    clippy::large_digit_groups,
    clippy::large_stack_arrays,
    clippy::large_types_passed_by_value,
    clippy::let_unit_value,
    clippy::linkedlist,
    clippy::lossy_float_literal,
    clippy::macro_use_imports,
    clippy::manual_ok_or,
    clippy::map_err_ignore,
    clippy::map_flatten,
    clippy::map_unwrap_or,
    clippy::match_on_vec_items,
    clippy::match_same_arms,
    clippy::match_wild_err_arm,
    clippy::match_wildcard_for_single_variants,
    clippy::mem_forget,
    clippy::mismatched_target_os,
    clippy::missing_enforced_import_renames,
    clippy::mut_mut,
    clippy::mutex_integer,
    clippy::needless_borrow,
    clippy::needless_continue,
    clippy::needless_for_each,
    clippy::option_option,
    clippy::path_buf_push_overwrite,
    clippy::ptr_as_ptr,
    clippy::rc_mutex,
    clippy::ref_option_ref,
    clippy::rest_pat_in_fully_bound_structs,
    clippy::same_functions_in_if_condition,
    clippy::semicolon_if_nothing_returned,
    clippy::single_match_else,
    clippy::string_add_assign,
    clippy::string_add,
    clippy::string_lit_as_bytes,
    clippy::string_to_string,
    clippy::todo,
    clippy::trait_duplication_in_bounds,
    clippy::unimplemented,
    clippy::unnested_or_patterns,
    clippy::unused_self,
    clippy::useless_transmute,
    clippy::verbose_file_reads,
    clippy::zero_sized_map_values,
    future_incompatible,
    nonstandard_style,
    rust_2018_idioms
)]
// END - Embark standard lints v0.5 for Rust 1.55+
// crate-specific exceptions:
#![allow()]

```

## Clippy 配置的相关问题

目前 Clippy 不支持配置文件来配置Lint ，目前 像 Embark 公司有两种解决方法：

1.  将 lint 放到一个[统一文件](https://github.com/EmbarkStudios/rust-ecosystem/blob/main/lints.rs)中，然后复制粘贴到使用的地方。
2. 通过 `.cargo/config.toml` 来配置 `rustflags`  ，参考： [lints.toml](https://github.com/EmbarkStudios/rust-ecosystem/blob/main/lints.toml) 

Embark 也在跟踪和推动在 Cargo 中支持 Lint 配置的功能，相关 issues：

- [Be able to disable/enable Clippy lints globally](https://github.com/EmbarkStudios/rust-ecosystem/issues/22)
- [Support defining enabled and disabled lints in a configuration file](https://github.com/rust-lang/cargo/issues/5034)
- [[Roadmap] Configuration file for lints](https://github.com/rust-lang/rust-clippy/issues/6625)

## 代码生成相关 clippy 配置

和 C 语言绑定代码生成，避免clippy 警告，相关配置可参考：

```rust
// Generated by gir (https://github.com/gtk-rs/gir @ 5bbf6cb)
// from ../gir-files (@ 8e47c67)
// DO NOT EDIT

#![allow(non_camel_case_types, non_upper_case_globals, non_snake_case)]
#![allow(clippy::approx_constant, clippy::type_complexity, clippy::unreadable_literal, clippy::upper_case_acronyms)]
```