---
layout: post
title: "EF Core - LefJoinメソッドで外部結合（leff outer join）する"
date: 
tags: efcore t-sql
---

EF CoreでLefJoinメソッドを使って外部結合できるようになりました。
以前書いたGroupJoinを使ったサンプルを書き直してみました。

// todo:

- 2022-03-29-efcore-groupjoin.md

### サンプルのテーブルとデータ

取得したいサンプルデータです。以前と同じデータ。OuterとInnerを外部結合したいとします。

```sql
drop table if exists dbo.[Outer];
drop table if exists dbo.[Inner];

create table dbo.[Outer](
	Id int not null,
	Value nvarchar(10) not null,
	constraint PK_Outer primary key(Id)
);

create table dbo.[Inner](
	Id int not null,
	Value nvarchar(10) not null,
	constraint PK_Inner primary key(Id)
);

insert into dbo.[Outer](Id, Value)
output inserted.*
values
	(1, N'a'),
	(2, N'b'),
	(3, N'c');
/*
Id	Value
1	a
2	b
3	c
*/

insert into dbo.[Inner](Id, Value)
output inserted.*
values
	(1, N'A'),
	(4, N'D');
/*
Id	Value
1	A
4	D
*/

-- 実行したいクエリ
select *
from dbo.[Outer]
	left outer join dbo.[Inner]
		on dbo.[Outer].Id = dbo.[Inner].Id
order by dbo.[Outer].Id;
/*
Id	Value	Id	Value
1	a	1	A
2	b	NULL	NULL
3	c	NULL	NULL
*/
```

### LeftJoinメソッドを使って外部結合する

EF CoreでLeftJoinメソッドを使ってデータを取得してみます。

```csharp
// todo
```

### 参考

- https://learn.microsoft.com/ja-jp/ef/core/what-is-new/ef-core-10.0/whatsnew#support-for-the-net-10-leftjoin-and-rightjoin-operators
