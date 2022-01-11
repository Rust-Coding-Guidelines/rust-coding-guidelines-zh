## P.MAC.Proc.02  实现过程宏时要对关键特性增加测试

【描述】

实现过程宏的时候，要对关键特性增加测试，这是为了避免出现关键特性遗漏的情况。 

【正例】

在第三方库 [zeroize](https://github.com/iqlusioninc/crates/tree/main/zeroize) 中，曾经因为过程宏中对枚举类型没有实现 Drop 而引起问题。增加关键性测试可以避免这类问题。

```rust
#[test]
fn zeroize_on_struct() {
    parse_zeroize_test(stringify!(
        #[zeroize(drop)]
        struct Z {
            a: String,
            b: Vec<u8>,
            c: [u8; 3],
        }
    ));
}

#[test]
fn zeroize_on_enum() {
    parse_zeroize_test(stringify!(
        #[zeroize(drop)]
        enum Z {
            Variant1 { a: String, b: Vec<u8>, c: [u8; 3] },
        }
    ));
}
```

【反例】

在第三方库 [zeroize](https://github.com/iqlusioninc/crates/tree/main/zeroize) 中，曾经因为过程宏中对枚举类型没有实现 Drop 而引起问题。参见：[RUSTSEC-2021-0115](https://rustsec.org/advisories/RUSTSEC-2021-0115.html)

```rust
#[derive(Zeroize)]
#[zeroize(drop)]
pub enum Fails {
    Variant(Vec<u8>),
}

// This does compile with zeroize_derive version 1.1, meaning `#[zeroize(drop)]` didn't implement `Drop`.
impl Drop for Fails {
    fn drop(&mut self) {
        todo!()
    }
}
```
