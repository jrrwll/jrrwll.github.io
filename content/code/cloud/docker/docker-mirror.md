
---
title: "Docker Mirror"
---

## mirror

### dockerproxy

https://dockerproxy.net/

```shell
function dockerproxy() {
    image_name=$1
    if [ -z $image_name ]; then echo 'require a image name' && return 1; fi
    
    docker pull dockerproxy.net/$image_name && \
        docker tag dockerproxy.net/$image_name $image_name && \
        docker rmi dockerproxy.net/$image_name
}
```
