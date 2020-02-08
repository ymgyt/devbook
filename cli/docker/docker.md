# Docker

[recipe](./recipe.md)



## commands

### stop

`docker stop`は、`SIGTERM`をPID1に送る。それから10秒(もしくは`--time`)待機して、`SIGKILL`を送る。



### kill

`docker kill`はdefaultでは、`SIGKILL`を送るが  
`docker kill --signal=SIGINT foo`のようにsignalを指定できる。j