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

## dispatch

### static dispatch

```rust
trait Foo {
    fn method(&self) -> String;
}

impl Foo for u8 {
    fn method(&self) -> String { format!("u8: {}", *self) }
}

impl Foo for String {
    fn method(&self) -> String { format!("string: {}", *self) }
}

fn do_something<T: Foo>(x: T) {
    x.method();
}

fn main() {
    let x = 5u8;
    let y = "Hello".to_string();

    do_something(x);
    do_something(y);
}
```

この場合、RustがStringとu8それぞれに専用の関数を生成している。

```rust
fn do_something_u8(x: u8) {
    x.method();
}

fn do_something_string(x: String) {
    x.method();
}

fn main() {
    let x = 5u8;
    let y = "Hello".to_string();

    do_something_u8(x);
    do_something_string(y);
}
```

呼び出される関数はcompile時にわかっているので、関数呼び出しはインライン化できる。


### dynamic dispatch

trait objectとよばれる機能でRustは動的dispatchを提供している。  
`&Foo`や`Box<Foo>`のように記述され、指定されたtraitを実装するあらゆる型の値を保持する通常の値。