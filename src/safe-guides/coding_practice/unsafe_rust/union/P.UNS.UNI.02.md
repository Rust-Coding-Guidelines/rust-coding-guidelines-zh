## P.UNS.UNI.02  不要把联合体的不同变体用在不同生命周期内

**【描述】**

对联合体的变体进行借用的时候，要注意其他变体也将在同一个生命周期内。抛开内存布局、安全性和所有权之外，联合体的行为和结构体完全一致，你可以将联合体当做结构体来进行判断。

**【反例】**

```rust
// ERROR: cannot borrow `u` (via `u.f2`) as mutable more than once at a time
fn test() {
    let mut u = MyUnion { f1: 1 };
    unsafe {
        let b1 = &mut u.f1;
//                    ---- first mutable borrow occurs here (via `u.f1`)
        let b2 = &mut u.f2;
//                    ^^^^ second mutable borrow occurs here (via `u.f2`)
        *b1 = 5;
    }
//  - first borrow ends here
    assert_eq!(unsafe { u.f1 }, 5);
}
```
