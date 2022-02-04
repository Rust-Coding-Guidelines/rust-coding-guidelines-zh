## G.TRA.BLN.08 实现 `From` 而不是 `Into`

**【级别】** 建议

**【描述】**

优先为类型实现 `From` 而非 `Into`。因为实现了 `From`，`Into` 也会被自动实现。并且在错误处理的时候，`?` 操作符会通过调用 `From` 实现自动进行错误类型转换。

但是在泛型限定上，优先 `Into` 。

当然，也存在例外。

**【反例】**

```rust
struct StringWrapper(String);

impl Into<StringWrapper> for String {
    fn into(self) -> StringWrapper {
        StringWrapper(self)
    }
}
```

**【正例】**

```rust
struct StringWrapper(String);

impl From<String> for StringWrapper {
    fn from(s: String) -> StringWrapper {
        StringWrapper(s)
    }
}
```

**【例外】**

有两类情况，可以直接实现 `Into`。

1. `Into` 不提供 `From` 实现。在一些场景中，`From`  自动实现的 `Into` 并不符合转换需求。
2. 使用 `Into` 来跳过孤儿规则。

```rust
// 第一种情况。 
// From： https://github.com/apache/arrow-datafusion/blob/master/ballista/rust/core/src/serde/scheduler/from_proto.rs
#[allow(clippy::from_over_into)]
impl Into<PartitionStats> for protobuf::PartitionStats {
    fn into(self) -> PartitionStats {
        PartitionStats::new(
            foo(self.num_rows),
            foo(self.num_batches),
            foo(self.num_bytes),
        )
    }
}

// From： https://github.com/apache/arrow-datafusion/blob/master/ballista/rust/core/src/serde/scheduler/to_proto.rs
#[allow(clippy::from_over_into)]
impl Into<protobuf::PartitionStats> for PartitionStats {
    fn into(self) -> protobuf::PartitionStats {
        let none_value = -1_i64;
        protobuf::PartitionStats {
            num_rows: self.num_rows.map(|n| n as i64).unwrap_or(none_value),
            num_batches: self.num_batches.map(|n| n as i64).unwrap_or(none_value),
            num_bytes: self.num_bytes.map(|n| n as i64).unwrap_or(none_value),
            column_stats: vec![],
        }
    }
}

// 第二种情况
// 根据孤儿规则，trait 和 类型必须有一个在本地定义，所以不能为 Vec<T> 实现 From trait
struct Wrapper<T>(Vec<T>);
impl<T> From<Wrapper<T>> for Vec<T> {
    fn from(w: Wrapper<T>) -> Vec<T> {
        w.0
    }
}
// 但是通过 Into<Vec<T>> ，就可以绕过这个规则
struct Wrapper<T>(Vec<T>);
impl<T> Into<Vec<T>> for Wrapper<T> {
    fn into(self) -> Vec<T> {
        self.0
    }
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [from_over_into](https://rust-lang.github.io/rust-clippy/master/#from_over_into) | yes           | no           | style      | warn  |



