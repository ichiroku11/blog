---
layout: post
title: "T-SQL - COUNT(*)とGROUP BYの動きを確認する"
date: 
tags: t-sql
---

行数を取得するCOUNT(*)で少しハマったのでメモ。

WHERE句で存在しない行を条件にしてCOUNT(*)すると、0件として取得できます。
その動きを想定してうっかりGROUP BYでグルーピングをしつつCOUNT(*)しちゃうと、対象のレコードが存在しないので0件として取得できない。当然ですね。でもハマってしまいました。

ということでサンプルです。

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

`WHERE`で存在しないグループを条件に指定し、グルーピングしないクエリでは、0件として取得できます。

```sql
declare @groupId int = 3;

-- グルーピングしなければOK
select count(*)
from dbo.Sample
where GroupId = @groupId
/*
Count
-----
0
*/
```

`WHERE`で存在しないグループを条件に指定しつつ、`GROUP BY`してしまうと結果が空になります。

```sql
-- うっかりグルーピングしちゃうとダメ
select GroupId, count(*)
from dbo.Sample
where GroupId = @groupId
group by GroupId;
/*
GroupId     Count
----------- -----
*/
```
