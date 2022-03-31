## P.NAM.04 作用域越大命名越精确，反之应简短

**【描述】**

1. 对于全局函数、全局变量、宏、类型名、枚举命名，应当精确描述并全局唯一。
2. 对于函数局部变量，或者结构体、枚举中的成员变量，在其命名能够准确表达含义的前提下，应该尽量简短，避免冗余信息重复描述。

**【反例】**

```rust
// 不符合：描述不精确
static GET_COUNT: i32 = 42;  

// 不符合：信息冗余
enum WebEvent {
    PageLoadEvent,
    PageUnloadEvent,
    KeyPressEvent(char),
    PasteEvent(String),
    ClickEvent { x: i64, y: i64 },
}

// 不符合：信息冗余
type MaskSize = u16; 
pub struct HeaderMap {
    mask: MaskSize,
}
```

**【正例】**

```rust
// 符合
static MAX_THREAD_COUNT: i32 = 42;  

// 符合： 上下文信息已经知道它是 Event
enum WebEvent {
    PageLoad,
    PageUnload,
    KeyPress(char),
    Paste(String),
    Click { x: i64, y: i64 },
}

// 符合：在使用它的地方自然就知道是描述谁的大小
type Size = u16; 
pub struct HeaderMap {
    mask: Size,
}
```
