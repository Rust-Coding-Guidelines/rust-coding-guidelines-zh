## P.UNS.MEM.02  不能修改其它进程或动态库的内存变量

**【描述】**

除非调用合法的API，否则不要尝试修改其它进程/动态库的内存数据，否则会出现内存段错误(SIGSEGV)。

**【反例】**

`sqlite3_libversion()` 返回的 sqlite 版本信息指针指向 `/usr/lib/libsqlite3.so` 动态库的 static 字符串。

libsqlite3.so 中分配的静态字符串不属于进程的内存范围中。

当进程尝试修改 sqlite 动态库的静态字符串内容，操作系统就会发送 SIGSEGV 信号终止进程，以保证 sqlite 动态库的内存数据安全。

```rust
#[link(name = "sqlite3")]
extern "C" {
    fn sqlite3_libversion() -> *mut std::os::raw::c_char;
}

fn edit_sqlite_version() {
    unsafe {
        let mut sqlite_version = sqlite3_libversion();
        // SIGSEGV: invalid memory reference
        *sqlite_version = 3;
    }
}
```
