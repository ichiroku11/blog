---
layout: post
title: "Azure Cosmos DB - SQLクエリのSELECT VALUEを確認する"
date: 
tags: azure-cosmos-db
---

SELECT句にVALUEを指定した場合の結果を確認したいと思います。

まずはおさらいから。SELECT句では取得するJSONオブジェクトのプロパティを指定します。

次のクエリでは"$1"というプロパティを持ったJSONオブジェクトを取得しています。

```sql
select 1

/*
[
  {
    "$1": 1
  }
]
*/
```

次のクエリでは"x"いうプロパティを持ったJSONオブジェクトを取得しています。

```sql
select 1 as x

/*
[
  {
    "x": 1
  }
]
*/
```

おさらいはここまで。

SELECT VALUEの本題に移ります。
次のクエリのようにSELECT VALUEとすると、JSONオブジェクトではなく値そのものを取得できます。

```sql
select value 1

/*
[
  1
]
*/
```

値を取得するためのSELECT VALUEなので、"AS"でプロパティ名を指定するとエラーになります。

```sql
select value 1 as x
-- Syntax error, incorrect syntax near 'as'.
```

ここで、SELECT句でオブジェクト型を指定した場合を確認してみましょう。

```sql
select { x: 1 }
/*
[
  {
    "$1": {
      "x": 1
    }
  }
]
*/
```
そのオブジェクトをプロパティに持つオブジェクトを取得できました。

今度は、オブジェクト型に対してSELECT VALUEとして値を取得してみましょう。オブジェクトそのものを取得できます。

```sql
select value { x: 1 }
-- select 1 as xと同じ結果になる
/*
[
  {
    "x": 1
  }
]
*/
```

少し混乱しそうになりますが、この結果は`select 1 as x`と同じ結果になります。
というよりも
```sql
select 1 as x
```
は、次の「糖衣構文」とのことです。
```sql
select value { x: 1 }
```

同じ結果になるわけですね。

### 参考

- [Azure Cosmos DB での SELECT 句 &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-select)