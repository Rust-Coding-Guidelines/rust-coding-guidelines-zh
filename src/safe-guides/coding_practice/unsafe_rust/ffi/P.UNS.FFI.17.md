# P.UNS.FFI.17 当Rust绑定 C-API 不透明(Opaque)类型时，应该使用指向专用不透明类型的指针而不是`c_void`指针

## 【描述】

使用专门构建的不透明类型相比于直接使用 `c_void`可以提供一定程度的类型安全性。

## 【正例】

C 库中包含了一个不透明类型的 foo 指针和 bar 指针：

```c
void foo(void *arg);
void bar(void *arg);
```

通过包含私有字段`_private`且不包含构造函数，创建了两个无法在此模块之外实例化的不透明类型。空数组既是零大小又可设置布局为`#[repr(C)]`。

```rust
#[repr(C)]
pub struct Foo {_private: [u8; 0]}

#[repr(C)]
pub struct Bar {_private: [u8; 0]}


// SAFETY: 
//  因为 Foo 和 Bar类型不同，所以将在它们两者之间获得类型安全，这样就不可能意外地传递一个指向 `bar()` 的`Foo`指针。
extern "C" {
    fn foo(arg: *mut Foo); 
    fn bar(arg: *mut Bar);
}
```