---
layout: post
title: "T-SQL - n個未満ならINSERTする"
date: 2022-11-16
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

このテーブルに対して、同じ`GroupId`のレコード数が2未満であれば挿入できるINSERT文は次のようになります。
まずは`GroupId = 1`を条件にしたクエリです。`GroupId = 1`のレコードは2件登録されているので新たにレコードは追加されません。

```sql
-- GroupId = 1のレコード数が2未満であれば追加する
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

次に`GroupId = 2`を条件してクエリを実行してみましょう。
`GroupId = 2`のレコードは1件だけ登録されているのでレコードが追加されます。

```sql
-- GroupId = 2のレコード数が2未満であれば追加する
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

今回のサンプルは条件に使うテーブルと挿入先のテーブルが同じでしたが、別のテーブルでも条件付きINSERTができるのかなと思います。
