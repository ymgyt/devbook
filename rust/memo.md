# memo

## Interior mutability

Rustのmutは0/100なので、mutにしたら全fieldが対象になってしまう。基本的にimmutableだが、ここだけ変えたいというときに使う。  
the ability to mutate paticular fields in a structure regardless of its exterior mutability.

### usecase

* Introducing mutability inside of something immutable
* Mutating implementations of Clone
* Implementation details of logically immutable methods
* Mutating reference-counted variables


### multi thread corresponding

| single thread | multi thread |
| ------------- | -----------  |
| `Cell`        | `Atomic`     |
| `RefCell`     | `RwLock`     |
| `Rc`          | `Arc`        |


## `!Sync`

The presence of !Sync fields is “infectious”.  
Since Rc contains two Cell fields, their !Sync markers propagate to the entire Rc structure.  
Rc is also marked !Send — unsafe to send (move) to other threads.


## `?Sized`

```rust
struct Ref<T: ?Sized>
```

とすると、自動的につけられる`T: Sized`という制約を剥がすことができる。これによりTにはサイズ不定形がとれるようになる

* `Ref<dyn Fn() -> i32>`
* `Ref<dyn Any>`
* `Ref<str>`
* `Ref<[T]>`

## Raw pointers

reference = (raw pointer) + alien + borrow checking + lifetimes.  
Cのpointerと同じ。

`*const T`と`*mut T`がある。


## turbofish

```rust
fn generics<T>(t: T) {}

generics::<u8>()
```

型パラメータに型情報を渡している感じ。

```rust
pub trait Into<T> {
    fn into(self) -> T;
}

let s = Into::<String>::into("str");

// vecはこう
let v = Vec::<u32>::new();


pub enum Option<T> {
    None,
    Some(T),
}

// enumもいける
let o = Option::<String>::None;
```

型のほうに型パラメータがついている場合は上記のようにする。
