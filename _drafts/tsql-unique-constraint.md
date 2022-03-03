---
layout: post
title: "SQL Server - nvarcharに対するUNIQUE制約は大文字小文字を区別しない"
date: 
tags: sql-server t-sql
---

nvarchar型のカラムにUNIQUE制約を設定した場合、大文字小文字の違いはどうなるんだっけとふと気になったので確認。

結果としては大文字小文字の違いは区別しないで重複と判断されます。例えば、値が"abc"というレコードがあれば、"ABC"というレコードは追加できないです。

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

おまけでwhere句でも大文字小文字を区別しない。

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