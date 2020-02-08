# attribute

## `doc`

```rust
/// this is a doc comment.
#[doc = " This is a doc comment."]
```

`///`は`#[doc]`のsyntax sugar.

## `lint`

```rust
#![allow(missing_docs)] // allowing the lint, no message
#![warn(missing_docs)] // warn if there is missing docs
#![deny(missing_docs)] // rustdoc will fail if there is missing docs
```

## `stable`

```rust
#[stable(feature = "rust1", since = "1.0.0")]
```

compilerにどのchannelで利用できるかを指示している。

## `lang`

```rust
#[lang = "unsafe_cell"]
```




