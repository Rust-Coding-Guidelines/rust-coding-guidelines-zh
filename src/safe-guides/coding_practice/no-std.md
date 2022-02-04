# no-std 

`no-std` 是指 被标示为 `#![no_std]` 的 crate，意味着该 crate 将链接到 `core` crate 而非 `std` crate。

`no-std`  代表 裸机编程，嵌入式 Rust。

Rust 也有 `#![no_core]` 属性，但是还未稳定，不建议使用。

>  参考数据： `core` 在编译后文件大小中只占大约 3k 大小。
