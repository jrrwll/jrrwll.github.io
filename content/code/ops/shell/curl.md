
---
title: "curl"
---

## usage

```shell
curl -XPOST some_url \
    -F "userid=1" \
    -F "filecomment=This is an image file" \
    -F "image=@/home/user1/Desktop/test.jpg"
```

## share file with public network

```shell
curl bashupload.com -T your_file.txt
```

