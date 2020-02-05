# Questions

## `AsRef<T>`と`Borrow<T>`の違い

referenceに変換したいときはAsRef, 値がownedかborrowedかの違いを無視したいときはBorrow(hash値が同じとか)


## `impl T + 'static`の意味

So as a summary, a constraint like  "F: ‘static”  means that either:
* F has no lifetime parameter
* all lifetime parameters of F are ‘static

https://stackoverflow.com/questions/30499892/what-is-the-meaning-of-static-as-a-function-constraint/30500072#30500072
