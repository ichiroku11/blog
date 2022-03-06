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


### startofday/startofmonth/startofweek/startofyear関数

日・週・月・年の始まりの日を求める関数。

```
let dt = datetime(2022-03-01 11:12:13);
print
	dt = dt,
	// 指定した日の始まり
	startOfDay = startofday(dt),
	// 指定した日の週の始まり（日曜）
	startOfWeek = startofweek(dt),
	// 指定した日の月の始まり（月初）
	startOfMonth = startofmonth(dt),
	// 指定した日の年の始まり（年初）
	startOfYear = startofyear(dt)
/*
dt [UTC]	2022-03-01T11:12:13Z
startOfDay [UTC]	2022-03-01T00:00:00Z
startOfWeek [UTC]	2022-02-27T00:00:00Z
startOfMonth [UTC]	2022-03-01T00:00:00Z
startOfYear [UTC]	2022-01-01T00:00:00Z
*/
```


### endofday/endofmonth/endofweek/endofyear関数

日・週・月・年の終わりの日を求める関数。

```
let dt = datetime(2022-03-01 11:12:13);
print
	dt = dt,
	// 指定した日の終わり
	endOfDay = endofday(dt),
	// 指定した日の週の終わり（土曜）
	endOfWeek = endofweek(dt),
	// 指定した日の月の終わり（月末）
	endOfMonth = endofmonth(dt),
	// 指定した日の年の終わり（年末）
	endOfYear = endofyear(dt)
/*
dt [UTC]	2022-03-01T11:12:13Z
endOfDay [UTC]	2022-03-01T23:59:59.9999999Z
endOfWeek [UTC]	2022-03-05T23:59:59.9999999Z
endOfMonth [UTC]	2022-03-31T23:59:59.9999999Z
endOfYear [UTC]	2022-12-31T23:59:59.9999999Z
*/
```


### dayofweek/dayofmonth/dayofyear関数

週初め・月初・年初からの日数を求める関数。

dayoOfWeek関数の戻り値はtimespan型。dayofmonth/dayofyear関数の戻り値はint型。

```
let dt = datetime(2022-03-14 11:12:13);
print
	dt = dt,
	// 週初め（日曜）からの日数
	// 月曜なら1dになる
	dayoOfWeek = dayofweek(dt),
	// 月初からの日数
	dayoOfMonth = dayofmonth(dt),
	// 年初からの日数
	dayoOfYear = dayofyear(dt)
/*
dt [UTC]	2022-03-14T11:12:13Z
dayoOfWeek	1.00:00:00
dayoOfMonth	14
dayoOfYear	73
*/
```

