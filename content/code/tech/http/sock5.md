
---
title: "Sock5"
---

## install

### debian

```shell
apt install wget iproute2 procps -y

wget --no-check-certificate https://raw.github.com/Lozy/danted/master/install.sh -O install.sh
bash install.sh --port=8080 --user=NAME --passwd=PASSWORD
bash install.sh --uninstall

service sockd start
service sockd tail
service sockd adduser NAME PASSWORD
service sockd deluser NAME

curl --proxy 'socks5h://127.0.0.1:8080' --proxy-user NAME:PASSWORD 'https://api.ipify.org/'
```

**/etc/danted/sockd.conf**

```conf
internal: 127.0.0.1 port = 8080
external: eth0

client pass {
    from: 0.0.0.0/0  to: 0.0.0.0/0
}
```
