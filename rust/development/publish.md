# publish

## Homebrew

```console
cargo build --release
cd target/release
tar -czf app.tar.gz app
shasum -a 256 app.tar.gz
```


f270b51c41dbc9e27c23f7c31fdd308b420ad3798f88b8845501625d12d6b51c  mysqlpinger.tar.gz
https://github.com/ymgyt/mysqlpinger/releases/download/v0.3.0/mysqlpinger-0.3.0-x86_64-apple-darwin.tar.gz