---
layout: post
title: "SQL Server - Japanese_CI_ASの照合順序ではnvarcharに対するUNIQUE制約も大文字小文字を区別しない"
date: 
tags: sql-server t-sql
---

照合順序`Japanese_CI_AS`での話です。

nvarchar型のカラムにUNIQUE制約を設定した場合、大文字小文字の違いは区別しないで重複と判断されるよねという確認。

例えば、値が"abc"というレコードがあれば、"ABC"というレコードは追加できないということです。

以下確認。

```sql
-- 初期データを用意
drop table if exists dbo.Sample;
create table dbo.Sample(
	Id int not null,
	Value nvarchar(10) not null,
	constraint PK_Sample primary key(Id),
	constraint UQ_Sample_Value unique(Value)
);

insert into dbo.Sample(Id, Value)
output inserted.*
values(1, N'abc');
/*
Id  Value
1   abc
*/

-- "ABC"をinsertしてみる
-- 制約 'UQ_Sample_Value' の UNIQUE KEY 違反。オブジェクト 'dbo.Sample' には重複するキーを挿入できません。重複するキーの値は (ABC) です。
insert into dbo.Sample(Id, Value)
output inserted.*
values(2, N'ABC');

-- "abc"をinsertしてみる
-- 制約 'UQ_Sample_Value' の UNIQUE KEY 違反。オブジェクト 'dbo.Sample' には重複するキーを挿入できません。重複するキーの値は (abc) です。
insert into dbo.Sample(Id, Value)
output inserted.*
values(2, N'abc');
```

おまけでwhere句でも大文字小文字を区別しないことを確認。

```sql
select *
from dbo.Sample
where Value = N'abc';
/*
Id  Value
1   abc
*/

select *
from dbo.Sample
where Value = N'ABC';
/*
Id  Value
1   abc
*/
```