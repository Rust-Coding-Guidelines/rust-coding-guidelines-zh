## P.MAC.DCL.08 同一个 crate 内定义的宏相互调用时，需要注意卫生性

**【描述】**

 当同一个 crate 内定义的宏相互调用时候，应该使用 `$crate` 元变量来指代当前被调用宏的路径。

**【反例】**

```rust
#[macro_export]
macro_rules! helped {
    () => { helper!() } // This might lead to an error due to 'helper' not being in scope.
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// 在另外的 crate 中使用这两个宏
// 注意：`helper_macro::helper` 并没有导入进来
use helper_macro::helped;

fn unit() {
   // Error! 这个宏会出现问题，因为其内部调用的 helper 宏的路径会被编译器认为是当前调用crate 的路径
   helped!();
}
```

**【正例】**

```rust
#[macro_export]
macro_rules! helped {
    () => { $crate::helper!() }
}

#[macro_export]
macro_rules! helper {
    () => { () }
}

//// 在另外的 crate 中使用这两个宏
// 注意：`helper_macro::helper` 并没有导入进来
use helper_macro::helped;

fn unit() {
   // OK! 这个宏能运行通过，因为 `$crate` 正确地展开成 `helper_macro` crate 的路径（而不是使用者的路径）
   helped!();
}
```
