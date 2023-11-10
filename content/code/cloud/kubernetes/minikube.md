
---
title: "minikube"
---

## install

### mac

```shell
brew install minikube
minikube start --driver qemu --network socket_vmnet
# aliyun image speed up
minikube start --registry-mirror=https://xxx.mirror.aliyuncs.com --driver qemu --network socket_vmnet
```

**dedicated network**

```shell
# https://minikube.sigs.k8s.io/docs/drivers/qemu/#networking
brew install socket_vmnet
brew tap homebrew/services
sudo brew services start socket_vmnet
brew services info socket_vmnet
# or /opt/homebrew/var/log on Mac M1
cat /usr/local/var/log/socket_vmnet/stderr
cat /usr/local/var/log/socket_vmnet/stdout

# or compile from source
brew install go
git clone https://github.com/lima-vm/socket_vmnet.git && cd socket_vmnet
sudo make install
```

### kubectx & kubens

> https://github.com/ahmetb/kubectx
```shell
brew install kubectx
sudo apt install kubectx
```
