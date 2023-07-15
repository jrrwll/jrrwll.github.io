
---
title: "systemctl"
---

## status

```shell
# or systemctl list-units --type=service
systemctl --type=service
pstree

cat /lib/systemd/system/kadmin.service
```

## log

```shell
journalctl -u frps.service --no-pager --since "$(date '+%Y-%m-%d')"

journalctl -u frps.service --no-pager --since=yesterday
```
