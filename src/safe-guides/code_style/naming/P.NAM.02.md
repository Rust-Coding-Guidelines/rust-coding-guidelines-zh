## P.NAM.02  cargo feature 名中不应该含有无意义的占位词

**【描述】**

给 [Cargo feature](http://doc.crates.io/manifest.html#the-features-section) 命名时，不要带有无实际含义的的词语，比如无需 `use-abc` 或 `with-abc` ，而是直接以 `abc` 命名。
 
这条原则经常出现在对 Rust 标准库进行 [可选依赖(optional-dependency)](https://doc.rust-lang.org/cargo/reference/features.html#optional-dependencies) 配置的 crate 上。


**【反例】**

```toml
# In Cargo.toml

// 不要给 feature 取 `use-std` 或者 `with-std` 或者除 `std` 之外另取名字。
[features]
default = ["use-std"]
std = []
```

```rust,ignored
// In lib.rs

#![cfg_attr(not(feature = "use-std"), no_std)]
```

feature 应与 Cargo 在推断可选依赖时隐含的 features 具有一致的名字。

假如`x` crate 对 Serde 和 标准库 具有可选依赖关系

```toml
[package]
name = "x"
version = "0.1.0"

[features]
std = ["serde/std"]
// Cargo 要求 features 应该是叠加的，所以像 `no-abc` 这种负向的 feature 命名实际上并不正确。
no-abc=[]

[dependencies]
serde = { version = "1.0", optional = true }
```




**【正例】**

最简洁且正确的做法是：

```toml
# In Cargo.toml

[features]
default = ["std"]
std = []
```

```rust,ignored
// In lib.rs

#![cfg_attr(not(feature = "std"), no_std)]
```

假如 `x` crate 对 Serde 和 标准库具有可选依赖关系：

```toml
[package]
name = "x"
version = "0.1.0"

[features]
std = ["serde/std"]

[dependencies]
serde = { version = "1.0", optional = true }
```

当我们使用 `x` crate 时，可以使用 `features = ["serde"]` 开启 Serde 依赖。类似地，也可以使用 `features = ["std"]` 开启标准库依赖。
Cargo 推断的隐含的 features 应该叫做 `serde` ，而不是 `use-serde` 或者 `with-serde` 。
