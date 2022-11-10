---
layout: post
title: "T-SQL - n個未満ならINSERTする"
date: 
tags: t-sql
---

条件を満たすレコード数がn個未満の場合だけ挿入できるINSERT文を書いてみました。

まずサンプルのテーブルと初期データを用意します。

```sql
drop table if exists dbo.Sample;
create table dbo.Sample(
    GroupId int not null,
    Value nvarchar(5) not null
);

insert into dbo.Sample(GroupId, Value)
output inserted.*
values
    (1, N'a'),
    (1, N'b'),
    (2, N'c');
/*
GroupId     Value
----------- -----
1           a
1           b
2           c
*/
```

```sql
-- GroupId = 1のレコードが2未満であれば追加する
-- => 追加できない
insert into dbo.Sample(GroupId, Value)
output inserted.*
select 1, N'x'
from dbo.Sample
where GroupId = 1
group by GroupId
having count(*) < 2;
/*
GroupId     Value
----------- -----
*/
```

```sql
-- GroupId = 2のレコードが2未満であれば追加する
-- => 追加できる
insert into dbo.Sample(GroupId, Value)
output inserted.*
select 2, N'x'
from dbo.Sample
where GroupId = 2
group by GroupId
having count(*) < 2;
/*
GroupId     Value
----------- -----
2           x
*/
```