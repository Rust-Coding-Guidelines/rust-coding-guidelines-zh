## P.VAR.01  非必要请避免像 C 语言那样先声明可变变量再赋值

**【描述】**

一般情况下，不要先声明一个可变的变量，然后在后续过程中再去改变它的值，这可能来自于 C 语言的习惯，但 Rust 中不推荐这样使用。

一般情况下，声明一个变量的时候，要对其进行初始化。如果后续可能会改变其值，要考虑优先使用变量遮蔽（继承式可变）功能。如果需要在一个子作用域内改变其值，再使用可变绑定或可变引用。

**【反例】**

```rust

let base : u8;
if cfg!(not(USB_PROTOCOL_NEW_ARCH)) {
    base = other_instance.base;
} else {
    base = 42u8;
}
```

**【正例】**

```rust
let base : u8 = if cfg!(not(USB_PROTOCOL_NEW_ARCH)) {
    base = other_instance.base;
} else {
    base = 42u8;
}
```