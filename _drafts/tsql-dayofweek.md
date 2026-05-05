---
layout: post
title: "T-SQL - 曜日についてDATEPART関数と@@DATEFIRSTを確認する"
date: 
tags: t-sql sql-server
---

SQL Serverで指定した日付の曜日を示す数字を取得するには`DATEPART`関数を使いますが、`@@DATEFIRST`の値によりこの関数が返す値が変わってきます。

変更できるようになっているというか、扱いに少し癖があるというか。

例えば"日曜日は0、月曜日は1、..."といったように値が固定されていません。

といったあたりを確認していきます。

まず、曜日文字列を取得する`DATENAME`関数もあわせて使って、1週間分の`DATEPART`関数の結果を確認します。

```sql
-- datepart(dw, @date)
-- 日付から曜日を示す数字（1～7）を取得する
-- @datefirstが7の場合、1：日曜日～7：土曜日’（日曜日始まり）となる

-- @dateから1週間分の日付一覧を取得して、曜日を確認する
declare @date date = '2026/05/03';

with Week(Date)
as(
    select @date
    union all
    select dateadd(d, 1, Date)
    from Week
    where Date < dateadd(d, 6, @date)
)
select
    Date,
    -- 曜日文字列
    cast(datename(dw, Date) as nvarchar(5)) as [datename],
    -- 曜日を示す数字
    datepart(dw, Date) as [datepart]
from Week;
/*
Date       datename datepart
---------- -------- -----------
2026-05-03 日曜日      1

2026-05-04 月曜日      2
2026-05-05 火曜日      3
2026-05-06 水曜日      4
2026-05-07 木曜日      5
2026-05-08 金曜日      6
2026-05-09 土曜日      7
*/
```

`DATEPART`関数は、日曜日：`1`～土曜日：`7`を返していることを確認できました。

続いて`@@DATEFIRST`の値を日本語環境で確認したところ、デフォルトでは`@@datefirst`の値は`7`となっていました。
`7`つまり**日曜日始まり**ということになり、指定した日付が日曜日のときに`1`を返すようになっているようです。

```sql
-- @@datefirst
-- 週の最初の曜日を示す数字（1：月曜日～7：日曜日）を取得する
-- 日本が環境ではデフォルトで7：日曜日となる様子
select @@datefirst as [@@datefirst];
-- 実行結果
/*
@@datefirst
-----------
7
*/
```

ややこし。

- https://learn.microsoft.com/ja-jp/sql/t-sql/functions/datepart-transact-sql?view=sql-server-ver17#week-and-weekday-datepart-arguments
- https://learn.microsoft.com/ja-jp/sql/t-sql/functions/datefirst-transact-sql?view=sql-server-ver17
