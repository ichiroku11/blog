---
layout: post
title: "T-SQL - RANK関数とDENSE_RANK関数の結果を確認する"
date: 
tags: t-sql
---

順位付け関数にRANK関数とDENSE_RANK関数があります。

どちらの関数も行の順位を返す関数ですが、違いは同じ順位があった場合です。
同じ順位があった場合、それ以降の順位が変わってきます。

どっちがどうだったか忘れるのでクエリの結果を残しておきます。

- [RANK (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/rank-transact-sql?view=sql-server-ver16)
- [DENSE_RANK (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/dense-rank-transact-sql?view=sql-server-ver16)

```sql
select *,
    rank() over(order by Value) as Rank,
    dense_rank() over(order by Value) as DenseRank
from (
    values
        (1, N'a', 100),
        -- Id=2とId=3が同じ順位
        (2, N'b', 200),
        (3, N'c', 200),
        (4, N'd', 300),
        (5, N'e', 400)
    ) as Value(Id, Name, Value)
order by Id;
```

クエリの結果です。

|Id|Name|Value|Rank|DenseRank|
|--|--|--|--|--|
|1|a|100|1|1|
|2|b|200|2|2|
|3|c|200|2|2|
|4|d|300|4|3|
|5|e|400|5|4|

Id=2とId=3が同じ順位なので、それ以降のRank列、DenseRank列に注目すると、
- Rank関数の結果は、**1つ前までの順位の数 + 1**
- DenseRank関数の結果は、**1つ前の順位 + 1**
となっていることがわかります。

`dense`という単語には「ぎっしり詰まった」「隙間がない」という意味もあるようでなので、
DENSE_RANK関数は「順位に隙間がない」と覚えると良いのかなと思いました。
