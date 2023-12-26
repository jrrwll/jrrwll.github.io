
---
title: "qbittorrent"
---

## install

### linux

```shell
sudo apt install qbittorrent-nox
```

```shell
# 启动一个webserver来监听下载进度，账密：admin/adminadmin
# --webui-port=8080
qbittorrent-nox -d --webui-port=1111

qbittorrent-nox torrent_file_or_url

tail -100f ~/.local/share/qBittorrent/logs/qbittorrent.log
```
