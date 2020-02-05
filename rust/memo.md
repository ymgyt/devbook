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