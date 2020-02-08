# Multi thread

## 型ごとの役割

|                   | Single thread | Multi threads      |
| ----------------  | ------------  | -------------      |
| Copy              | `Cell`        | `Atomic*`          |
| Non-Copy          | `RefCell`     | `RwLock`, `Mutext` |
| Reference counter | `Rc`          | `Arc`              |


## 参照の取得

| Type of access       | Borrow checker | RefCell      | RwLock  | Mutex  |
| --------------       | -------------  | -------      | ------  | ------ |
| shared / read-only   | `&T`           | `borrow`     | `read`  | -      |
| exclusive / writable | `&mut T`       | `borrow_mut` | `write` | `lock` |