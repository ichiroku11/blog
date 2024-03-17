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

### AUTOモードとPATHモードの違い

`FOR JSON`句では`AUTO`モードか`PATH`モードを指定します。

`AUTO`モードでは、カラム名が出力されるプロパティ名になります。`.`を含むカラム名でも入れ子になったオブジェクトにはなりません。
一方、`PATH`モードでは、`.`区切りのカラム名を指定すると入れ子になったオブジェクトとして出力できます。

結果の違いを確認してみましょう。実際のクエリ結果にはカラム名が入りますが、カラム名を省略してJSONだけにしています。また、JSON文字列も余分な空白がない状態で出力されますが、以降のJSON形式はすべて見やすいように整形しています。

```sql
-- AUTOモード
select
    X,
    Y as 'detail.y'
from #Temp
for json auto;
/*
// todo:
*/

-- PATHモード
-- 入れ子になったオブジェクトとして出力される
select
    X,
    Y as 'detail.y'
from #Temp
for json path;
/*
// todo:
*/
```

### ROOTオプション

`ROOT`オプションを指定すると、引数に指定したプロパティ名を持つオブジェクトとして出力できます。

`ROOT`オプションの有無で結果の違いを確認してみましょう。

```sql
-- ROOTオプションなし
select
    X as x,
    Y as y
from #Temp
for json path;
/*
// todo:
*/

-- ROOTオプションあり
-- "items"プロパティを持つオブジェクトとして出力される
select
    X as x,
    Y as y
from #Temp
for json path, root('items');
/*
// todo:
*/
```

### INCLUDE_NULL_VALUESオプション

名前の通りですね。`NULL`を出力したい場合に指定するオプションです。

```sql
-- INCLUDE_NULL_VALUESオプションなし
select
    Z as z
from #Temp
for json path;
/*
// todo:
*/

-- INCLUDE_NULL_VALUESオプションあり
select
    Z as z
from #Temp
for json path, include_null_values;
/*
// todo:
*/
```

### WITHOUT_ARRAY_WRAPPERオプション

// todo:

```sql
-- WITHOUT_ARRAY_WRAPPERオプション
select
    X as x
from #Temp
for json path;
/*
// todo:
*/

select
    X as x
from #Temp
for json path, without_array_wrapper;
/*
// todo:
*/
```

// todo:

```sql
select *
from #Temp
for json path, root('items'), without_array_wrapper;
-- エラー
-- ROOT option and WITHOUT_ARRAY_WRAPPER option cannot be used together in FOR JSON. Remove one of these options.
```

