## G.MOD.04  一个项目中应该避免使用不同的模块布局风格

**【级别】** 建议

**【描述】**

Rust 支持两种 模块布局，文件夹内使用 `mod.rs` 或者是使用跟文件夹同名的文件名，来组织模块。

但是项目里如果混合这两种模块布局，是比较让人困惑的，最好统一为同一种风格。

 上面两种  lint ，选择其中一种用于检查是否存在不同的模块布局。

 **【反例】**

```rust
#![warn(clippy::self_named_module_files, clippy::mod_module_files)]

// 不符合：使用 `self_named_module_files`，不允许下面模块布局
#![warn(clippy::self_named_module_files)]
src/
  stuff/
    stuff_files.rs
  stuff.rs
  lib.rs

// 不符合：使用 `mod_module_files`，不允许下面模块布局
#![warn(clippy::mod_module_files)]
src/
  stuff/
    stuff_files.rs
    mod.rs
  lib.rs
```

**【正例】**

```rust
// 符合：使用 `self_named_module_files`，允许下面模块布局
#![warn(clippy::self_named_module_files)]
src/
  stuff/
    stuff_files.rs
    mod.rs
  lib.rs

// 符合：使用 `mod_module_files`，允许下面模块布局
#![warn(clippy::mod_module_files)]
src/
  stuff/
    stuff_files.rs
  stuff.rs
  lib.rs
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [self_named_module_files](https://rust-lang.github.io/rust-clippy/master/#self_named_module_files) | yes           | no           | restriction | allow |
| [mod_module_files](https://rust-lang.github.io/rust-clippy/master/#mod_module_files) | yes           | no           | restriction | allow |



