
---
title: "PostgreSQL Type"
---

## example

### Table With All Types

```sql
create schema test;
alter schema test owner to myuser;
grant all on all tables in schema test to myuser;

create table test.t_all_type (
c_bool bool,
c_bytea bytea,
c_char char,
c_name name,
c_int8 int8,
c_bigserial bigserial,
c_int2 int2,
c_int2vector int2vector,
c_int4 int4,
c_serial serial,
c_regproc regproc,
c_text text,
c_oid oid,
c_tid tid,
c_xid xid,
c_cid cid,
c_oidvector oidvector,
c_json json,
c_xml xml,
c_point point,
c_lseg lseg,
c_path path,
c_box box,
c_polygon polygon,
c_line line,
c_float4 float4,
c_float8 float8,
c_circle circle,
c_money money,
c_macaddr macaddr,
c_inet inet,
c_cidr cidr,
c_aclitem aclitem,
c_bpchar bpchar(10),
c_varchar varchar(10),
c_date date,
c_time time(6),
c_timestamp timestamp(6),
c_timestamptz timestamptz(6),
c_interval interval(6),
c_timetz timetz(6),
c_bit bit(10),
c_varbit varbit(10),
c_numeric numeric(10, 6),
c_uuid uuid,
c_tsvector tsvector,
c_gtsvector gtsvector,
c_tsquery tsquery,
c_jsonb jsonb
);
comment on table test.t_all_type is '全类型表';

comment on column test.t_all_type.c_bool is '列类型: bool';
comment on column test.t_all_type.c_bytea is '列类型: bytea';
comment on column test.t_all_type.c_char is '列类型: char';
comment on column test.t_all_type.c_name is '列类型: name';
comment on column test.t_all_type.c_int8 is '列类型: int8';
comment on column test.t_all_type.c_bigserial is '列类型: bigserial';
comment on column test.t_all_type.c_int2 is '列类型: int2';
comment on column test.t_all_type.c_int2vector is '列类型: int2vector';
comment on column test.t_all_type.c_int4 is '列类型: int4';
comment on column test.t_all_type.c_serial is '列类型: serial';
comment on column test.t_all_type.c_regproc is '列类型: regproc';
comment on column test.t_all_type.c_text is '列类型: text';
comment on column test.t_all_type.c_oid is '列类型: oid';
comment on column test.t_all_type.c_tid is '列类型: tid';
comment on column test.t_all_type.c_xid is '列类型: xid';
comment on column test.t_all_type.c_cid is '列类型: cid';
comment on column test.t_all_type.c_oidvector is '列类型: oidvector';
comment on column test.t_all_type.c_json is '列类型: json';
comment on column test.t_all_type.c_xml is '列类型: xml';
comment on column test.t_all_type.c_point is '列类型: point';
comment on column test.t_all_type.c_lseg is '列类型: lseg';
comment on column test.t_all_type.c_path is '列类型: path';
comment on column test.t_all_type.c_box is '列类型: box';
comment on column test.t_all_type.c_polygon is '列类型: polygon';
comment on column test.t_all_type.c_line is '列类型: line';
comment on column test.t_all_type.c_float4 is '列类型: float4';
comment on column test.t_all_type.c_float8 is '列类型: float8';
comment on column test.t_all_type.c_circle is '列类型: circle';
comment on column test.t_all_type.c_money is '列类型: money';
comment on column test.t_all_type.c_macaddr is '列类型: macaddr';
comment on column test.t_all_type.c_inet is '列类型: inet';
comment on column test.t_all_type.c_cidr is '列类型: cidr';
comment on column test.t_all_type.c_aclitem is '列类型: aclitem';
comment on column test.t_all_type.c_bpchar is '列类型: bpchar(10)';
comment on column test.t_all_type.c_varchar is '列类型: varchar(10)';
comment on column test.t_all_type.c_date is '列类型: date';
comment on column test.t_all_type.c_time is '列类型: time(6)';
comment on column test.t_all_type.c_timestamp is '列类型: timestamp(6)';
comment on column test.t_all_type.c_timestamptz is '列类型: timestamptz(6)';
comment on column test.t_all_type.c_interval is '列类型: interval(6)';
comment on column test.t_all_type.c_timetz is '列类型: timetz(6)';
comment on column test.t_all_type.c_bit is '列类型: bit(10)';
comment on column test.t_all_type.c_varbit is '列类型: varbit(10)';
comment on column test.t_all_type.c_numeric is '列类型: numeric(10, 6)';
comment on column test.t_all_type.c_uuid is '列类型: uuid';
comment on column test.t_all_type.c_tsvector is '列类型: tsvector';
comment on column test.t_all_type.c_gtsvector is '列类型: gtsvector';
comment on column test.t_all_type.c_tsquery is '列类型: tsquery';
comment on column test.t_all_type.c_jsonb is '列类型: jsonb';
```
