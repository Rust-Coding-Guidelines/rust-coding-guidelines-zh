## P.TRA.01  使用 trait 时要注意 trait 一致性规则

**【描述】**

使用 trait 的时候，必须要满足 trait 一致性规则，即，**孤儿规则（orphans rule）**：类型和trait，必须有一个是在本地crate内定义的。
当不满足孤儿规则时，可以考虑使用`NewType`模式来解决问题。

**【正例】**
```rust
// String 和 FromStr都在标准库中被定义
// 如果想给String实现FromStr，则编译器会报错，告诉你这违反孤儿规则
// （虽然标准库内已经为string实现了FromStr,这里只是示例）
// 但是通过使用NewType，我们可以间接的达成目标
// 使用这种单个元素的元组结构体包装一个类型就叫NewType模式。
pub struct PhoneNumber(String);

use std::str::FromStr;
impl FromStr for PhoneNumber {
    type Err = Box<dyn std::error::Error>;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        Ok(PhoneNumber(s.to_string()))
    }
}
```

