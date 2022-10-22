---
layout: post
title: "T-SQL - テーブル・カラムの一覧を取得するクエリ"
date: 
tags: t-sql
---

```sql
select
    s.name as [schema],
    t.name as [table],
    c.name as [column],
    *
from sys.tables as t
    inner join sys.schemas as s
        on t.schema_id = s.schema_id
    inner join sys.columns as c
        on t.object_id = c.object_id
order by
    [schema],
    [table],
    [column];
```
