# lazy_static

```rust
use lazy_static::lazy_static;

lazy_static! {
   static ref COUNTER: Mutex<usize> = Mutex::new(0);
}
```