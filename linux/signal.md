# Signal


## Usage

```console
# 一覧を表示
kill -l

# processにsignalを送信
kill -s <signal> <pid>
kill -s <signalID> <pid>
kill -<signalID> <pid>
kill -<SIG> <pid>
kill -SIG<signal> <pid>
kill <pid> # => pidを停止
```

defaultでは 15 SIGTERMが指定される  


## よく利用されるSignal

### `SIGHUP`

Hung upの意味。 Default ActionはTerminate. dialup modemからきている。  
daemons等のbackground processには設定fileの再読み込みを指示するのに利用される。  
shellからlogoutすると、running processに送られる。

Shortcut: `Ctrl+d`


### `SIGINT`

Interrupt(or Interactive)の意味。 Default ActionはTerminate.  
running processをterminateするためのuser-generatedなsignal.

Shortcut: `Ctrl+c`


### `SIGKILL`

handlingできない強制のTerminate.


### `SIGQUIT`

core dumpとしてmemoryをdiskに書き込んでから、Terminateする。

Shortcut: `Ctrl+\`


### `SIGTSTP`

Terminal Stop. terminalからprocessにstopを要求する。

Shortcut: `Ctrl+z`


### `SIGSTOP`

handlingできないstop.

### `SIGCONT`

Resume execution when paused.

## `kill -0`

```
man kill::
  If sig is 0, then no signal is sent, but error checking is still performed.
```

実際にはシグナルは送らないが、permission的にsignalがおくれるかをテストするために利用できる.  
アルファベットO(オー)ではなく数字のゼロ

```
sudo sleep 2500 &

pgrep -f sleep
# => 25772

kill -0 $(pgrep -f sleep)
# => kill: kill 25772 failed: operation not permitted

sudo kill -0 $(pgrep -f sleep)
```

capistranoのdeploy時にも利用されている

```
[ -e /usr/shared/tmp/pids/unicorn.pid ] && \
kill -0 `cat /usr/shared/tmp/pids/unicorn.pid`
```

signalID 0のシグナルは死活監視に利用できる


