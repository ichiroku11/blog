---
layout: post
title: "T-SQL - n個未満ならINSERTする"
date: 2022-11-16
tags: t-sql
---

以前書いたINSERT文だと条件を満たすレコード数が0件の場合に挿入できなかったので、クエリを修正しました。（2022-12-27更新）

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

```sql
declare @groupId int = 1;

with Cte(Count)
as(
    select count(*)
    from dbo.Sample
    where GroupId = @groupId)
insert into dbo.Sample(GroupId, Value)
output inserted.*
select @groupId, N'x'
where (select * from Cte) < 2;
```

`declare @groupId int = 1;`としてクエリを実行すると、`GroupId = 1`のレコードは2件登録されているので新たにレコードは追加されません。

```sql
-- GroupId = 1のレコード数が2未満であれば追加する
-- => 追加できない
declare @groupId int = 1;

-- クエリは上記と同じなので省略

/*
GroupId     Value
----------- -----
*/
```

次に`declare @groupId int = 2;`としてクエリを実行してみましょう。
`GroupId = 2`のレコードは1件だけ登録されているのでレコードが追加されます。

```sql
-- GroupId = 2のレコード数が2未満であれば追加する
-- => 追加できる
declare @groupId int = 2;

-- クエリは上記と同じなので省略

/*
GroupId     Value
----------- -----
2           x
*/
```

最後に`declare @groupId int = 3;`としてクエリを実行してみましょう。
`GroupId = 3`のレコードは登録されていないのでレコードが追加されます。

```sql
-- GroupId = 3のレコード数が2未満であれば追加する
-- => 追加できる
declare @groupId int = 3;

-- クエリは上記と同じなので省略

/*
GroupId     Value
----------- -----
3           x
*/
```


今回のサンプルは条件に使うテーブルと挿入先のテーブルが同じでしたが、別のテーブルでも条件付きINSERTができるのかなと思います。
