# 内置 trait

Rust 标准库内置了很多 trait，在使用这些 trait 的时候也需要注意。

---

## G.TRA.Buitin.01   应该具体类型的 `default()` 方法代替 ` Default::default()` 调用

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [default_trait_access](https://rust-lang.github.io/rust-clippy/master/#default_trait_access) | yes           | no           | **pedantic** | allow |

### 【描述】

为了增强可读性。

【正例】

```rust
let s = String::default();
```

【反例】

```rust
let s: String = Default::default();
```

## G.TRA.Buitin.02    不要为迭代器实现`Copy` trait 

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group   | level |
| ------------------------------------------------------------ | ------------- | ------------ | ------------ | ----- |
| [copy_iterator](https://rust-lang.github.io/rust-clippy/master/#copy_iterator) | yes           | no           | **pedantic** | allow |

### 【描述】

在 Rust 中，迭代器是不能实现 Copy 的。因为在需要迭代修改的场景，因为 Copy 的存在，而失去效果。

【反例】

比如，对于标准库里的 `Range<T>` 就不能实现 Copy，因为它也是一个迭代器。

```rust
let mut iter = 0..n;
for i in iter { if i > 2 { break; } }
iter.collect();
```

如果它实现了 Copy，示例中 iter 的值将不会被改变，这样就不符合预期结果。

## G.TRA.Buitin.03   能使用`derive` 自动实现`Default`  trait 就不要用手工实现 

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group     | level |
| ------------------------------------------------------------ | ------------- | ------------ | -------------- | ----- |
| [derivable_impls](https://rust-lang.github.io/rust-clippy/master/#derivable_impls) | yes           | no           | **complexity** | warn  |

该lint不能用于检测泛型参数类型的 Default 手工实现。

### 【描述】

手工实现 Default，代码不精炼。 

【正例】

```rust
#[derive(Default)]
struct Foo {
    bar: bool
}
```

【反例】

```rust
struct Foo {
    bar: bool
}

impl std::default::Default for Foo {
    fn default() -> Self {
        Self {
            bar: false
        }
    }
}
```

## G.TRA.Buitin.04   在使用`#[derive(Hash)]` 的时候，避免再手工实现 `PartialEq`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [derive_hash_xor_eq](https://rust-lang.github.io/rust-clippy/master/#derive_hash_xor_eq) | yes           | no           | correctness | deny  |

### 【描述】

实现 Hash 和 Eq 必须要满足下面一个等式：

```text
k1 == k2  -> hash(k1) == hash(k2)
```

即，当`k1` 和 `k2` 相等时，`hash(k1)`也应该和 `hash(k2)` 相等。  所以要求 `PartialEq` / `Eq` / `Hash`  的实现必须保持一致。 

如果用 `#[derive(Hash)]` 的时候，搭配了一个手工实现的 `PartialEq` 就很可能出现不一致的情况。

但也有例外。

【正例】

```rust
#[derive(PartialEq, Eq, Hash)]
struct Foo;
```

【反例】

```rust
#[derive(Hash)]
struct Foo;

impl PartialEq for Foo {
    ...
}
```

【例外】

```rust
// From: https://docs.rs/crate/blsttc/3.3.0/source/src/lib.rs

// Clippy warns that it's dangerous to derive `PartialEq` and explicitly implement `Hash`, but the
// `pairing::bls12_381` types don't implement `Hash`, so we can't derive it.
#![allow(clippy::derive_hash_xor_eq)]
```

## G.TRA.Buitin.05   在使用`#[derive(Ord)]` 的时候，避免再手工实现 `PartialOrd`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [derive_ord_xor_partial_ord](https://rust-lang.github.io/rust-clippy/master/#derive_ord_xor_partial_ord) | yes           | no           | correctness | deny  |

### 【描述】

跟实现 Hash 和 Eq 的要求类似，对于实现` Ord` 的类型来说，必须要满足下面一个等式：

```text
k1.cmp(&k2) == k1.partial_cmp(&k2).unwrap()
```

所以要求与  `PartialOrd`   的实现必须保持一致，并确保`max`、`min`和`clamp`与`cmp`一致。

通过`#[derive(Ord)]` 并手动实现`PartialOrd`，很容易意外地使cmp和partial_cmp不一致。

但也有例外。

【正例】

```rust
#[derive(Ord, PartialOrd, PartialEq, Eq)]
struct Foo;

// or

#[derive(PartialEq, Eq)]
struct Foo;

impl PartialOrd for Foo {
    fn partial_cmp(&self, other: &Foo) -> Option<Ordering> {
       Some(self.cmp(other))
    }
}

impl Ord for Foo {
    ...
}

```

【反例】

```rust
#[derive(Ord, PartialEq, Eq)]
struct Foo;

impl PartialOrd for Foo {
    ...
}
```

【例外】

使用 `#[derive(PartialOrd)]` 自动实现 `PartialOrd`，然后再手工实现 `Ord`的时候在内部调用自动实现的`partial_cmp` ，应该是满足 `k1.cmp(&k2) == k1.partial_cmp(&k2).unwrap()` 了。

```rust
// From: https://docs.rs/crate/adventjson/0.1.1/source/src/lib.rs
#[derive(Clone, Debug, PartialEq, PartialOrd)]
pub enum JsonObject
{
    /// An array of objects (e.g.: \[1,2,3\])
    Array(Vec<Self>),
    /// Key-value pairs (e.g.: {\"first\": 10, \"other\": 15})
    Obj(Vec<(String, Self)>),
    /// A number (e.g.: -0.08333)
    Number(f64),
    /// A string (e.g.: \"Test: \\\"\")
    JsonStr(String),
    /// A boolean (e.g. true)
    Bool(bool),
    /// The null-value
    Null,
}

/// Save because no not-number values are allowed in json
impl Eq for JsonObject {}

/// Save because no not-number values are allowed in json
#[allow(clippy::derive_ord_xor_partial_ord)]
impl Ord for JsonObject
{
    fn cmp(&self, other: &Self) -> Ordering
    {
        self.partial_cmp(other).unwrap()
    }
}
```

## G.TRA.Buitin.06    不要对实现 `Copy`  或引用类型调用  `std::mem::drop` 和 `std::mem::forgot`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [drop_copy](https://rust-lang.github.io/rust-clippy/master/#drop_copy) | yes           | no           | correctness | deny  |
| [drop_ref](https://rust-lang.github.io/rust-clippy/master/#drop_ref) | yes           | no           | correctness | deny  |
| [forget_copy](https://rust-lang.github.io/rust-clippy/master/#forget_copy) | yes           | no           | correctness | deny  |
| [forget_ref](https://rust-lang.github.io/rust-clippy/master/#forget_ref) | yes           | no           | correctness | deny  |
| [undropped_manually_drops](https://rust-lang.github.io/rust-clippy/master/#undropped_manually_drops) | yes           | no           | correctness | deny  |

### 【描述】

`std::mem::drop` 函数只是利用 Rust 所有权的一个技巧，对于 实现了 Copy 的类型 或引用，是无效的。如果使用它，对导致代码可读方便产生误导作用。

另外`std::mem::drop` 也无法 Drop 掉 `ManuallyDrop` 类型。

`std::mem::forgot`   同理。

但是也存在例外的情况。

【反例】

```rust
let x: i32 = 42; // i32 implements Copy
std::mem::drop(x) // A copy of x is passed to the function, leaving the
                  // original unaffected
```

【例外】

在某些情况下，虽然不会有实际效果，但是为了提升语义，也可以使用。

下面代码中，为了防止自引用的问题，使用 `drop(self)` ，提升了代码语义，但实际并不会 drop。

```rust
// From: https://docs.rs/crate/dhall/0.10.1/source/src/error/builder.rs

#[allow(clippy::drop_ref)]
pub fn format(&mut self) -> String {
    if self.consumed {
        panic!("tried to format the same ErrorBuilder twice")
    }
    let this = std::mem::take(self);
    self.consumed = true;
    drop(self); // Get rid of the self reference so we don't use it by mistake.
    // ...
}
```



## G.TRA.Buitin.07   对实现 `Copy` 的可迭代类型来说，要通过迭代器拷贝其所有元素时，应该使用 `copied`方法，而非`cloned`

### 【级别：建议】

建议按此规范执行。

### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [cloned_instead_of_copied](https://rust-lang.github.io/rust-clippy/master/#cloned_instead_of_copied) | yes           | no           | pedantic   | allow |

### 【描述】

`copied` 方法在语义层面，是针对实现 `Copy` 的类型，所以应该使用 `copied` 来增加代码可读性。

【正例】

```rust
let a = [1, 2, 3];

let v_copied: Vec<_> = a.iter().copied().collect();
```

【反例】

```rust
let a = [1, 2, 3];

let v_copied: Vec<_> = a.iter().cloned().collect();
```

