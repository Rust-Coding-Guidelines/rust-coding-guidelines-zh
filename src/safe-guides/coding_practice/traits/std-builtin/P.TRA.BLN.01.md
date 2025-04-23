## P.TRA.BLN.01  在实现`Borrow`特质时，需要注意一致性

**【描述】**

当你想把不同类型的借用进行统一抽象，或者当你要建立一个数据结构，以同等方式处理自拥有值（ownered）和借用值（borrowed）时，例如散列（hash）和比较（compare）时，选择`Borrow`。当把某个类型直接转换为引用，选择 `AsRef` 。

但是使用 `Borrow` 的时候，需要注意一致性问题。具体请看示例。

**【反例】**

```rust
// 这个结构体能不能作为 HashMap 的 key？
pub struct CaseInsensitiveString(String);

// 它实现 Eq 没有问题
impl  PartialEq for CaseInsensitiveString {
    fn eq(&self, other: &Self) -> bool {
       // 但这里比较是要求忽略了 ascii 大小写
        self.0.eq_ignore_ascii_case(&other.0)
    }
}

impl Eq for CaseInsensitiveString { }

// 实现 Hash 没有问题
// 但因为 eq 忽略大小写，那么 hash 计算也必须忽略大小写
impl Hash for CaseInsensitiveString {
    fn hash<H: Hasher>(&self, state: &mut H) {
        for c in self.0.as_bytes() {
            // 不符合：没有忽略大小写
            c.to_ascii_lowercase().hash(state)
        }
    }
}
```

这种情况下，就不能为 `CaseInsensitiveString` 实现 `Borrow`，并非编译不通过，而是在逻辑上不应该为其实现 `Borrow`，因为  `CaseInsensitiveString`  实现 `Eq` 和 `Hash` 的行为不一致，而 `HashMap` 则要求 `Key` 必须 `Hash` 和 `Eq` 的实现一致。这种不一致，编译器无法检查，所以在逻辑上，就不应该为其实现 `Borrow`。如果强行实现，那可能会出现逻辑 Bug。
