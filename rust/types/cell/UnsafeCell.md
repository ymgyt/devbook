# UnsafeCell

```rust
#[lang = "unsafe_cell"]
#[stable(feature = "rust1", since = "1.0.0")]
#[repr(transparent)]
pub struct UnsafeCell<T: ?Sized> {
    value: T,
}
```

単なるTのWrapperなので、compilerに特別扱いされる点がポイント。