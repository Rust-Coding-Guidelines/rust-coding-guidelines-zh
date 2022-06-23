# Rustfmt 模板

为了方便 Rust 开发者，这里提供一个 Rustfmt 的模板，以供参考。

以下内容可以放到 `rustfmt.toml` 或 `.rustfmt.toml` 文件中。因为部分选项还未稳定，所以要使用 `cargo +nightly fmt` 执行。

很多选项都是默认的，无需配置。以下配置的都不是默认值。


**【只包含 Stable 的选项】**

```toml
# 万一你要使用 rustfmt 2.0 就需要指定这个·
version = "Two"

# 统一管理宽度设置，但不包含 comment_width
use_small_heuristics="MAX"
# 在match分支中，如果包含了块，也需要加逗号以示分隔
match_block_trailing_comma=true
# 当使用 extern 指定外部函数时，不需要显式指定 C-ABI ，默认就是 C-ABI
force_explicit_abi=false 
# 如果项目只在 Unix 平台下跑，可以设置该项为 Unix，表示换行符只依赖 Unix
newline_style="Unix" 
# 不要将多个 Derive 宏合并为同一行
merge_derives = false

# 指定 fmt 忽略的目录
ignore = [
     "src/test",
     "test",
     "docs",
]
```

**【也包含还未 Stable 的选项】**

未稳定，代表该选项还有一些 issue 没有解决，待解决以后就会稳定。

```toml
# 万一你要使用 rustfmt 2.0 就需要指定这个·
version = "Two"

# 统一管理宽度设置，但不包含 comment_width
use_small_heuristics="MAX"
# 使多个标识符定义保持对齐风格，代码看上去可以非常工整
indent_style="Visual"  # 未稳定
# 设置让自定义具有判别式的枚举体按等号对齐的宽度
enum_discrim_align_threshold = 10  # 未稳定
# 在match分支中，如果包含了块，也需要加逗号以示分隔
match_block_trailing_comma=true
# 自动将同一个 crate 的模块导入合并到一起
imports_granularity="Crate"  # 未稳定
# StdExternalCrate 导入模块分组规则
# 1. 导入来自 std、core 和 alloc 的模块需要置于前面一组。
# 2. 导入来自 第三方库的模块 应该置于中间一组。
# 3. 导入来自本地 self、super和crate前缀的模块，置于后面一组。
group_imports="StdExternalCrate"  # 未稳定
# format_macro_matchers 规则说明：
# 声明宏 模式匹配分支（=> 左侧）中要使用紧凑格式
# 默认声明宏分支代码体（=> 右侧） 使用宽松格式
format_macro_matchers=true  # 未稳定
# 当使用 extern 指定外部函数时，不需要显式指定 C-ABI ，默认就是 C-ABI
force_explicit_abi=false 
# 指定一行注释允许的最大宽度
comment_width=100   # 未稳定
# wrap_comments 配合 comment_width 使用，自动将一行超过宽带限制的注释切分为多行注释
wrap_comments=true  # 未稳定
# 将 /**/ 注释转为 //
normalize_comments=true # 未稳定
# 元组模式匹配的时候允许使用 `..` 来匹配剩余元素
condense_wildcard_suffixes=true # 未稳定
# 如果项目只在 Unix 平台下跑，可以设置该项为 Unix，表示换行符只依赖 Unix
newline_style="Unix" 
# 不要将多个 Derive 宏合并为同一行
merge_derives = false

# 指定 fmt 忽略的目录
ignore = [
     "src/test",
     "test",
     "docs",
]
```

