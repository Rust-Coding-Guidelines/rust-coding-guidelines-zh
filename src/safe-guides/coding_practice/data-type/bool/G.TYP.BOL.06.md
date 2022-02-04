## G.TYP.BOL.06  不应使用数字来代替布尔值

**【级别】** 要求

**【描述】**

Rust 中布尔值就是 `true`  和 `false`。 不要试图使用数字 `1` 和 `0` 来代替布尔值。

虽然 布尔值 可以强转为 对应的数字，但是反之则不行。

不要通过判断数字来代替 布尔值，除非是 FFi 场景通过 C-ABI 和其他语言打交道。

**【反例】**

```rust
let a = 1;
let b = 0;
assert_eq!(true, a == 1);  
assert_eq!(false, b == 0);
```

**【正例】**

```rust
let a = true;
let b = false;
assert_eq!(1, a as u32);
assert_eq!(0, b as u32);
```

【例外】

FFi 时从外部传入数字

---
