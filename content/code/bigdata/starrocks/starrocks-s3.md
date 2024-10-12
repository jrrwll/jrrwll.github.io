
---
title: "StarRocks On S3"
---

## MinIO

> Starrocks 存算一体模式部署
> https://docs.starrocks.io/zh/docs/deployment/shared_data/minio/

### config

#### fe.conf

```conf
# shared_data 存算分离模式，shared_nothing 存算一体模式
run_mode = shared_data
# 云原生元数据服务监听端口
cloud_native_meta_port = 6090
# 存储类型：S3，AZBLOB，HDFS
cloud_native_storage_type = S3

# https://s3.us-west-2.amazonaws.com
aws_s3_endpoint = http://172.26.1.0:9000
aws_s3_region = us-east1
aws_s3_path = testbucket/subpath

aws_s3_access_key = minio_access_key
aws_s3_secret_key = minio_secret_key
```

在集群创建后再手动创建默认存储卷，则只需添加以下配置项：

```conf
run_mode = shared_data
cloud_native_meta_port = 6090
# false 则启动之后必须先创建默认存储卷
enable_load_volume_from_conf = false
```

#### cn.conf

```conf
# 用于 CN 心跳服务的端口
starlet_port = 9070
# 本地缓存数据依赖的存储目录，默认值：${STARROCKS_HOME}/storage
storage_root_path = /data1;/data2
```

### setup

#### 创建默认存储卷

```sql
-- builtin_storage_volume
create storage volume default_storage_volume
type = s3 locations = ("s3://defaultbucket")
properties (
    "enabled" = "true",
    "aws.s3.region" = "us-east1",
    "aws.s3.endpoint" = "http://172.26.x.x:39000",
    "aws.s3.access_key" = "minio_access_key",
    "aws.s3.secret_key" = "minio_secret_key",
    "aws.s3.enable_partitioned_prefix" = "true"
);

set default_storage_volume as default storage volume;
show storage volumes;
desc storage volume builtin_storage_volume;
```

#### 创建云原生表

```sql
properties (
    -- 如果未指定存储卷，StarRocks 将使用默认存储卷
    "storage_volume" = "def_volume",
    -- 是否启用本地磁盘缓存，默认值：true。true则数据会同时导入对象存储和本地磁盘，作为查询加速的缓存
    "datacache.enable" = "true",
    -- 热数据的有效期（hour day month year）。当启用本地磁盘缓存时，所有数据都会导入至本地磁盘缓存中。当缓存满时，StarRocks 会从缓存中删除最近较少使用（Less recently used）的数据。当有查询需要扫描已删除的数据时，StarRocks 会从当前时间开始，检查该数据是否在有效期内。如果数据在有效期内，StarRocks 会再次将数据导入至缓存中。如果不指定该参数，StarRocks 将所有数据都作为热数据进行缓存
    "datacache.partition_duration" = "1 month",
    -- 是否允许数据异步写入对象存储，默认值：false。true则导入任务在数据写入本地磁盘缓存后立即返回成功，数据将异步写入对象存储。允许数据异步写入可以提升导入性能，但如果系统发生故障，可能会存在一定的数据可靠性风险。
    "enable_async_write_back" = "false"
);
```

#### 初始化配置

```sql
-- mysql -h 127.0.0.1 -P9030 -uroot

show frontends\G
-- 添加FE节点到集群 ip:edit_log_port
alter system add follower "127.0.0.1:9010";
-- 添加CN节点到集群 ip:heartbeat_service_port
alter system add compute node '127.0.0.1:9050';
show compute nodes\G
```

```sql
-- 设置root密码
-- set password for 'root' = password('root');
set password = password('root');
```
