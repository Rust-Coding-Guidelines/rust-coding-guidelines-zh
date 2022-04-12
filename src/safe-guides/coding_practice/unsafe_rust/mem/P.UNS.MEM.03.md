## P.UNS.MEM.03  不能让 `String/Vec` 自动 Drop 其它进程或动态库的内存数据

**【描述】**

使用 `String/Vec` 指向其它进程/动态库的内存数据时，一定要手动禁止 `String/Vec` 的 Drop 方法(析构函数)的调用，避免 free 其它进程/动态库的内存数据。

**【反例】**

`sqlite3_libversion()` 返回的 sqlite 版本信息指针指向 `/usr/lib/libsqlite3.so` 动态库的 static 字符串。

当进程在 `String` drop 的时候尝试释放 sqlite 动态库的静态字符串内存时，操作系统就会发送 SIGABRT 信号终止进程，以保证 sqlite 动态库的内存数据安全。

```rust
#[link(name = "sqlite3")]
extern "C" {
    fn sqlite3_libversion() -> *mut std::os::raw::c_char;
}

fn print_sqlite_version() {
    unsafe {
        let ptr = sqlite3_libversion();
        let len = libc::strlen(ptr);
        let version = String::from_raw_parts(ptr.cast(), len, len);
        println!("found sqlite3 version={}", version);
        // SIGABRT: invalid free
    }
}
```

**【正例】**

除了用 `mem::forget` 或者 `ManualDrop` 禁止 `String` drop 其它动态库的内存，也可以用标准库 `ptr/slice` 的 `copy` 或者 `libc::strdup` 将 sqlite 的版本信息字符串**复制到当前进程的内存空间**再进行操作

```rust
fn print_sqlite_version() {
    unsafe {
        let ptr = sqlite3_libversion();
        let len = libc::strlen(ptr);
        let version = String::from_raw_parts(ptr.cast(), len, len);
        println!("found sqlite3 version={}", version);
        // 手动禁止 String 的析构函数调用
        std::mem::forget(version);
    }
}
```
