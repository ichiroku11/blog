---
layout: post
title: "T-SQL - OPENJSONを使ってJSON文字列カラム内の値を条件に取得する"
date: 
tags: t-sql
---

JSON文字列を保持するカラムを条件にして、データを取得するクエリを書いてみました。

OPENJSON関数はJOSN文字列をパースしてテーブルを返します。その戻り値をFROM句に指定しつつ条件を指定する感じです。

[OPENJSON (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/openjson-transact-sql?view=sql-server-ver16)

まずはサンプル用のテーブルとデータ。TODOのTagsカラムに配列のJSON文字列が入っています。

```sql
drop table if exists dbo.TodoItem;
create table dbo.TodoItem(
    Id int not null,
    Title nvarchar(10) not null,
    Tags nvarchar(20) not null,
    constraint PK_Blog primary key(Id)
);
insert into dbo.TodoItem(Id, Title, Tags)
output inserted.*
values
    (1, N'todo-1', N'["tag-a","tag-b"]'),
    (2, N'todo-2', N'["tag-b"]'),
    (3, N'todo-3', N'["tag-a","tag-c"]');
/*
// todo:
*/
```

あるタグを含んでいるレコードを取得するには、次のクエリを実行します。

```sql
-- tag-aを含んでいるレコードを取得
select *
from dbo.TodoItem
where exists (
    select Tag
    from openjson(Tags) with(Tag nvarchar(20) N'$')
    where Tag = N'tag-a'
);
/*
// todo:
*/
```

// todo: 別解

```sql
select *
from dbo.TodoItem
where N'tag-a' in (
    select Tag
    from openjson(Tags) with(Tag nvarchar(20) N'$')
/*
// todo:
*/
);
```

// todo: 別解

```sql
-- Tagsをレコードに展開
select *
from dbo.TodoItem
cross apply openjson(Tags) with(Tag nvarchar(20) N'$');
```