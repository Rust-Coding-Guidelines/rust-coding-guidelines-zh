## P.ERR.02  在确定  `Option<T>` 和 `Result<T, E>`类型的值不可能是 `None` 或 `Err` 时，请用  `expect` 代替  `unwrap()`

**【描述】**

当需要处理的   `Option<T>` 和 `Result<T, E>` 类型的值，永远都不可能是 `None` 或 `Err` 时，虽然直接 `unwrap()` 也是可以的，但使用 `expect` 会有更加明确的语义。

> `expect` 的语义： 
>
> 我不打算处理 `None` 或 `Err` 这种可能性，因为我知道这种可能性永远不会发生，或者，它不应该发生。但是 类型系统并不知道它永远不会发生。所以，我需要像类型系统保证，如果它确实发生了，它可以认为是一种错误，并且程序应该崩溃，并带着可以用于跟踪和修复该错误的栈跟踪信息。

所以在指定 `expect` 输出消息的时候，请使用肯定的描述，而非否定，用于提升可读性。

**【反例】**

```rust
// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic ，但这里并没有提供错误信息，对于调试或使用都没有帮助
let config = Config::read("some_config.toml").unwrap();

// or
// expect 的输出描述使用否定式内容，可读性不好
let config = Config::read("some_config.toml").expect("No configuration file provided"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().unwrap();
}

// or
// expect 的输出描述使用否定式内容，可读性不好
fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("IP addr parse failed!");
}
```

**【正例】**

```rust
// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic 并提供错误信息方便调试，或者让使用者知道原因
// expect 里输出的描述信息，使用肯定的内容，整体代码可读性更高，更能突出 expect 的语义
let config = Config::read("some_config.toml").expect("Provide the correct configuration file"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("Provide the correct Ip addr");
}
```

