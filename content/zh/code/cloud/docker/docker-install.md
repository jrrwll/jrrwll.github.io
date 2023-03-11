
---
title: "Docker Install"
---

## docker

### docker-ce

```shell
# https://docs.docker.com/engine/install/debian/
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release -y
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

sudo systemctl status docker

sudo usermod -aG docker $USER
```

### debian/ubuntu

```shell
sudo apt-get install docker.io
# order to perform docker without sudo prefix 
sudo usermod -aG docker $USER

sudo systemctl start docker

sudo docker run --rm hello-world

sudo apt-get autoremove --purge docker-io 
rm -rf /var/lib/docker
```

### centos/fedora

```shell
sudo dnf install docker
# 启动 Docker 服务
# sudo service docker start
sudo systemctl start docker
docker run --rm hello-world

docker version
docker info
# 为避免输入sudo，可以把用户加入 Docker 用户组
sudo groupadd docker
sudo usermod -aG docker $USER
```

