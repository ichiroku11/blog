---
layout: post
title: "T-SQL - CREATE VIEWでCTEを使う"
date: 
tags: t-sql
---

`CREATE VIEW`のSELECT句でCTEを使えることを知ったのでサンプルクエリを残しておきます。

CTEの中身は適当です。

```sql
use Test;

drop view if exists dbo.Sample;
go

create view dbo.Sample
as
    -- 1～10までの連番を生成するCTE
    with Cte as (
        select 1 as Value
        union all
        select Value + 1
        from Cte
        where Value < 10
    )
    select *
    from Cte;
go

select *
from dbo.Sample;

/*
Value
----------
1
2
3
4
5
6
7
8
9
10
*/
```

### 参考

- [CREATE VIEW (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/statements/create-view-transact-sql?view=sql-server-ver17)
