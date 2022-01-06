## P.MAC.Decl.07   确保在宏定义之后再去调用宏

  【描述】

 Rust 中类型或函数，你可以在定义前后都可以调用它，但是宏不一样。 Rust  查找宏定义是按词法依赖顺序的，必须注意定义和调用的先后顺序。

【正例】

```rust
macro_rules! X { () => {}; }
mod a {
    X!(); // defined
}
mod b {
    X!(); // defined
}
mod c {
    X!(); // defined
}
fn main() {}
```

【反例】

```rust
mod a {
    // X!(); // undefined
}
mod b {
    // X!(); // undefined
    macro_rules! X { () => {}; }
    X!(); // defined
}
mod c {
    // X!(); // undefined
}
fn main() {}
```

【例外】

宏与宏之间相互调用，不受词法顺序的限制。

```rust
mod a {
    // X!(); // undefined
}

macro_rules! X { () => { Y!(); }; } // 注意：这里的 Y! 宏是在定义前被调用的，代码正常执行

mod b {
    // X!(); // defined, but Y! is undefined 
}

macro_rules! Y { () => {}; } // Y! 宏被定义在 X! 宏后面

mod c {
    X!(); // defined, and so is Y!
}
fn main() {}
```
