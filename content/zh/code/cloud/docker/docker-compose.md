---
title: "Docker Compose"
---

## install

### debian9

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

sudo usermod -aG docker $USER

sudo systemctl status docker.service
sudo systemctl restart docker.service
```

## config

```yaml
# volumes:
# - <宿主机目录>:<容器目录>
volumes:
- /host_path:/container_path
```
