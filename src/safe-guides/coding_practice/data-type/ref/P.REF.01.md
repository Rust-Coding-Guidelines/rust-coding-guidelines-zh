## P.REF.01  使用引用的时候要注意其生命周期不要重合

**【描述】**

在使用 引用的时候，要注意分析其生命周期，不可变借用和可变借用之间，以及可变借用之间不要有重叠。

**【反例】**

```rust
fn main(){
    let mut s = String::from("hello");
    // r1 是不可变借用，其生命周期和 可变借用 r3 重叠，所以会出问题
    let r1 = &s; // no problem    ---------------- lifetime r1 start
    let r2 = &mut s; // no problem
    let r3 = &mut s; // BIG PROBLEM -------------- lifetime r3 start 

    println!("{}, {}, and {}", r1, r2, r3);  //  lifetime r1, r2, r3  end; 
    
}
```

**【正例】**

```rust
fn main(){
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &mut s; // no problem
    let r3 = &mut s; // no PROBLEM

    // println!("{}, {}, and {}", r1, r2, r3);
    
}
```


