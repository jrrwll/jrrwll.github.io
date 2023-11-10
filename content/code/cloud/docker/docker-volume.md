
---
title: "Docker Volume"
---

## volume

```shell
ls -lah /var/lib/docker/volumes
```

## compose

### nfs

```yaml
version: '3'
volumes:
  some_data:
    driver_opts:
      type: "nfs"
      o: "addr=$NFS_ADDRESS,nolock,soft,rw,nfsvers=4"
      device: ":$NFS_DIR/some_data"
```
