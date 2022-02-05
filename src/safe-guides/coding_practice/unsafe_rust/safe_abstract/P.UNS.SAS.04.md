## P.UNS.SAS.04  要考虑 Panic Safety 的情况

**【描述】**

要注意 Panic Safety 的情况，避免双重释放（double free）的问题发生。

在使用 `std::ptr` 模块中接口需要注意，容易产生 UB 问题，要多多查看 API 文档。

**【反例】**

```rust
//case 1
macro_rules! from_event_option_array_into_event_list(
    ($e:ty, $len:expr) => (
        impl<'e> From<[Option<$e>; $len]> for EventList {
                fn from(events: [Option<$e>; $len]) -> EventList {
                    let mut el = EventList::with_capacity(events.len());
                    for idx in 0..events.len() {
                    // 这个 unsafe 用法在 `event.into()`调用 panic 的时候会导致双重释放
                        let event_opt = unsafe { ptr::read(events.get_unchecked(idx)) };
                        if let Some(event) = event_opt { el.push::<Event>(event.into()); }
                    }
                    // 此处 mem::forget 就是为了防止 `dobule free`。
                    // 因为 `ptr::read` 也会制造一次 drop。
                    // 所以上面如果发生了 panic，那就相当于注释了 `mem::forget`，导致 `dobule free`
                    mem::forget(events);
                    el
                }
        }
    )
);
```

**【正例】**

 ```rust
 macro_rules! from_event_option_array_into_event_list(
     ($e:ty, $len:expr) => (
         impl<'e> From<[Option<$e>; $len]> for EventList {
             fn from(events: [Option<$e>; $len]) -> EventList {
                 let mut el = ManuallyDrop::new(
                     EventList::with_capacity(events.len())
                 );
 
                 for idx in 0..events.len() {
                     let event_opt = unsafe {
                         ptr::read(events.get_unchecked(idx))
                     };
 
                     if let Some(event) = event_opt {
                         // Use `ManuallyDrop` to guard against
                         // potential panic within `into()`.
                         // 当 into 方法发生 panic 当时候，这里 ManuallyDrop 可以保护其不会`double free`
                         let event = ManuallyDrop::into_inner(
                             ManuallyDrop::new(event)
                             .into()
                         );
                         el.push(event);
                     }
                 }
                 mem::forget(events);
                 ManuallyDrop::into_inner(el)
             }
         }
     )
 );
 ```
