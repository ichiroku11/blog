---
layout: post
title: "KQLのdatetime型/timespan型と関連する関数のまとめ"
date: 
tags: azure-monitor kql
---

KQLで日時のdatetime型と時間間隔のtimespan型、それらに関連する関数のまとめです。

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

関数の前にまずリテラルのおさらい。

```
```

- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/scalar-data-types/timespan
- https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/samples?pivots=azuremonitor#date-and-time-operations

### datetime/timespanの演算
