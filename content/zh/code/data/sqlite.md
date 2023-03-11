
---
title: "Sqlite"
---

## cmd

```sqilte
.help
.databases
.tables
```

## Sample
```sql
-- jdbc:mysql://localhost:3306?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
-- jdbc:sqlite:test.sqlite
create database test;

create table student (
    name varchar(100) not null primary key,
    class varchar(100),
    gender varchar(100),
    age smallint default -1,
    height decimal(16,6),
    weight decimal(16,6)
);

insert into student values
('john','a','male',27,173,78),
('paul','a','male',27,179,73),
('george','b','male',25,182,69),
('ringer','c','male',24,169,59),
('yoko','a','female',33,165,53),
('rita','b','female',25,163,57),
('lucy','c','female',28,175,60);

date,subject,sdutent_name,score

create table score (
    `date` date not null,
    subject varchar(100),
    student_name varchar(100),
    score decimal(16,6)
);

insert into score values
('2020-08-04','chinese','john',60),
('2020-08-04','chinese','paul',75),
('2020-08-04','chinese','george',55),
('2020-08-04','chinese','ringer',81),
('2020-08-04','chinese','yoko',95),
('2020-08-04','chinese','rita',72),
('2020-08-04','chinese','lucy',88),
('2020-08-04','math','john',96),
('2020-08-04','math','paul',100),
('2020-08-04','math','george',65),
('2020-08-04','math','ringer',87),
('2020-08-04','math','yoko',77),
('2020-08-04','math','rita',85),
('2020-08-04','math','lucy',98),
('2020-08-04','pe','john',82),
('2020-08-04','pe','paul',97),
('2020-08-04','pe','george',71),
('2020-08-04','pe','ringer',100),
('2020-08-04','pe','yoko',85),
('2020-08-04','pe','rita',52),
('2020-08-04','pe','lucy',75),
('2020-08-05','chinese','john',64),
('2020-08-05','chinese','paul',80),
('2020-08-05','chinese','george',42),
('2020-08-05','chinese','ringer',91),
('2020-08-05','chinese','yoko',100),
('2020-08-05','chinese','rita',79),
('2020-08-05','chinese','lucy',82),
('2020-08-05','math','john',91),
('2020-08-05','math','paul',90),
('2020-08-05','math','george',73),
('2020-08-05','math','ringer',76),
('2020-08-05','math','yoko',87),
('2020-08-05','math','rita',81),
('2020-08-05','math','lucy',100),
('2020-08-05','pe','john',88),
('2020-08-05','pe','paul',100),
('2020-08-05','pe','george',67),
('2020-08-05','pe','ringer',91),
('2020-08-05','pe','yoko',92),
('2020-08-05','pe','rita',60),
('2020-08-05','pe','lucy',73);
```
