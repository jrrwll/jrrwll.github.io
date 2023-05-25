
---
title: "SSH"
---

## install

```shell
# sshd or ssh
/etc/init.d/sshd restart
service ssh restart
systemctl restart sshd
```

## issue

```shell
sudo tail -f /var/log/secure
sudo sshd -t

# check selinux
sudo /usr/sbin/sestatus -v
sudo service firewalld status
```
 
#### debug3: receive packet: type 51

`-vvv` 输出 `debug3: receive packet: type 51` 表示服务器拒绝了公钥认证

```shell
ssh -vvv some_host

rm known_hosts.old
rm known_hosts
```

## `sshd.conf`

```conf
# 是否检查用户home目录和rhosts文件的权限和所有权
StrictModes no
```
