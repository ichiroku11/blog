---
layout: post
title: "KQLのdatetime型/timespan型と関連する関数のまとめ"
date: 
tags: azure-monitor kql
---

KQLにおける日時のdatetime型と時間間隔のtimespan型、それらに関連する関数のまとめです。

- timespanのリテラル
- datetime/timespanの演算
- bin関数
- startofday/startofweek/startofmonth/startofyear関数
- endofday/endofweek/endofmonth/endofyear関数
- dayofweek/dayofmonth/dayofyear関数
- datetime_part関数
- datetime_add関数
- datetime_diff関数
- ago/now関数

### timespanのリテラル

関数の前にまずtimespanのリテラルのおさらい。

```
print
	day = 1d,
	hour = 2h,
	minute = 3m,
	second = 4s,
	millisecond = 5ms,
	microsecond = 6microsecond,
	tick = 7tick
/*
day	1.00:00:00
hour	02:00:00
minute	00:03:00
second	00:00:04
millisecond	00:00:00.0050000
microsecond	00:00:00.0000060
tick	00:00:00.0000007
*/
```

// todo

- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/scalar-data-types/timespan
- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/samples?pivots=azuremonitor#date-and-time-operations

### datetime/timespanの演算

datetimeとtimespanは演算できます。

```
let dt = datetime(2022-01-10);
print
	// 3日前
	dt - 3d,
	// 3日後
	dt + 3d
/*
print_0 [UTC]	2022-01-07T00:00:00Z
print_1 [UTC]	2022-01-13T00:00:00Z
*/
```

// todo

- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/datetime-timespan-arithmetic

### bin関数

日付以外にも利用できますが、値を切り捨てする関数。

```
let dt = datetime(2022-02-22 11:12:13);
print
	dt,
	// 時間までで切り捨て
	bin(dt, 1h),
	// 日までで切り捨て
	bin(dt, 1d)
/*
print_0 [UTC]	2022-02-22T11:12:13Z
print_1 [UTC]	2022-02-22T11:00:00Z
print_2 [UTC]	2022-02-22T00:00:00Z
*/
```

// todo

- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/binfunction
