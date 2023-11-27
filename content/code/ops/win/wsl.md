
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

## config

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
