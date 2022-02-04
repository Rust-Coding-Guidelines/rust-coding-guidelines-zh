## P.TRA.OBJ.01  根据场景合理选择使用trait对象或泛型静态分发

**【描述】**

trait对象存在一定运行时开销，除非必要，不要滥用，但triat对象也可以避免编译文件大小膨胀。

在性能有严格要求的情况下，可以考虑`Enum`或泛型静态分发代替。

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