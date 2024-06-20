---
layout: post
title: "T-SQL - OPENJSONを使ってJSON文字列カラム内の値を条件に取得する"
date: 
tags: t-sql
---


```sql
drop table if exists dbo.todoItem;
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


-- tag-aを含んでいるレコードを取得
select *
from dbo.TodoItem
where exists (
    select Tag
    from openjson(Tags) with(Tag nvarchar(20) N'$')
    where Tag = N'tag-a'
);

select *
from dbo.TodoItem
where N'tag-a' in (
    select Tag
    from openjson(Tags) with(Tag nvarchar(20) N'$')
);


-- Tagsをレコードに展開
select *
from dbo.TodoItem
cross apply openjson(Tags) with(Tag nvarchar(20) N'$');
```