# Cell

interior mutabilityを提供するための型。RefCellと違い、runtime borrow checkを提供しない。

`get`と`set`でCellのinner valueのread/writeができる。

```rust
use std::cell::Cell;

fn foo(cell: &Cell<u32>) {
    let value = cell.get();
    cell.set(value * 2);
}

fn main() {
    let cell = Cell::new(0);
    let value = cell.get();
    let new_value = cell.get() + 1;
    foo(&cell);
    cell.set(new_value); // oops, we clobbered the work done by foo
}
```

## multi thread

operationはatomicではない。o
