
---
title: "Consul"
---

## install

```shell
docker run -d --name=consul1 \
    -p 8500:8500 \
    -e CONSUL_BIND_INTERFACE=eth0 \
    consul
docker run -d --name consul2 -e CONSUL_BIND_INTERFACE=eth0 \
    consul agent -dev -join=172.17.0.2

# query for all the members in the cluster
docker exec -t consul consul members

curl http://localhost:8500/v1/health/service/consul?pretty
```
