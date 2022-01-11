## P.MAC.Decl.01 不要将声明宏内的变量作为外部变量使用

  【描述】

声明宏是半卫生（semi-hygienic）宏，其内部元变量（metavariables）不可作为外部变量去使用。

但是对于泛型参数（包括生命周期参数）是不卫生的，所以要小心使用。

【正例】

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

【反例】

```rust
macro_rules! using_a {
    ($e:expr) => {
        {
            let a = 42;
            $e
        }
    }
}

let four = using_a!(a / 10); // build error:  cannot find value `a` in this scope
```
