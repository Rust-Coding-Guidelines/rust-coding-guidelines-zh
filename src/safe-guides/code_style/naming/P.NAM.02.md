## P.NAM.02  为 cargo feature 命名时不应含有无意义的占位词

**【描述】**

给 [Cargo feature](http://doc.crates.io/manifest.html#the-features-section) 命名时，不应带有无实际含义的的词语，比如使用`abc`命名来替代 `use-abc` 或 `with-abc`。
 
这条原则经常出现在对 Rust 标准库进行 [可选依赖(optional-dependency)](https://doc.rust-lang.org/cargo/reference/features.html#optional-dependencies) 配置的 crate 上。

并且 Cargo 要求 features 应该是相互叠加的，所以像 `no-abc` 这种负向的 feature 命名实际上并不正确。

**【反例】**

```toml
# In Cargo.toml


[features]
// 不符合
default = ["use-std"]
std = []
// 不符合
no-abc=[]
```

```rust,ignored
// In lib.rs

#![cfg_attr(not(feature = "use-std"), no_std)]
```

**【正例】**

最简洁且正确的做法是：

```toml
# In Cargo.toml

[features]
// 符合
default = ["std"]
std = []
```

```rust,ignored
// In lib.rs

#![cfg_attr(not(feature = "std"), no_std)]
```


