# trait 对象

trait 对象需要注意 动态安全 （dyn safe），也叫对象安全 （object safe），但官方现在倾向于 动态安全这个术语。

---
<!-- toc -->
---

## P.TRA.Object.01  除非必要，避免使用 trait对象

**【描述】**

trait 对象存在一定运行时开销，除非必要，不要滥用，但 triat 对象也可以避免编译文件大小膨胀。

在性能有严格要求的情况下，可以考虑 `Enum` 或 泛型静态分发 代替。

**【正例】**

使用 Enum 代替 trait 对象。 示例来自于 [enum_dispatch](https://docs.rs/enum_dispatch/0.3.7/enum_dispatch/)

```rust
trait KnobControl {
    fn set_position(&mut self, value: f64);
    fn get_value(&self) -> f64;
}

struct LinearKnob {
    position: f64,
}

struct LogarithmicKnob {
    position: f64,
}

impl KnobControl for LinearKnob {
    fn set_position(&mut self, value: f64) {
        self.position = value;
    }

    fn get_value(&self) -> f64 {
        self.position
    }
}

impl KnobControl for LogarithmicKnob {
    fn set_position(&mut self, value: f64) {
        self.position = value;
    }

    fn get_value(&self) -> f64 {
        (self.position + 1.).log2()
    }
}

fn main() {
    // 这里使用 trait 对象
    let v: Vec<Box<dyn KnobControl>> = vec![
        //set the knobs
    ];

    //use the knobs
}
```

用 Enum 代替：

```rust
enum Knob {
    Linear(LinearKnob),
    Logarithmic(LogarithmicKnob),
}

impl KnobControl for Knob {
    fn set_position(&mut self, value: f64) {
        match self {
            Knob::Linear(inner_knob) => inner_knob.set_position(value),
            Knob::Logarithmic(inner_knob) => inner_knob.set_position(value),
        }
    }

    fn get_value(&self) -> f64 {
        match self {
            Knob::Linear(inner_knob) => inner_knob.get_value(),
            Knob::Logarithmic(inner_knob) => inner_knob.get_value(),
        }
    }
}
```

性能有显著提高，但是牺牲了维护成本。可以借助于宏来自动生成相关代码，参见： [enum_dispatch](https://docs.rs/enum_dispatch/0.3.7/enum_dispatch/)

## P.TRA.Object.02  除非必要，避免自定义虚表

**【描述】**

trait 对象 `dyn Trait` 隐藏了复杂而又为危险的虚表实现，为我们提供了简单而又安全的动态分发。手动实现虚表的代码中充斥着大量的 `unsafe`，稍有不慎，就会引入 bug 。如无必要，不要自定义虚表。

如果你的设计不能使用标准的 `dyn Trait` 结构来表达，那么你首先应该尝试重构你的程序，并参考以下理由来决定是否使用自定义的虚表。

- 你想要为一类指针对象实现多态，并且无法忍受多级指针解引用造成的性能开销，参考 [RawWaker](https://doc.rust-lang.org/std/task/struct.RawWaker.html) 与 [Bytes](https://docs.rs/bytes/1.1.0/bytes/struct.Bytes.html)。
- 你想要自定义内存布局，比如像 C++ 中虚表一样紧凑的内存结构（虚表指针位于对象内），参考 [RawTask](https://github.com/tokio-rs/tokio/blob/master/tokio/src/runtime/task/raw.rs#L12)。
- 你的 crate 需要在 `no_std` 环境中使用动态分发，参考 [RawWaker](https://doc.rust-lang.org/std/task/struct.RawWaker.html)  。
- 或者，标准的 trait object 确实无法实现你的需求。

