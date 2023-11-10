
---
title: "SSL Cert"
---

## dev

### mac

```shell
brew install mkcert
mkcert -install

# 在当前目录生成域名对应ip的证书
# -key.pem 和 .pem
mkcert localhost 127.0.0.1 ::1 192.168.1.2
```