---
title: "Docker Compose"
---

## install

### debian9

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

sudo usermod -aG docker $USER

sudo systemctl status docker.service
sudo systemctl restart docker.service
```

## connector

### memory

**etc/catalog/memory.properties**

```properties
connector.name=memory
memory.max-data-per-node=128MB
```

```sql
create table memory.default.nation as
select * from tpch.tiny.nation;

insert into memory.default.nation
select * from tpch.tiny.nation;

select * from memory.default.nation;

drop table memory.default.nation;
```
    
