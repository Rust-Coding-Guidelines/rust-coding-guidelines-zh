## G.TRA.BLN.04   在使用`#[derive(Hash)]` 的时候，避免再手工实现 `PartialEq`

**【级别】** 要求

**【描述】**

实现 Hash 和 Eq 必须要满足下面一个等式：

```text
k1 == k2  -> hash(k1) == hash(k2)
```

即，当`k1` 和 `k2` 相等时，`hash(k1)`也应该和 `hash(k2)` 相等。  所以要求 `PartialEq` / `Eq` / `Hash`  的实现必须保持一致。 

如果用 `#[derive(Hash)]` 的时候，搭配了一个手工实现的 `PartialEq` 就很可能出现不一致的情况。

但也有例外。

**【反例】**

```rust
#[derive(Hash)]
struct Foo;
// 不符合
impl PartialEq for Foo {
    ...
}
```

**【正例】**

```rust
// 符合
#[derive(PartialEq, Eq, Hash)]
struct Foo;
```

**【例外】**

```rust
// From: https://docs.rs/crate/blsttc/3.3.0/source/src/lib.rs

// Clippy warns that it's dangerous to derive `PartialEq` and explicitly implement `Hash`, but the
// `pairing::bls12_381` types don't implement `Hash`, so we can't derive it.
#![allow(clippy::derive_hash_xor_eq)]
```

**【Lint 检测】**

| lint name                                                                                | Clippy 可检测 | Rustc 可检测 | Lint Group  | 默认level |
| ---------------------------------------------------------------------------------------- | ------------- | ------------ | ----------- | --------- |
| [derive_hash_xor_eq](https://rust-lang.github.io/rust-clippy/master/#derive_hash_xor_eq) | yes           | no           | correctness | deny      |
