
---
title: "Redis Deploy"
---

## container

```shell
docker run -it -d --name redis -p 6379:6379 redis
```

## sentinel

### localhost

```shell
config set protected-mode no
```

### multi master (3 master, 3 slave, and 3 sentinel)

```shell
cat << EOF > redis.conf
port 7000
daemonize yes
cluster-enabled yes
cluster-config-file 7000/nodes.conf
cluster-node-timeout 5000
appendonly yes
EOF

mkdir 7000 7001 7002 7003 7004 7005

cat redis.conf | sed s/7000/7000/g > 7000/redis.conf
cat redis.conf | sed s/7000/7001/g > 7001/redis.conf
cat redis.conf | sed s/7000/7002/g > 7002/redis.conf
cat redis.conf | sed s/7000/7003/g > 7003/redis.conf
cat redis.conf | sed s/7000/7004/g > 7004/redis.conf
cat redis.conf | sed s/7000/7005/g > 7005/redis.conf

export redis_server='../redis/src/redis-server'
$redis_server 7000/redis.conf 
$redis_server 7001/redis.conf 
$redis_server 7002/redis.conf 
$redis_server 7003/redis.conf 
$redis_server 7004/redis.conf 
$redis_server 7005/redis.conf 
# three master & three slave
../redis/src/redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 \
127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005

```

```shell
cat << EOF > sentinel.conf
port 26379
daemonize yes

sentinel monitor mymaster1 127.0.0.1 %port1 2
sentinel down-after-milliseconds mymaster1 60000
sentinel failover-timeout mymaster1 180000
sentinel parallel-syncs mymaster1 1

sentinel monitor mymaster2 127.0.0.1 %port2 2
sentinel down-after-milliseconds mymaster2 60000
sentinel failover-timeout mymaster2 180000
sentinel parallel-syncs mymaster2 1

sentinel monitor mymaster3 127.0.0.1 %port3 2
sentinel down-after-milliseconds mymaster3 60000
sentinel failover-timeout mymaster3 180000
sentinel parallel-syncs mymaster3 1
EOF

cat sentinel.conf | sed s/26379/26379/g | sed 's/%port1/1/g' > sentinel_0.conf
cat sentinel.conf | sed s/26379/36379/g | sed 's/%port2//g' > sentinel_1.conf
cat sentinel.conf | sed s/26379/46379/g | sed 's/%port3//g' > sentinel_2.conf

export redis_server='../redis/src/redis-server'
$redis_server sentinel_0.conf --sentinel
$redis_server sentinel_1.conf --sentinel
$redis_server sentinel_2.conf --sentinel
```

### single master(1 master, 5 slave, and 3 sentinel)

```shell
cat << EOF > redis.conf
port 7000
daemonize yes
protected-mode no
# cluster-enabled yes
cluster-config-file 7000/nodes.conf
cluster-node-timeout 5000
appendonly yes
EOF

mkdir 7000 7001 7002 7003 7004 7005

cat redis.conf | sed s/7000/7000/g > 7000/redis.conf
cat redis.conf | sed s/7000/7001/g > 7001/redis.conf
cat redis.conf | sed s/7000/7002/g > 7002/redis.conf
cat redis.conf | sed s/7000/7003/g > 7003/redis.conf
cat redis.conf | sed s/7000/7004/g > 7004/redis.conf
cat redis.conf | sed s/7000/7005/g > 7005/redis.conf

export redis_server='../redis/src/redis-server'
# master
$redis_server 7000/redis.conf
# slave
$redis_server 7001/redis.conf --slaveof 127.0.0.1 7000
$redis_server 7002/redis.conf --slaveof 127.0.0.1 7000
$redis_server 7003/redis.conf --slaveof 127.0.0.1 7000
$redis_server 7004/redis.conf --slaveof 127.0.0.1 7000
$redis_server 7005/redis.conf --slaveof 127.0.0.1 7000

```

```shell
cat << EOF > sentinel.conf
port 26379
daemonize yes
protected-mode no
sentinel monitor mymaster 127.0.0.1 7000 2
sentinel down-after-milliseconds mymaster 60000
sentinel failover-timeout mymaster 180000
sentinel parallel-syncs mymaster 1
EOF

cat sentinel.conf | sed s/26379/26379/g > sentinel_0.conf
cat sentinel.conf | sed s/26379/36379/g > sentinel_1.conf
cat sentinel.conf | sed s/26379/46379/g > sentinel_2.conf

redis_server='../redis/src/redis-server'
$redis_server sentinel_0.conf --sentinel
$redis_server sentinel_1.conf --sentinel
$redis_server sentinel_2.conf --sentinel
```
