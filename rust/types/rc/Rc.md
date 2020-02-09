# Rc

The Rc<T> type provides shared ownership of an immutable value.  
Destruction is deterministic, and will occur as soon as the last owner is gone.

`get_mut`は`Option<&mut T>`を返す。唯一の強参照だった場合にSomeになる。

* `Rc`と`Weak`はどちらもheap領域に対するpointerで、heap領域にはstrong reference counter, week reference counter, 実態の3つかのっている。

## 基本後続

```rust
pub struct Rc<T: ?Sized> {
    ptr: NonNull<RcBox<T>>,
}

pub struct Weak<T: ?Sized> {
    ptr: NonNull<RcBox<T>>,
}

struct RcBox<T: ?Sized> {
    strong: Cell<usize>,
    weak: Cell<usize>,
    value: T,
}
```

## `Weak`

`Weak`の参照先は生きている保証がないので、`Deref`は実装されていない。明示的な`upgrade()`が必要。

## 状態共有
```rust
use std::rc::Rc;
use std::sync::{Arc, Mutex};
use std::thread;

// 状態共有
fn main() {
   let counter = Arc::new(Mutex::new(0));

    let thread = thread::spawn({
        let counter = counter.clone();
        move || {
            for _ in 0..10000 {
                let mut counter = counter.lock().unwrap();
                if *counter % 2 == 0 {
                    *counter += 1;
                }
            }
        }
    });

    for _ in 0..10000 {
        let mut counter = counter.lock().unwrap();
        if *counter % 2 == 1 {
            *counter += 1;
        }
    }

    thread.join().unwrap();

    let counter = *counter.lock().unwrap();
    eprintln!("counter = {}", counter);
}
```
