---
layout: post
title: "T-SQL - シーケンスの一覧を取得するクエリ"
date: 2022-12-11
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

### 参考
- [sys.schemas (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/relational-databases/system-catalog-views/schemas-catalog-views-sys-schemas)
- [sys.sequences (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/relational-databases/system-catalog-views/sys-sequences-transact-sql)
