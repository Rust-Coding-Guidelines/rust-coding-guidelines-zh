# P.UNS.FFI.16 给 C 接口传递 Rust 闭包时需要将数据和代码进行分离，并要保证其安全性

**【描述】**

在 Rust 中，闭包只是一种语法糖，其实质是由编译器生成的匿名结构体和一些 `call`方法组成。而 C 语言中只支持函数指针，并不支持 Rust 这种闭包。

因此为 C 接口传递 Rust 闭包的思路是将闭包“拆分”为数据（匿名结构体的实例）和函数（`call()`方法）部分来将闭包传递给 C 接口，并且要从以下三方面保证安全性：

1. Rust 闭包中捕获变量引用的生命周期有效性。
2. 传入的Rust 闭包要实现 `std::panic::UnwindSafe`，这样可以保证异常安全
3. 传入的 Rust 闭包要实现 `Send`，这样可以保证线程安全


**【正例】**

```rust
// Safety: 此处需要保证以下不变性来进行安全抽象
// - widget 必须是一个有效的指针
//    - 这里因为使用 Rust 引用，所以它一定是有效的
// - 数据必须在其被析构之前有效
//     - 这里增加了`'static` 限定来确保它有效
// - 增加 `std::panic::UnwindSafe` 和 `Send` 限定，确保其异常安全和线程安全
fn register_c_callback<F>(widget: &mut ffi::widget_t, callback: F)
where
    F: FnMut(ffi::event_t) + 'static + std::panic::UnwindSafe + Send,
{
    // 需要闭包实例数据保留一定时间，所以将其放到堆上，使用`Box::into_raw`防止其被析构 
    let data = Box::into_raw(Box::new(callback)); 
    unsafe {
        // 分别将 数据 和 闭包调用代码 传入 C 接口
        ffi::widget_register_callback(
            widget,
            data as *mut _, // 数据
            call_closure::<F>, // 代码
            drop_box::<F>,
        );
    }
}

// Safety: 传入该函数的指针必须是 `F` 类型的非空指针
// 这里不需要 `#[no_mangle]`，是因为它会以函数指针的方式直接传递给 C ，而不需要通过函数名称调用
unsafe extern "C" fn call_closure<F>(
    data: *mut libc::c_void,
    event: ffi::event_t,
)
where
    F: FnMut(ffi::event_t) + 'static + std::panic::UnwindSafe + Send,
{
    let callback_ptr = data as *mut F;
    let callback = &mut *callback_ptr;
    callback(event); // 调用闭包
}

// 在 C 端手动调用的析构函数
unsafe extern "C" fn drop_box<T>(data: *mut libc::c_void) {
    Box::from_raw(data as *mut T);
}
```