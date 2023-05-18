---
title: "MongoDB"
---

## install

### container

```shell
# -v /path/to/mongodb-persistence:/bitnami/mongodb
docker run -d --name mongo \
-p 27017:27017 \
-e MONGODB_ROOT_USER=root \
-e MONGODB_ROOT_PASSWORD=root \
-e MONGODB_USERNAME=myuser \
-e MONGODB_PASSWORD=mypassword \
-e MONGODB_DATABASE=u \
bitnami/mongodb

docker exec -it mongo mongosh

# ME_CONFIG_MONGODB_ADMINUSERNAME=root
# ME_CONFIG_MONGODB_ADMINPASSWORD=example
# ME_CONFIG_MONGODB_URL=mongodb://root:example@mongo:27017
docker run -it -d --name mongo-express \
-p 8081:8081 \
mongo-express
```

## auth

```js
use my_db
db.auth('admin', 'my_old_passwd')
db.addUser('admin','my_new_passwd')
db.changeUserPassword('admin','my_new_passwd')
```

### role
> https://www.mongodb.com/docs/manual/reference/privilege-actions/

```js
use admin
db.getRoles()
db.createRole({
    role: 'admin',
    privileges: [
        {
        // { db: <database>, collection: <collection> }
        resource: {anyResource: true},
        actions: ['anyAction']
        }
    ],
    roles: [
        'readWriteAnyDatabase', 'userAdminAnyDatabase', 
        'dbAdminAnyDatabase'
    ]
})
db.dropRole('admin')
```

### user

```js
use admin
db.getUsers()
db.createUser({
    user: "myuser",
    pwd: "mypassword",
    roles: [
        // {
        // role: "myrole",
        // db: "mydb"
        // }
        "admin"
    ]
})
db.dropUser('myuser')

db.auth('myuser', 'mypassword')
use mydb
db.getUsers()
```

```shell
# 指定认证的数据库
mongosh mongodb://myuser:mypassword@localhost/mydb?authSource=admin
mongosh localhost -u myuser -p mypassword --authenticationDatabase admin
```

## sql

### curd

```js
show dbs
db

use mydb
db

db.mytab.insert({
"c_oid": ObjectId("52ffc4a5d85242602e000000"),
"c_string": "abc",
"c_integer": 1,
"c_boolean": true,
"c_double": 3.14,
"c_null": null,
"c_date": Date(),
"c_array_integer": [1, 2, 3],
"c_object": {"name": "jerry"},
"c_array_object": [
{"id": ObjectId("52ffc4a5d85242602e000000")}
]
})
```
