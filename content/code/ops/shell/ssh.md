
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

## ssh

```shell
chmod 755 ~/.ssh
cd ~/.ssh
cd 644 rsa_id.pub authorized_keys
cd 600 rsa_id
```

```conf
# ~/.ssh/config
host *
    AddKeysToAgent yes
    UseKeychain yes
    TCPKeepAlive yes
    ServerAliveInterval 60
    ServerAliveCountMax 5
host github.com
  user git
  hostname github.com
  port 22
  identityfile ~/.ssh/github/id_rsa
  identitiesOnly yes
```

## `sshd.conf`

```conf
# 是否检查用户home目录和rhosts文件的权限和所有权
StrictModes no
```

## sshfs

```shell
sudo apt install sshfs
# -p 22
# -o allow_other,IdentityFile=~/.ssh/id_rsa
sshfs $user@$remote_ip:$remote_dir $local_dir

umount $local_dir
```

```shell
# or download from https://osxfuse.github.io/
brew install macfuse
brew install sshfs
```

## issue

```shell
sudo tail -f /var/log/secure
sudo sshd -t

# check selinux
sudo /usr/sbin/sestatus -v
sudo service firewalld status
```

### debug3: receive packet: type 51

`-vvv` 输出 `debug3: receive packet: type 51` 表示服务器拒绝了公钥认证

```shell
ssh -vvv some_host

rm known_hosts.old
rm known_hosts
```
