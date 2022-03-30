---
layout: post
title: "KQLのrange関数"
date: 
tags: azure-monitor kql
---

range関数


```
range x from 1 to 5 step 1
/*
x
1
2
3
4
5
*/
```

```
range t from timespan(00:00:00) to timespan(05:00:00) step 1h
/*
t
00:00:00
01:00:00
02:00:00
03:00:00
04:00:00
05:00:00
*/
```

```
range dt from datetime(2022-04-01 00:00:00) to datetime(2022-04-01 05:00:00) step 1h
/*
dt
2022/4/1 0:00:00.000
2022/4/1 1:00:00.000
2022/4/1 2:00:00.000
2022/4/1 3:00:00.000
2022/4/1 4:00:00.000
2022/4/1 5:00:00.000
*/
```
https://docs.microsoft.com/ja-jp/azure/data-explorer/kusto/query/rangeoperator
