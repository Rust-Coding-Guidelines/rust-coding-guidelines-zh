## P.GEN.01    用泛型来抽象公共语义

**【描述】**

应该巧用泛型来抽象公共语义，消除重复代码。

**【反例】**

```rust
use std::ops::Add;

#[derive(Debug, Clone, Copy)]
struct Meter {
    value: f64
}

impl Meter {
    fn new(value: f64) -> Self {
        Self { value }
    }
}

impl Add for Meter {
    type Output = Meter;

    fn add(self, another: Meter) -> Self::Output {
        let value = self.value + another.value;
        Meter { value }
    }
}

#[derive(Debug, Clone, Copy)]
struct Kilogram {
    value: f64
}

impl Kilogram {
    fn new(value: f64) -> Self {
        Self { value }
    }
}

impl Add for Kilogram {
    type Output = Kilogram;

    fn add(self, another: Kilogram) -> Self::Output {
        let value = self.value + another.value;
        Kilogram { value }
    }
}
fn main() {
    let one_meter = Meter::new(1.0);
    let two_kilograms = Kilogram::new(2.0);
    
    let two_meters = one_meter + one_meter;
}

```

**【正例】**

```rust
use std::ops::Add;
use std::marker::PhantomData;

#[derive(Debug, Clone, Copy)]
struct Unit<T> {
    value: f64,
    unit_type: PhantomData<T>,
}

impl<T> Unit<T> {
    fn new(value: f64) -> Self {
        Self {
            value,
            unit_type: PhantomData,
        }
    }
}

impl<T> Add for Unit<T> {
    type Output = Unit<T>;

    fn add(self, another: Unit<T>) -> Self::Output {
        let new_value = self.value + another.value;
        Unit::new(new_value)
    }
}

#[derive(Debug, Clone, Copy)]
struct MeterType;

#[derive(Debug, Clone, Copy)]
struct KilogramType;

type Meter = Unit<MeterType>;
type Kilogram = Unit<KilogramType>;
fn main() {
    let one_meter = Meter::new(1.0);
    let two_kilograms = Kilogram::new(2.0);
    
    let two_meters = one_meter + one_meter;
}
```