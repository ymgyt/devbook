# memory ordering

```c++
namespace std {
 typedef enum memory_order {
   memory_order_relaxed,
   memory_order_consume,
   memory_order_acquire,
   memory_order_release,
   memory_order_acq_rel,
   memory_order_seq_cst
 } memory_order; 
}
```

* `order_acquire`

   gurantees that subsequent loads are not moved before the current load or any preceding loads.
   
   
* `order_release`

   preceeding store are not movoed past that current store or any subsequent stores.
   
   
* `order_relaxed`

   all reordering are okay.
   
   
   
 ## reference

* [Who ordered memory fences on an x86](https://bartoszmilewski.com/2008/11/05/who-ordered-memory-fences-on-an-x86/)
* [C++ atomics and memory ordering](https://bartoszmilewski.com/2008/12/01/c-atomics-and-memory-ordering/)