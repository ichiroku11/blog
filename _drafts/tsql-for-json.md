---
layout: post
title: "T-SQL - FOR JSON句を使って結果をJSON形式で出力する"
date: 
tags: t-sql
---

`FOR JSON`句を使ってクエリの結果をJSON形式にするサンプルクエリを書いて試してみました。

試してみたのはこのあたり。

- `AUTO`モードと`PATH`モードの違い
- `ROOT`オプション
- `INCLUDE_NULL_VALUES`オプション
- `WITHOUT_ARRAY_WRAPPER`オプション

まずはテスト用のデータです。

```sql
drop table if exists #Temp;

select *
into #Temp
from (values
    (1, 'a', null)
) as Temp(X, Y, Z);

select *
from #Temp;
/*
X           Y    Z
----------- ---- -----------
1           a    NULL
*/
```

`FOR JSON`句を使ってクエリを実行してみると、JSON文字列を1レコードの結果セットとして取得できます。
JSON文字列は配列になります。

実際の結果セットにはカラム名が入りますが、以降ではカラム名を省略してJSON文字列だけを表示しています。
また、JSON文字列も余分な空白がない状態で出力されますが、すべて見やすいように整形しています。

```sql
select *
from #Temp
for json auto;
/*
[
    {
        "X": 1,
        "Y": "a"
    }
]
*/
```

### AUTOモードとPATHモードの違い

`FOR JSON`句では`AUTO`モードか`PATH`モードを指定します。

`AUTO`モードでは、カラム名が出力されるプロパティ名になります。`.`を含むカラム名でも入れ子になったオブジェクトにはなりません。
一方、`PATH`モードでは、`.`区切りのカラム名を指定すると入れ子になったオブジェクトとして出力できます。

結果の違いを確認してみましょう。

```sql
-- AUTOモード
select
    X,
    Y as 'detail.y'
from #Temp
for json auto;
/*
[
    {
        "X": 1,
        "detail.y": "a"
    }
]
*/

-- PATHモード
-- 入れ子になったオブジェクトとして出力される
select
    X,
    Y as 'detail.y'
from #Temp
for json path;
/*
[
    {
        "X": 1,
        "detail": {
            "y": "a"
        }
    }
]
*/
```

### ROOTオプション

`ROOT`オプションを指定すると、引数に指定したプロパティ名を持つオブジェクトとして出力できます。

```sql
-- ROOTオプションあり
-- "items"プロパティを持つオブジェクトとして出力される
select
    X as x,
    Y as y
from #Temp
for json path, root('items');
/*
{
    "items": [
        {
            "x": 1,
            "y": "a"
        }
    ]
}
*/
```

### INCLUDE_NULL_VALUESオプション

名前の通りですね。`NULL`を出力したい場合に指定するオプションです。

```sql
-- INCLUDE_NULL_VALUESオプションあり
select Z as z
from #Temp
for json path, include_null_values;
/*
[
    {
        "z": null
    }
]
*/
```

### WITHOUT_ARRAY_WRAPPERオプション

配列として出力しないオプションです。
配列内の要素が複数でもカンマ区切りとして出力されてJSONとして正しくない気がするので、配列の要素が1つのときに使うのかも？

```sql
select X as x
from #Temp
for json path, without_array_wrapper;
/*
{
    "x": 1
}
*/
```

`ROOT`オプションと`WITHOUT_ARRAY_WRAPPER`オプションどちらも指定するとエラーになります。

```sql
select *
from #Temp
for json path, root('items'), without_array_wrapper;
-- エラー
-- ROOT option and WITHOUT_ARRAY_WRAPPER option cannot be used together in FOR JSON. Remove one of these options.
```

