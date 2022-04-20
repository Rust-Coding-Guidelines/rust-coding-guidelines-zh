## G.TYP.BOL.07 使用 `.not()` 方法代替逻辑取反运算符 (`!`)

**【级别】** 建议

**【描述】**

逻辑取反运算符 (`!`) 是前缀一元运算符，相对较长的逻辑表达式来说很不显眼。

理解业务逻辑时，容易忽略取反符号，并且需要回头看。

使用 `.not()` 后缀方法 (`std::ops::Not`) 可以吸引注意力，视觉上更为连续。

**【反例】**

```rust
assert!(!self.map.contains(&key));

if !cache.contains(&key) {
    // ...
}

// 不符合：容易忽略取反符号
```

**【正例】**

```rust
use std::ops::Not;

assert!(self.map.contains(&key).not());

if cache.contains(&key).not() {
    // ...
}

// 符合：`.not()` 更容易吸引注意力
```
