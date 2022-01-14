## P.EMB.01  `no-std` 下必须定义一个Panic行为以确保安全

**【描述】**

鉴于`#![no_std]`应用程序没有标准输出，并且某些`#![no_std]`应用程序（例如嵌入式应用程序）需要不同的 Panic 行为来进行开发和发布。

因此，可以通过属性宏`#[panic_handler]`来定义 Panic 行为。

**【正例】**

定义 `panic-semihosting` Crate，将 Panic 消息记录到 Host 的 stderr :

```rust
#![no_std]

use core::fmt::{Write, self};
use core::panic::PanicInfo;

struct HStderr {
    // ..
}

#[panic_handler]
fn panic(info: &PanicInfo) -> ! {
    let mut host_stderr = HStderr::new();
    
    // logs "panicked at '$reason', src/main.rs:27:4" to the host stderr
    writeln!(host_stderr, "{}", info).ok();

    loop {}
}
```

定义 `panic-halt` Crate，将 Panic 消息丢弃。

```rust
#![no_std]
use core::panic::PanicInfo;

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

在 `app` Crate 中， Debug 和 Release 编译模式调用不同的 Panic 行为。

```rust
#![no_std]

// dev profile
#[cfg(debug_assertions)]
extern crate panic_semihosting;

// release profile
#[cfg(not(debug_assertions))]
extern crate panic_halt;

fn main() {
    // ..
}
```