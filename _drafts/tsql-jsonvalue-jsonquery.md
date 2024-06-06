---
layout: post
title: "T-SQL - JSON_VALUE関数/JSON_QUERY関数を確認する"
date: 
tags: t-sql json
---

JSON文字列からスカラー値を返すJSON_VALUE関数とJSON文字列からオブジェクトまたは配列を返すJSON_QUERY関数を試します。

### JSON_VALUE関数

// todo:

```sql
declare @json nvarchar(max) = '{
    "a": true,
    "b": 1,
    "c": "xyz",
    "d": null,
    "x": {},
    "y": []
}';

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
```

### JSON_QUERY関数

// todo:

```sql
declare @json nvarchar(max) = '{
    "a": true,
    "b": 1,
    "c": "xyz",
    "d": null,
    "x": {},
    "y": []
}';

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


select
    json_query(@json, '$') as [root];
/*
root
-----------
{
    "a": true,
    "b": 1,
    "c": "xyz",
    "d": null,
    "x": {},
    "y": []
}
*/
```
