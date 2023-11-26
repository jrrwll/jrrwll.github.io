
---
title: "Windows Subsystem Linux"
---

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
