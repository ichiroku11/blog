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
    Tags nvarchar(max) not null,
    constraint PK_Blog primary key(Id)
);
insert into dbo.TodoItem(Id, Title, Tags)
output inserted.*
values
    (1, N'todo-1', N'["tag-a","tag-b"]'),
    (2, N'todo-2', N'["tag-b"]'),
    (3, N'todo-3', N'["tag-a","tag-c"]');
/*
Id	Title	Tags
1	todo-1	["tag-a","tag-b"]
2	todo-2	["tag-b"]
3	todo-3	["tag-a","tag-c"]
*/
```

今回はタグ`tag-a`を含んでいるレコードを取得してみたいと思います。

クエリは次のような感じ。

```sql
select *
from dbo.TodoItem
where exists (
    select Tag
    from openjson(Tags) with(Tag nvarchar(max) N'$')
    where Tag = N'tag-a');
/*
Id	Title	Tags
1	todo-1	["tag-a","tag-b"]
3	todo-3	["tag-a","tag-c"]
*/
```

別解。
個人的には、条件をIN句の左辺に持ってくるのがわかりやすいのかどうか疑問。
ちなみにEF Core 8で`Contains`を使ったら出力されたクエリです。

```sql
select *
from dbo.TodoItem
where N'tag-a' in (
    select Tag
    from openjson(Tags) with(Tag nvarchar(max) N'$')
);
/*
Id	Title	Tags
1	todo-1	["tag-a","tag-b"]
3	todo-3	["tag-a","tag-c"]
*/
);
```

もう1つ別解。`CROSS APPLY`と`OPENJSON`を組み合わせるやり方ですね。

```sql
select *
from dbo.TodoItem
    cross apply openjson(Tags) with(Tag nvarchar(max) N'$')
where Tag = N'tag-a';
/*
Id	Title	Tags	Tag
1	todo-1	["tag-a","tag-b"]	tag-a
3	todo-3	["tag-a","tag-c"]	tag-a
*/
```

なんとなく1つ目がわかりやすいのかなと思います。
