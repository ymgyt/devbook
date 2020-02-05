# RefCell

interior mutabilityを提供するための型。Tが`Copy`でない場合に利用する。(`Copy`ならCellを使えばよい)

`RefCell`は`borrow_mut()`で取得したpointerがscopeから落ちるまでで"locked"される。

内部的には、`Cell`を利用しているので、atomicでないため、`Cell`同様、multi threadでは使えない。


## multi thread

