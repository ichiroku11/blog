---
layout: post
title: "T-SQL - GREATEST関数とLEAST関数"
date: 
tags: t-sql
---

引数の中の最大値を返すGREATEST関数、引数の中の最小値を返すLEAST関数のサンプルです。SQL Server 2022から使えるようになったようです。

```sql
-- 引数の中から最大値を取得
select greatest(1, 2, 3) as Greatest;
/*
Greatest
-----------
3
*/

-- 引数の中から最小値を取得
select least(1, 2, 3) as Least;
/*
Least
-----------
1
*/
```

引数は式なので、定数以外にも変数や関数、列なども指定できるようですね。

GREATEST
https://learn.microsoft.com/ja-jp/sql/t-sql/functions/logical-functions-greatest-transact-sql?view=azuresqldb-current


LEAST
https://learn.microsoft.com/ja-jp/sql/t-sql/functions/logical-functions-least-transact-sql?view=azuresqldb-current

