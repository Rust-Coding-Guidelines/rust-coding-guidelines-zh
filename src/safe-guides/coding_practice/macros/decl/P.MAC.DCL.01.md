## P.MAC.DCL.01 不要将声明宏内的变量作为外部变量使用

**【描述】**

声明宏是半卫生（semi-hygienic）宏，其内部元变量（metavariables）不可作为外部变量去使用。但是对于泛型参数（包括生命周期参数）是不卫生的，所以要小心使用。

**【反例】**

下面为卫生场景示例：

```rust
macro_rules! using_a {
    ($e:expr) => {
        {
            let a = 42;
            $e
        }
    }
}

fn main() {
    let four = using_a!(a / 10); // build error:  cannot find value `a` in this scope
}
```

下面为不卫生场景示例：

```rust
trait FooTrait {
    fn get(&self) -> i32;
}

// 使用宏为带生命周期的类型实现 FooTrait
macro_rules! impl_FooTrait {
    ($name:ty) => {
        // 这里使用的 'a 是宏内部定义
        impl<'a> $crate::FooTrait for $name { 
            fn get(&self) -> i32 {
                *self.0
            }    
        }
    };
}

struct Baz<'a>(&'a i32);

impl_FooTrait!(Baz<'a>); // 这里的 'a 是宏外部


// 整个程序正常编译运行，说明宏内外的 'a 被共用，不卫生
fn main() {
    let val = 20;
    let baz = Baz(&val);
    method(&baz);
}

// 测试实现 FooTrait 的方法
fn method(foo: &dyn FooTrait) {
    println!("{:?}", foo.get());
}
```

**【正例】**

下面为卫生场景示例：

```rust
macro_rules! using_a {
    ($a:ident, $e:expr) => {{
        let $a = 42;
        $e
    }};
}

fn main() {
    let four = using_a!(a, a / 10);
}
```

下面为不卫生场景示例：

```rust
trait FooTrait {
    fn get(&self) -> i32;
}

// 使用宏为带生命周期的类型实现 FooTrait
macro_rules! impl_FooTrait {
    // 这里不直接使用宏内部定义的 'a ，而从外面通过 $lifetime 传入
    // 是为了避免不卫生可能引发的问题
    ($name:ty, $lifetime:tt) => {
        impl<$lifetime> $crate::FooTrait for $name { 
            fn get(&self) -> i32 {
                *self.0
            }    
        }
    };
}

struct Baz<'a>(&'a i32);

impl_FooTrait!(Baz<'a>, 'a); // 这里的 'a 是从宏外部传入到宏内


// 整个程序正常编译运行
fn main() {
    let val = 20;
    let baz = Baz(&val);
    method(&baz);
}

// 测试实现 FooTrait 的方法
fn method(foo: &dyn FooTrait) {
    println!("{:?}", foo.get());
}

```