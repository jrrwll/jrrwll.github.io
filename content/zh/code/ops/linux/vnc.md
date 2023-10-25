
---
title: "VNC"
---

## debian

### xrdp

```shell
sudo apt install xrdp
systemctl status xrdp

sudo adduser xrdp ssl-cert
sudo systemctl restart xrdp
# sudo ufw allow 3389
# sudo ufw allow from 1.2.3.4/32 to any port 3389
cat /etc/xrdp/xrdp.ini
ip a

# install xfce
sudo apt install task-xfce-desktop
# switch desktop
sudo update-alternatives --config x-session-manager


```

### vnc

```shell
sudo apt install tightvncserver -y

xhost +local:

# create a server :2
# ~/.vnc/xstartup
vncserver
touch ~/.Xresources
ps uax | grep vncserver
vncserver -kill :2
```

### Remote Desktop Manager

https://devolutions.net/remote-desktop-manager/

