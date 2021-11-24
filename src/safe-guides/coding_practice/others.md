# 其他

---
<!-- toc -->
---

## G.OTH.01    对于某些场景下不建议使用的方法可以通过配置 `clippy.toml` 来拒绝

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group      | level |
| ------------------------------------------------------------ | ------------- | ------------ | --------------- | ----- |
| [disallowed_method](https://rust-lang.github.io/rust-clippy/master/#disallowed_method) | yes           | no           | nursery         | allow |
| [disallowed_script_idents](https://rust-lang.github.io/rust-clippy/master/#disallowed_script_idents) | yes           | no           | **restriction** | allow |
| [disallowed_type](https://rust-lang.github.io/rust-clippy/master/#disallowed_type) | yes           | no           | **nursery**     | allow |

这些lint 作用相似，但注意`nursery` 的lint 还未稳定。 

### 【描述】

有些场合可能需要拒绝使用一些容易出错的方法或函数，可以在 `clippy.toml` 中通过配置 `disallowed_method` 来满足这个需求。

```toml
# clippy.toml
disallowed-methods = [
    # Can use a string as the path of the disallowed method.
    "std::boxed::Box::new",
    # Can also use an inline table with a `path` key.
    { path = "std::time::Instant::now" },
    # When using an inline table, can add a `reason` for why the method
    # is disallowed.
    { path = "std::vec::Vec::leak", reason = "no leaking memory" },
]

# 允许 Lint 支持配置值对应的本地语言
# 配置时候从该列表取别名 https://www.unicode.org/iso15924/iso15924-codes.html
allowed-locales = ["Latin", "Cyrillic"] 
```

【反例】

当 `clippy.toml` 做了上面配置时，下面代码会曝出警告。

```rust
// Example code where clippy issues a warning
let xs = vec![1, 2, 3, 4];
xs.leak(); // Vec::leak is disallowed in the config.
// The diagnostic contains the message "no leaking memory".

let _now = Instant::now(); // Instant::now is disallowed in the config.

let _box = Box::new(3); // Box::new is disallowed in the config.
```

【正例】

```rust
// Example code which does not raise clippy warning
let mut xs = Vec::new(); // Vec::new is _not_ disallowed in the
```

## G.OTH.02   【标准库】 计算秒级、毫秒级、微秒级的时间请使用对应的方法

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [duration_subsec](https://rust-lang.github.io/rust-clippy/master/#duration_subsec) | yes           | no           | complexity | warn  |

### 【描述】

【正例】

```rust
# use std::time::Duration;
let dur = Duration::new(5, 0);

// Good
let _micros = dur.subsec_micros(); // 得到微秒
let _millis = dur.subsec_millis(); // 得到毫秒
```

【反例】

```rust
# use std::time::Duration;
let dur = Duration::new(5, 0);

// Bad
let _micros = dur.subsec_nanos() / 1_000;      // 用纳秒 计算
let _millis = dur.subsec_nanos() / 1_000_000;  // 用纳秒 计算

```



