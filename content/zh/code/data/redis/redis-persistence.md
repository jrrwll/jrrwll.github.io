
---
title: "Redis Persistence"
---

## persistence

### RDB

fork sub process  periodically and dump to a single file

### AOF, appended only file

log every writer and delete records

```conf
# after 300s, dump if at least 10 key changed
save 300 10

# aof, always no
# every second
appendfsync everysec

# grow 100% then rewrite
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

```

