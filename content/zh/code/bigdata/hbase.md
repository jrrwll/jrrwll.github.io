
---
title: "HBase"
---

## HBase Shell

```shell
habse shell
```

### ddl

```ruby
# list all the tables
list

create 'mytable', 'c_int', 'c_string', 'c_date'
disable 'mytable'
drop 'mytable'
# drop_all 't.*'
```

### dml

```ruby
# put 'talbe_name', 'row', 'colfamily:colname', 'value'
put 'mytable', 1, 'c_int:h1', '1'
put 'mytable', 1, 'c_int:h2', '2'
put 'mytable', 1, 'c_int:h3', '3'
put 'mytable', 1, 'c_int:h4', '4'
```

```ruby
scan 'mytable'
get 'mytable', 1
```
