## P.MAC.DCL.06 当宏需要接收 `self` 时需要注意

**【描述】**

`self` 在 Rust 中属于关键字，它会在代码运行时被替换为具体类型的实例。当它传递给 宏 时会被看做为一个变量，而宏对于变量而言是具备卫生性的。而且，声明宏的作用只是替换而非计算，它并不能计算出 self 的具体类型。

**【反例】**

```rust
macro_rules! make_mutable {
    ($i:ident) => {let mut $i = $i;};
}

struct Dummy(i32);

impl Dummy {
    fn double(self) -> Dummy {
        make_mutable!(self);  // 这里传入的 self 和宏内部 let 定义的 self 不同
        self.0 *= 2;
        self
    }
}

fn main() {
    println!("{:?}", Dummy(4).double().0);
}
```

**【正例】**

```rust
macro_rules! double_method {
    ($self_:ident, $body:expr) => {
        fn double(mut $self_) -> Dummy {
            $body
        }
    };
}

struct Dummy(i32);

impl Dummy {
    double_method! {self, {
        self.0 *= 2;
        self
    }}
}

fn main() {
    println!("{:?}", Dummy(4).double().0);
}
```
