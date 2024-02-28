---
layout: post
title: "T-SQL - DATETRUNC関数を使ってグルーピングする"
date: 
tags: t-sql
---

SQL Server 2022で追加されたDATETRUNC関数は、日時を指定した部分に切り詰める関数です。

[DATETRUNC (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/datetrunc-transact-sql?view=sql-server-ver16)

GROUP BY句で日付や時間ごとにグルーピングする際に使えそうなので試してみました。
次のサンプルでは、日時の一覧を時間ごとにグルーピングして時間あたりの個数を取得しています。

```sql
-- 日時の一覧（アクセスログのようなイメージ）
drop table if exists #Log;
select *
into #Log
from (values
	('2024-02-29 01:02:03'),
	('2024-02-29 01:02:04'),
	('2024-02-29 02:02:03'),
	('2024-02-29 03:02:03'),
	('2024-02-29 03:02:04'),
	('2024-02-29 03:02:05'),
	('2024-02-29 04:02:03')
) as Log(Timestamp);

-- 時間でグルーピングして個数を取得する
select datetrunc(hour, Timestamp), count(*)
from #Log
group by datetrunc(hour, Timestamp);
/*
--------------------------- -----------
2024-02-29 01:00:00.0000000 2
2024-02-29 02:00:00.0000000 1
2024-02-29 03:00:00.0000000 3
2024-02-29 04:00:00.0000000 1
*/
```

また、DATETRUNC関数の1つ目の引数`datepart`に`year`、`month`、`day`、`hour`、`minute`を指定して確認してみた結果も残しておきます。

```sql
declare @date datetime2 = '2024-02-29 01:02:03';

-- 年に切り詰める
select datetrunc(year, @date) as [Year];
/*
Year
---------------------------
2024-01-01 00:00:00.0000000
*/

-- 月に切り詰める
select datetrunc(month, @date) as [Month];
/*
Month
---------------------------
2024-02-01 00:00:00.0000000
*/

-- 日に切り詰める
select datetrunc(day, @date) as [Day];
/*
Day
---------------------------
2024-02-29 00:00:00.0000000
*/

-- 時に切り詰める
select datetrunc(hour, @date) as [Hour];
/*
Hour
---------------------------
2024-02-29 01:00:00.0000000
*/

-- 分に切り詰める
select datetrunc(minute, @date) as [Minute];
/*
Minute
---------------------------
2024-02-29 01:02:00.0000000
*/
```

他にも`quarter`とか`week`とか指定できるみたいです。