# mem

## `transmute`

```rust
   let n: f32 = "42.42";
   let n_: u32 = unsafe { std::mem::transmute(n) };
```

引数の値のbit列を戻り値の型として解釈する。