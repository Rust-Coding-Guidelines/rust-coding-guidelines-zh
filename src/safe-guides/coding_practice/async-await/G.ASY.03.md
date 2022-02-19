## G.ASY.03 在跨 `await` 调用中，需要对其持有 `RefCell` 的引用进行处理

**【级别】** 建议

**【描述】**

与[上条规则](./G.ASY.02.md)类似，使用 `RefCell` 的独占（可变）借用会导致 Panic。因为 `RefCell` 是运行时检查独占的可变访问，如果跨 `await` 持有一个可变引用则可能会因为共享的可变引用而引起 Panic。

这种共享可变在编译期是无法被检查出来的。

**【反例】**

```rust
use std::cell::RefCell;

async fn foo(x: &RefCell<u32>) {
  let mut y = x.borrow_mut();
  *y += 1;
  bar.await;
}
```

**【正例】**

```rust
use std::cell::RefCell;

async fn foo(x: &RefCell<u32>) {
  {
    let mut y = x.borrow_mut();
    *y += 1;
  }
  bar.await;
}
```

**【例外】**

跨 `await` 持有 `RefCell` 的可变借用，但是当前场景确信永远不会 Panic，则可以使用。

用例来源：[wasm-streams](https://github.com/MattiasBuelens/wasm-streams/blob/dff05d77513cc1d590c21cd251a63b43cf520fed/src/readable/into_underlying_byte_source.rs#L65)

```rust
pub fn pull(&mut self, controller: sys::ReadableByteStreamController) -> Promise {
  let inner = self.inner.clone();
  let fut = async move {
    // This mutable borrow can never panic, since the ReadableStream always queues
    // each operation on the underlying source.
    // 这个可变借用永远不会恐慌，因为 ReadableStream 对底层源的每个操作总是有序的。
    let mut inner = inner.try_borrow_mut().unwrap_throw();
    inner.pull(controller).await
  };
  // ...
}
```

**【Lint 检测】**

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group | level |
| ------------------------------------------------------------ | ------------- | ------------ | ---------- | ----- |
| [await_holding_refcell_ref](https://rust-lang.github.io/rust-clippy/master/#await_holding_refcell_ref) | yes           | no           | pedantic   | allow |
