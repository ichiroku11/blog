---
layout: post
title: "T-SQL - count(*)とgroup byの動きを確認する"
date: 
tags: t-sql
---

当たり前だけどgroup byするとレコードが消えることの確認


```sql
-- サンプルデータ
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
declare @groupId int = 3;

-- グルーピングしない
select count(*)
from dbo.Sample
where GroupId = @groupId
/*
// todo: 結果
*/

-- group byでグルーピンする
select GroupId, count(*)
from dbo.Sample
where GroupId = @groupId
group by GroupId;
/*
// todo: 結果
*/
```

