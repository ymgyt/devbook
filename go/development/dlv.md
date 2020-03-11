# dlv

## usage

```console
# run
$ dlv debug internal/main.go

# breakpointを設定
# file pathはユニークならfile名だけでもよい
(dlv) b path/to/file.go

# breakpointには名前をつけられる
(dlv) b name server/server.go:10
(dlv) cond name a == "x"

# breakpoint一覧
(dlv) bp

# restart
(dlv) r
```

### codeにbreakpointを設定する

```go
package main

import "runtime"

func main() {
    runtime.Breakpoint()
}
```

dlvを実行してcontinueすればよい

## install

```console
go get -u github.com/go-delve/delve/cmd/dlv
```

