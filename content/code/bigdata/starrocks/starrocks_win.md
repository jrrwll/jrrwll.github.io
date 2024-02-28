
---
title: "Window Function of StarRocks"
---

## row_number

```sql
select any_value(sid),
    sum(score),
    max(ctime)
from (select *, 
        row_number() over(partition by tid order by ctime) as score_rownumber
    from (select 1 as sid, 1 as tid, 1 as score, 1 as ctime
            union
            select 1 as sid, 1 as tid, 3 as score, 2 as ctime
            union
            select 1 as sid, 2 as tid, 7 as score, 3 as ctime
            union
            select 1 as sid, 3 as tid, 15 as score, 4 as ctime
            union
            select 1 as sid, 4 as tid, 31 as score, 5 as ctime
            union
            select 2 as sid, 5 as tid, 63 as score, 6 as ctime) t) t1
where t1.score_rownumber = 1
and sid = 1;
```
