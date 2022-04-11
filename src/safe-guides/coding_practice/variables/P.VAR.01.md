## P.VAR.01  一般情况下避免先声明可变变量再赋值

**【描述】**

一般情况下，不要先声明一个可变的变量，然后在后续过程中再去改变它的值。声明一个变量的时候，要对其进行初始化。如果后续可能会改变其值，要考虑优先使用变量遮蔽（继承式可变）功能。如果需要在一个子作用域内改变其值，再使用可变绑定或可变引用。

**【反例】**

```rust
// 不符合
let mut base : u8;
if cfg!(not(USB_PROTOCOL_NEW_ARCH)) {
    base = other_instance.base;
} else {
    base = 42u8;
}
```

**【正例】**

```rust
// 符合
let base : u8 = if cfg!(not(USB_PROTOCOL_NEW_ARCH)) {
    other_instance.base
} else {
    42u8
}
```