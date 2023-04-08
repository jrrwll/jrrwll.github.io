
---
title: "systemctl"
---

## log

```shell
journalctl -u frps.service --no-pager --since "$(date '+%Y-%m-%d')"

journalctl -u frps.service --no-pager --since=yesterday
```
