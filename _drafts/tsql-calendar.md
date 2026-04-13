---
layout: post
title: "T-SQL - ある月の日付一覧を取得するクエリ"
date: 
tags: t-sql
---

ある年月の日付一覧を取得するクエリを書いてみました。

たとえば、2026年4月の場合は、2026/4/1、2026/4/2、・・・、2026/4/29、2026/4/30という結果を取得したいとします。

### 月初と月末の日付を取得する

まずは月初と月末の日付は次のように取得できます。

```sql
-- 日付を指定して取得する場合
declare @date date = '2026/04/14';

select
    datetrunc(m, @date) as [Beginning],
    eomonth(@date) as [End];
/*
Beginning  End
---------- ----------
2026-04-01 2026-04-30
*/

-- 年と月を指定して取得する場合
declare @year int = 2026;
declare @month int = 4;

select
    datefromparts(@year, @month, 1) as [Beginning],
    eomonth(datefromparts(@year, @month, 1)) as [End]
/*
（結果は同じになるので省略）
*/
```

### 月初から月末までの日付一覧を取得する

上記をふまえて、月初から月末までの日付を再帰CTEを利用して取得してみます。

```sql
-- todo:
```
