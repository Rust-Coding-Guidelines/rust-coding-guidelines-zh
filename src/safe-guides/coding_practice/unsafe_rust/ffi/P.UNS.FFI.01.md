## P.UNS.FFI.01 避免从公开的 Rust API 直接传字符串到 C 中

**【描述】**

在跨越 C 边界的时候，应该对 字符串进行边界检查，避免传入一些非法字符串。

**【正例】**

这个示例中，从公开的 `Rust API` 传入非法字符串到 `C`，导致字符串格式化漏洞。 

```rust
// From: https://github.com/RustSec/advisory-db/issues/106

extern crate pancurses;

use pancurses::{initscr, endwin};

fn main() {
    let crash = "!~&@%+ S"; //  特意构造非法字符串

    let window = initscr();
    window.printw(crash); // 通过该函数跨 C 边界传入非法字符串，引起字符串格式化漏洞
    window.refresh();
    window.getch();
    endwin();
}
```
