
---
title: "fdisk"
---

## fdisk

```sh
# 查看分区
df -lh
lsblk
fdisk -l
# 磁盘分区
fdisk /dev/sdb

# mkfs -t ext4 /dev/sdb1
mkfs.ext4 /dev/sdb1
mount /dev/sdb1 /my_mount1

mkfs.xfs /dev/sdb2
```

**fdisk**

- m 显示帮助菜单
- n 新建分区
- p 显示分区列表
- q 不保存退出
- w 保存退出
