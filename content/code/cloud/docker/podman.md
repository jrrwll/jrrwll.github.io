
---
title: "podman"
---

## install

```shell
brew install podman
brew install podman-compose

podman machine init
podman machine start
```

## config

### `/etc/containers/registries.conf`

```conf
# ~/.config/containers/registries.conf

unqualified-search-registries = ["docker.io"]

[[registry]]
prefix = "docker.io"
#location = "registry.docker-cn.com"
location = "xxxxxx.mirror.aliyuncs.com"
#insecure = true
```

### docker

`/etc/docker/daemon.json`

```json
{
     "registry-mirrors": [""]
}
```

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```


