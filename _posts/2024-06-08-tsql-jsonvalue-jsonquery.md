---
layout: post
title: "T-SQL - JSON_VALUE関数/JSON_QUERY関数を確認する"
date: 2024-06-08
tags: t-sql json
---

JSON文字列からスカラー値を返すJSON_VALUE関数とJSON文字列からオブジェクトまたは配列を返すJSON_QUERY関数を試します。

- JSON_VALUE関数
    - 真偽値、数値、文字列を取得できる
    - それ以外はNULL
    - [JSON_VALUE (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/json-value-transact-sql?view=sql-server-ver16)

- JSON_QUERY関数
    - 配列、オブジェクトを取得できる
    - それ以外はNULL
    - [JSON_QUERY (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/t-sql/functions/json-query-transact-sql?view=sql-server-ver16)

クエリを書いて実行してみた結果です。

```sql
declare @json nvarchar(max) = '{
    "a": true,
    "b": 1,
    "c": "xyz",
    "d": null,
    "x": {},
    "y": []
}';

-- JSON_VALUE関数は、真偽値、数値、文字列を取得できる
select
    json_value(@json, '$.a') as [boolean],
    json_value(@json, '$.b') as [number],
    json_value(@json, '$.c') as [string],
    json_value(@json, '$.d') as [null],
    json_value(@json, '$.x') as [object],
    json_value(@json, '$.y') as [array];
/*
boolean     number      string      null        object      array
----------- ----------- ----------- ----------- ----------- -----------
true        1           xyz         NULL        NULL        NULL
*/

-- JSON_QUERY関数は、配列、オブジェクトを取得できる
select
    json_query(@json, '$.a') as [boolean],
    json_query(@json, '$.b') as [number],
    json_query(@json, '$.c') as [string],
    json_query(@json, '$.d') as [null],
    json_query(@json, '$.x') as [object],
    json_query(@json, '$.y') as [array];
/*
boolean     number      string      null        object      array
----------- ----------- ----------- ----------- ----------- -----------
NULL        NULL        NULL        NULL        {}          []
*/

JSON内の`null`は`JSON_VALUE`関数、`JSON_QUERY`関数どちらもでNULLになります。
