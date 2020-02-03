# grpcurl

## requestをなげる

```bash
$  grpcurl \
     -plaintext \
     -import-path=vendor \
     -import-path=. \
     -proto=path/to/service.proto \
     localhost:99999 my.domain.service.Service.Method
```

