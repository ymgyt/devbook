# Deref

`*`演算子をオーバーロードするためのトレイト。 

```rust
impl<T: ?Sized> Deref for Rc<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.inner().value
    }
}
```

lifetime elision規則により、`defer`は`&'a Rc<T>`から`&'a T`を作る関数。

`Deref`が実装されていると、`*x`は`*Deref::dererf(&x)`に脱糖される。つまり

```rust
let rc = Rc::new(42);
let x = *rc;
```

は

```rust
let x = *Rc::deref(&rc);
//                ^^^ &Rc<i32>
//       ^^^^^^^^^^^^ &i32
//      ^^^^^^^^^^^^^ i32
```

のように脱糖される。