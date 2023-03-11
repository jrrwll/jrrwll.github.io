
---
title: "Docker Build"
---

## build

```shell
docker build [OPTIONS] PATH | URL | -

# use Dockerfile in current path to build a image
docker build -t [repository]/[username]/[remote_image_name]:[tag] ./
# or
docker build -t [local_image_name]  ./
docker image tag [local_image_name] [repository]/[username]/[remote_image_name]:[tag]
# finally
docker push [repository]/[username]/[remote_image_name]:[tag]
```

- **-f, --file :** Name of the Dockerfile
- **--force-rm :**  Always remove intermediate containers
- **--no-cache :** Do not use cache when building the image
- **--pull :** Always attempt to pull a newer version of the image
- **--quiet, -q :** Only print image ID on success
- **--tag, -t:**  Name and optionally a tag in the 'name:tag' format
- **--network:**  Set the networking mode for the RUN instructions during build (default "default")

## Dockerfile

```dockerfile
ENV FOO=BAR
# cid=$(docker run -e FOO=BAR <image>)
# docker commit $cid
```

## `.dockerignore`

```ignore
.git
README.md
```
