
---
title: "Docker Image"
---

## search

```shell
# --limit int       Max number of search results (default 25)
# --no-trunc        Don't truncate output
docker search <something> 
```

## pull

```shell
docker pull debian
# daemon mode
container_id=`docker run -itd debian /bin/bash `
docker exec -it $container_id /bin/bash  
docker attach $container_id
```

## push

```shell
docker commit <container_id> <image_name>
docker tag <image_name> <username>/<imagename>:<tagname>
docker login -u <username> -p <password>
docker push <username>/<imagename>:<tagname>
docker logout
```

## tag

```shell
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

## commit

```shell
# Create a new image from a container's changes
docker commit <container_id> <image_name>
# -a, --author string   Author
# -m, --message string  Commit message

cid=$(docker run -e FOO=BAR <image>)
docker commit $cid
```

## images

```shell
# -a, --all         Show all images (default hides intermediate images)
# --digests         Show digests
# --no-trunc        Don't truncate output
# -q, --quiet       Only show numeric IDs
docker images
```

## rmi

```shell
# -f, --force
docker rmi [OPTIONS] IMAGE [IMAGE...]
```

## history

```shell
# Show the history of an image
# --no-trunc        Don't truncate output
docker history [OPTIONS] IMAGE
```

## save

```shell
# Save one or more images to a tar archive
docker save -o <tar_file_name> <image>
```

## load

```shell
# Load an image from a tar archive or STDIN。
docker load -i <tar_file_name>
```

## `dockertags.sh`

```shell
#!/bin/bash

if [ $# -lt 1 ]
then
cat << HELP

dockertags  --  list all tags for a Docker image on a remote registry.

EXAMPLE: 
    - list all tags for ubuntu:
       dockertags ubuntu

    - list all php tags containing apache:
       dockertags php apache

HELP
fi

image="$1"
tags=`wget -q https://registry.hub.docker.com/v1/repositories/${image}/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}'`

if [ -n "$2" ]
then
    tags=` echo "${tags}" | grep "$2" `
fi

echo "${tags}"
```
