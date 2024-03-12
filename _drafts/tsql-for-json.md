---
layout: post
title: "T-SQL - FOR JSON句を使って結果をJSON形式で出力する"
date: 
tags: t-sql
---

`FOR JSON`句を使ってクエリの結果をJSON形式にするサンプルクエリを書いて試してみました。

- `AUTO`モードと`PATH`モードの違い
- `ROOT`オプション
- INCLUDE_NULL_VALUESオプション
- WITHOUT_ARRAY_WRAPPERオプション

まずはテスト用のデータです。

```sql
drop table if exists #Temp;

select *
into #Temp
from (values
    (1, 'a', 9),
    (2, 'b', null)
) as Temp(X, Y, Z);

select *
from #Temp;
/*
X           Y    Z
----------- ---- -----------
1           a    9
2           b    NULL
*/
```

### AUTOモードとPATHモードの違い

`FOR JSON`句では`AUTO`モードか`PATH`モードを指定します。

`AUTO`モードでは、カラム名がそのまま出力されるプロパティ名になります。`.`を含むカラム名でも入れ子になったオブジェクトになったりはしません。
一方、`PATH`モードでは、`.`区切りのカラム名を指定すると入れ子になったオブジェクトとして出力できます。

実際のクエリ結果は、JSON内の余分な空白がない状態で出力されますが、以降のJSON形式はすべて見やすいように整形しています。

```sql
-- autoとpathの違い
select
    X,
    Y as 'detail.y',
    Z as 'detail.z'
from #Temp
for json auto;
/*
[
    {
        "X": 1,
        "detail.y": "a",
        "detail.z": 9
    },
    {
        "X": 2,
        "detail.y": "b"
    }
]
*/

select
    X,
    Y as 'detail.y',
    Y as 'detail.z'
from #Temp
for json path;
/*
[
    {
        "X": 1,
        "detail": {
            "y": "a",
            "z": "a"
        }
    },
    {
        "X": 2,
        "detail": {
            "y": "b",
            "z": "b"
        }
    }
]
*/

