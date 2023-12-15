
---
title: "Windows Subsystem Linux"
---

## install

https://learn.microsoft.com/zh-cn/windows/wsl/install-manual

```shell
wsl --install
```

```shell
# 启用适用于 Linux 的 Windows 子系统
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
# 启用虚拟机平台可选功能
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
# 更新最新版本的 WSL 2 Linux 内核
wsl --update
# 将 WSL 2 设置为默认版本
wsl --set-default-version 2
# 初始化安装好的发行版
# https://aka.ms/wsl-debian-gnulinux
debian
```

```shell
wsl
# wsl --terminate Ubuntu
wsl -t Debian
wsl --shutdown
wsl --list
```

## config

```shell
# 如果没有列出 zh_CN.utf8，则会出现中文乱码
locale -a
# sudo apt-get install language-pack-zh-hans
# 选中 en_US.utf8 和 zh_CN.utf8，然后设置默认 en_US.utf8
sudo dpkg-reconfigure locales
# 添加到 ~/.zshrc
export LC_ALL=en_US.UTF-8
```

```conf
Host *
    SendEnv LANG LC_*
```

### `~/.wslconfig`

```ini
# 网络桥接模式
[experimental]
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true
```

## ssh

```shell
# 重新安装openssh-server
sudo apt purge openssh-server
sudo apt install openssh-server

# 启动ssh
# sed -i 's/Port 22/Port 42122/' /etc/ssh/sshd_config
sudo service ssh start
service ssh status
sudo service ssh start
```

```shell
# 重启WSL
sudo net stop LxssManager
sudo net start LxssManager

# 转发到WSL2
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=22 connectaddress=$wsl2_ip connectport=22
# 添加防火墙入站规则
netsh advfirewall firewall add rule name=WSL2 dir=in action=allow protocol=TCP localport=22
ipconfig
ssh root@$win_ip
```

## gpu

https://developer.nvidia.com/cuda/wsl

**install cuda**

https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_network

```shell
# 在WSL2中安装CUDA
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get install cuda-12-3 cuda-toolkit-12-3 -y

# add to ~/.zshrc
export PATH="/usr/local/cuda-12.3/bin:$PATH"
export LD_LIBRARY_PATH="/usr/local/cuda-12.3/lib64:$LD_LIBRARY_PATH"

nvcc -V
```

**install cudnn**

https://developer.nvidia.com/rdp/cudnn-archive

```shell
sudo dpkg -i cudnn-local-repo-debian11-8.9.5.30_1.0-1_amd64.deb
sudo apt-get update
sudo apt install libcudnn8 libcudnn8-dev -y
```

**test gpu**

```python
## torch
import torch
torch.cuda.is_available()
torch.cuda.device_count()
torch.cuda.get_device_name(0)
## tensorflow
import tensorflow as tf
tf.config.list_physical_devices()
tf.config.list_physical_devices('GPU')
tf.test.gpu_device_name()
```
