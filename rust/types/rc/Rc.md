# Rc

The Rc<T> type provides shared ownership of an immutable value.  
Destruction is deterministic, and will occur as soon as the last owner is gone.

`get_mut`は`Option<&mut T>`を返す。唯一の強参照だった場合にSomeになる。