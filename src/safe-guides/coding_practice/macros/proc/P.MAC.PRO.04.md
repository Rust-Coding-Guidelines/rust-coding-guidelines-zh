## P.MAC.PRO.04 给出正确的错误位置

**【描述】**

过程宏发生错误时，返回的错误应该有正确的位置信息。

**【反例】**

```rust
// 直接用Span::call_site()
Error::new(Span::call_site(), "requires unit variant")
    .to_compile_error()
    .into()
```

**【正例】**

```rust
#[proc_macro_derive(MyMacro)]
pub fn derive_my_macro(input: TokenStream) -> TokenStream {
    let derive_input: DeriveInput = syn::parse_macro_input!(input as DeriveInput);

    if let Data::Enum(e) = &derive_input.data {
        for variant in &e.variants {
            if !variant.fields.is_empty() {
                // 使用variant的span
                return syn::Error::new_spanned(&variant, "must be a unit variable.")
                    .to_compile_error()
                    .into();
            }
        }
    }

    todo!()
}
```
