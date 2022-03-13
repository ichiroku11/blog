---
layout: post
title: "KQLのdatetime型/timespan型と関連する関数のまとめ"
date: 2022-03-13
tags: azure-monitor kql
---

KQLにおける日時のdatetime型と時間間隔のtimespan型、それらに関連する関数のまとめです。

- [timespanのリテラル](#timespanのリテラル)
- [datetime/timespanの演算](#datetimetimespanの演算)
- [bin関数](#bin関数)
- [startofday/startofweek/startofmonth/startofyear関数](#startofdaystartofweekstartofmonthstartofyear関数)
- [endofday/endofweek/endofmonth/endofyear関数](#endofdayendofweekendofmonthendofyear関数)
- [dayofweek/dayofmonth/dayofyear関数](#dayofweekdayofmonthdayofyear関数)
- [datetime_part関数](#datetime_part関数)
- [datetime_add関数](#datetime_add関数)
- [datetime_diff関数](#datetime_diff関数)
- [now/ago関数](#nowago関数)
- [getyear/getmonth関数](#getyeargetmonth関数)


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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/scalar-data-types/timespan>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/samples?pivots=azuremonitor#date-and-time-operations>


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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/datetime-timespan-arithmetic>


### bin関数

値を切り捨てする関数です。日付以外にも利用できますがdatetime型で使うことも多いんじゃないかなと。

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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/binfunction>


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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/startofdayfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/startofmonthfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/startofweekfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/startofyearfunction>


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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/endofdayfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/endofmonthfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/endofweekfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/endofyearfunction>


### dayofweek/dayofmonth/dayofyear関数

週初め・月初・年初からの日数を求める関数。

dayoOfWeek関数の戻り値はtimespan型で、dayofmonth/dayofyear関数の戻り値はint型。

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

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/dayofweekfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/dayofmonthfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/dayofyearfunction>


### datetime_part関数

dattime型の部分を整数として取得します。

```
let dt = datetime(2022-03-10 11:12:13.14);
print
	year = datetime_part("year", dt),
	month = datetime_part("month", dt),
	day = datetime_part("day", dt),
	hour = datetime_part("hour", dt),
	minute = datetime_part("minute", dt),
	second = datetime_part("second", dt),
	millisecond = datetime_part("millisecond ", dt),
	// 四半期
	quarter = datetime_part("quarter", dt),
	// 年初からの日数
	dayOfYear = datetime_part("dayOfYear", dt),
	// 年初からの週数
	weekOfYear = datetime_part("week_of_year", dt)

/*
year	2022
month	3
day	10
hour	11
minute	12
second	13
millisecond	140
quarter	1
dayOfYear	69
weekOfYear	10
*/
```

1月～3月の場合はクォーターが1になるようです。

```
print
	q1 = datetime_part("quarter", datetime(2022-01-01)),
	q2 = datetime_part("quarter", datetime(2022-04-01)),
	q3 = datetime_part("quarter", datetime(2022-07-01)),
	q4 = datetime_part("quarter", datetime(2022-10-01))
/*
q1	1
q2	2
q3	3
q4	4
*/
```

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/datetime-partfunction>


### datetime_add関数

dattime型の指定した部分の値を加算する関数です。

```
let dt = datetime(2022-01-01);
print
	year = datetime_add("year", 1, dt),
	quarter = datetime_add("quarter", 1, dt),
	month = datetime_add("month", 1, dt),
	week = datetime_add("week", 1, dt),
	day = datetime_add("day", 1, dt),
	hour = datetime_add("hour", 1, dt),
	minute = datetime_add("minute", 1, dt),
	second = datetime_add("second", 1, dt)

/*
year [UTC]	2023-01-01T00:00:00Z
quarter [UTC]	2022-04-01T00:00:00Z
month [UTC]	2022-02-01T00:00:00Z
week [UTC]	2022-01-08T00:00:00Z
day [UTC]	2022-01-02T00:00:00Z
hour [UTC]	2022-01-01T01:00:00Z
minute [UTC]	2022-01-01T00:01:00Z
second [UTC]	2022-01-01T00:00:01Z
*/
```

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/datetime-addfunction>


### datetime_diff関数

datetime型の差を指定した単位で求めます。

```
let dt1 = datetime(2023-01-01);
let dt2 = datetime(2022-01-01);
print
	year = datetime_diff("year", dt1, dt2),
	quater = datetime_diff("quarter", dt1, dt2),
	month = datetime_diff("month", dt1, dt2),
	week = datetime_diff("week", dt1, dt2),
	day = datetime_diff("day", dt1, dt2)
/*
year	1
quater	4
month	12
week	53
day 365
*/

let dt1 = datetime(2022-01-01 02:00:00);
let dt2 = datetime(2022-01-01 01:00:00);
print
	hour = datetime_diff("hour", dt1, dt2),
	minute = datetime_diff("minute", dt1, dt2),
	second = datetime_diff("second", dt1, dt2)
/*
hour	1
minute	60
second	3600
*/
```

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/datetime-difffunction>


### now/ago関数

now関数は現在日時、ago関数は現在日時から減算した日時を取得します。同じステートメント内で複数呼び出した場合は同じ値になるとのことです。

```
print
	// 今
	now(),
	// 1時間前
	now(-1h),
	// 1時間前
	ago(1h)
// 結果は省略
```

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/nowfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/agofunction>


### getyear/getmonth関数

日付から年や月を取得します。datetime_part関数のショートハンド的な感じですかね。

```
let dt = datetime(2022-03-10);
print
	year = getyear(dt),
	month = getmonth(dt)

/*
year	2022
month	3
*/
```

- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/getyearfunction>
- <https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/getmonthfunction>
