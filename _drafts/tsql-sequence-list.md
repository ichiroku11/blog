---
layout: post
title: "T-SQL - シーケンスの一覧を取得するクエリ"
date: 
tags: t-sql
---

```sql
select
    sc.name as [schema],
    sq.name as [sequence],
    *
from sys.sequences as sq
    inner join sys.schemas sc
        on sq.schema_id = sc.schema_id
order by
    [schema],
    [sequence];
```
