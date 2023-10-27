---
title: "Docker Compose"
---

## install

```shell
curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-Linux-x86_64" -o docker-compose

VERSION=$(curl https://api.github.com/repos/docker/compose/releases/latest | jq .name -r)
sudo curl -L "https://github.com/docker/compose/releases/download/${VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

## config

### run

**--sysctl net.core.somaxconn=65535**

```yaml
version: '3'
  services:
    sysctls:
      net.core.somaxconn: 1024
``` 

```shell
sysctl -w net.ipv4.tcp_tw_recycle=1
```

**--add-host example.com:127.0.0.1**

```yaml
version: '3'
  services:
    extra-hosts:
      - example.com:127.0.0.1
```

### volume
```yaml
# volumes:
# - <宿主机目录>:<容器目录>
volumes:
- /host_path:/container_path
```
