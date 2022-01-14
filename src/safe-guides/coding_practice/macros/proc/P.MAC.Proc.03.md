## P.MAC.Proc.03 保证过程宏的卫生性

**【描述】**

过程宏生成的代码尽量使用完全限定名，防止命名冲突产生意想不到的后果。

可以使用 `#![no_implicit_prelude]` 属性来验证过程宏的卫生性。

```rust
#![no_implicit_prelude]

#[derive(MyMacro)]
struct A;
```

**【反例】**

```rust
quote!(a.to_string())
```

**【正例】**

```rust
quote!(std::string::ToString::to_string(a))
```

```rust
quote! {{
    use std::string::ToString;
    a.to_string()
}}
```
