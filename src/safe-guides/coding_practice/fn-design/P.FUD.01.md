## P.FUD.01   传递到闭包的变量建议单独重新绑定

**【描述】**

默认情况下，闭包通过借用来捕获环境变量。或者，可以使用 `move` 关键字来移动环境变量到闭包中。

将这些要在闭包内用的变量，重新进行分组绑定，可读性更好。

**【反例】**

```rust
use std::rc::Rc;

let num1 = Rc::new(1);
let num2 = Rc::new(2);
let num3 = Rc::new(3);
let closure = {
    // `num1` is moved
    let num2 = num2.clone();  // `num2` is cloned
    let num3 = num3.as_ref();  // `num3` is borrowed
    move || {
        *num1 + *num2 + *num3;
    }
};
```

**【正例】**

```rust
use std::rc::Rc;

let num1 = Rc::new(1);
let num2 = Rc::new(2);
let num3 = Rc::new(3);
// 单独对要传递到闭包的变量重新绑定
let num2_cloned = num2.clone();
let num3_borrowed = num3.as_ref();
let closure = move || {
    *num1 + *num2_cloned + *num3_borrowed;
};
```
