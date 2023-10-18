---
layout: post
title: "T-SQL - ISJSON関数を確認する"
date: 
tags: t-sql
---

引数がJSON文字列かどうかチェックできるISJSON関数を見つけたので、実行結果を確認したいと思います。

[ISJSON (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/isjson-transact-sql)


```sql
select
    isjson('true') as [true],
    isjson('false') as [false],
    isjson('null') as [null],
    isjson('0') as [number],
    isjson('""') as [string],
    isjson('{}') as [object],
    isjson('[]') as [array];
/*
true        false       null        number      string      object      array
----------- ----------- ----------- ----------- ----------- ----------- -----------
0           0           0           0           0           1           1
*/
```

```sql
select
    isjson('true', value) as [true],
    isjson('false', value) as [false],
    isjson('0', value) as [number],
    isjson('""', value) as [string],
    isjson('null', value) as [null],
    isjson('{}', value) as [object],
    isjson('[]', value) as [array];
/*
true        false       number      string      null        object      array
----------- ----------- ----------- ----------- ----------- ----------- -----------
1           1           1           1           1           1           1
*/
```

```sql
select
    isjson('true', array) as [true],
    isjson('false', array) as [false],
    isjson('0', array) as [number],
    isjson('""', array) as [string],
    isjson('null', array) as [null],
    isjson('{}', array) as [object],
    isjson('[]', array) as [array];
/*
true        false       number      string      null        object      array
----------- ----------- ----------- ----------- ----------- ----------- -----------
0           0           0           0           0           0           1
*/
```

```sql
select
    isjson('true', object) as [true],
    isjson('false', object) as [false],
    isjson('0', object) as [number],
    isjson('""', object) as [string],
    isjson('null', object) as [null],
    isjson('{}', object) as [object],
    isjson('[]', object) as [array];
/*
true        false       number      string      null        object      array
----------- ----------- ----------- ----------- ----------- ----------- -----------
0           0           0           0           0           1           0
*/
```

```sql
select
    isjson('true', scalar) as [true],
    isjson('false', scalar) as [false],
    isjson('0', scalar) as [number],
    isjson('""', scalar) as [string],
    isjson('null', scalar) as [null],
    isjson('{}', scalar) as [object],
    isjson('[]', scalar) as [array];
/*
true        false       number      string      null        object      array
----------- ----------- ----------- ----------- ----------- ----------- -----------
0           0           1           1           0           0           0
*/
```