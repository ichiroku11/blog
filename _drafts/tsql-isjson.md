---
layout: post
title: "T-SQL - ISJSON関数を確認する"
date: 
tags: t-sql
---

引数に渡された値がJSON文字列かどうかチェックできるISJSON関数を見つけたので、実行結果を確認したいと思います。

[ISJSON (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/isjson-transact-sql)

ISJSON関数は、1つ目の引数にテストする文字列を指定し、2つ目の引数にチェックするJSONのタイプ（種類）を指定します。

2つ目の引数json_type_constraintによって変わる結果を確認していきましょう。

### json_type_constraint引数を省略した場合、オブジェクトか配列であれば1を返す

`ARRAY`の結果と`OBJECT`の結果のORという感じ。

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

### json_type_constraint引数にVALUEを指定した場合、真偽値・数値・文字列・null・オブジェクト・配列であれば1を返す

有効なJSON値であれば`1`を返すと。

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

### json_type_constraint引数にARRAYを指定した場合、配列であれば1を返す

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

### json_type_constraint引数にOBJECTを指定した場合、オブジェクトであれば1を返す

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

### json_type_constraint引数にSCALARを指定した場合、数値と文字列であれば1を返す

真偽値や`null`では結果が`0`になります。

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