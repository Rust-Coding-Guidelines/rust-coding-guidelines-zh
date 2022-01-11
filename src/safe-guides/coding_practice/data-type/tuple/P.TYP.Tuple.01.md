## P.TYP.Tuple.01  宜使用元组解构来同时定义多个变量

**【描述】**

可以利用元组解构的特性，来更好地精简代码。

**【示例】**

```rust
struct A(i32, i32);

fn hello( A(a, b): A){
    println!("{}, {}", a, b);
}

fn main(){
    let a = A(1, 2) ;
    hello(a);
}
```
