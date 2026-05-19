---
layout: post
title: "T-SQL - 曜日を.NETのDayOfWeekにあわせる"
date: 
tags: t-sql sql-server dotnet
---

前回、SQL Serverの曜日の値を`DATEPART`関数を使って取得し、`@@DATEFIRST`の値に依存することを確認しました。

[T-SQL - 曜日についてDATEPART関数と@@DATEFIRSTを確認する &#124; いちろぐ](/blog/2026/05/19/tsql-dayofweek.html)

一方で.NETの曜日（DayOfWeek）は0～6になっています。
```csharp
public enum DayOfWeek {
    Sunday = 0,
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
}
```

SQL Server上でも曜日を0～6固定として管理したいですね。

そんなときには`DATEPART関数`と`@DATEFIRST`を使って、次のように計算すると`@DATEFIRST`の値によらず戻り値は0～6として扱えるようです。

```sql
(datepart(dw, Date) + @@datefirst - 1) % 7
```

以下、1週間の日付一覧に対して曜日を確認してみたクエリです。

```sql
-- @dateから1週間の日付一覧を取得して、曜日を確認する
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
    cast(datename(dw, Date) as nvarchar(5)) as [datename],
    -- 1～7
    datepart(dw, Date) as [datepart],
    -- @@datefirstの値が7の場合
    -- datepart(dw, Date) + @@datefirst => 8～14
    -- datepart(dw, Date) + @@datefirst - 1 => 7～13
    -- ※@@datefirstの値によるが1～13（1～7 + 1～7 - 1）の値があり得るはず
    datepart(dw, Date) + @@datefirst - 1 as [datepart + datefirst - 1],
    -- .NETのDayOfWeekと同じ値に変換
    -- 0～6
    (datepart(dw, Date) + @@datefirst - 1) % 7 as [.NET DayOfWeek]
from Week;
/*
Date       datename datepart    datepart + datefirst - 1 .NET DayOfWeek
---------- -------- ----------- ------------------------ --------------
2026-05-03 日曜日      1           7                        0
2026-05-04 月曜日      2           8                        1
2026-05-05 火曜日      3           9                        2
2026-05-06 水曜日      4           10                       3
2026-05-07 木曜日      5           11                       4
2026-05-08 金曜日      6           12                       5
2026-05-09 土曜日      7           13                       6
*/
```
