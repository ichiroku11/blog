---
layout: post
title: "Azure Cosmos DB - SQLクエリの結合とサブクエリを使って集計する"
date: 
tags: azure-cosmos-db
---

サンプルデータを用意して結合・サブクエリを使った集計を試します。

いつものようにどこかの焼き鳥屋さんの注文と注文明細のようなサンプルデータを用意しました。システムによって生成されるプロパティは省略しています。

この注文データに対して`id`別の注文合計金額（`details.price * details.quantity`のsum）を取得してみたいと思います。

```json
[
  {
    "id": "1",
    "details": [
      {
        "menu": "純けい",
        "price": 360,
        "quantity": 3
      },
      {
        "menu": "しろ",
        "price": 330,
        "quantity": 2
      }
    ]
  },
  {
    "id": "2",
    "details": [
      {
        "menu": "若皮",
        "price": 330,
        "quantity": 2
      }
    ]
  }
]
```

### 結合（JOIN）を使ったクエリで集計する

集計する前にINキーワードを使ったJOINの結果を確認します。
結果はクロス積になるので`details`が平坦化されます。`details`の項目1つ1つに`id`が付与された結果になっていることがわかります。

##### クエリ：

```sql
select
  o.id,
  --o.details,
  d.menu,
  d.price,
  d.quantity
from o
  join d in o.details
```

##### 結果：

```json
[
  {
    "id": "1",
    "menu": "純けい",
    "price": 360,
    "quantity": 3
  },
  {
    "id": "1",
    "menu": "しろ",
    "price": 330,
    "quantity": 2
  },
  {
    "id": "2",
    "menu": "若皮",
    "price": 330,
    "quantity": 2
  }
]
```

このクエリにグルーピングと集計関数を使うと、求める合計を取得できます。

##### クエリ：

```sql
select
  o.id,
  sum(d.price * d.quantity) as total
from o
  join d in o.details
--order by o.id
group by o.id
```

##### 結果：

```json
[
  {
    "id": "2",
    "total": 660
  },
  {
    "id": "1",
    "total": 1740
  }
]
```

`id`順で並び替えをしようとして上記クエリに`order by`句を足したらエラーになりました。並び替えたい場合は次のようにサブクエリにする必要があるのかもしれません。

```sql
select *
from (
  select
    o.id,
    sum(d.price * d.quantity) as total
  from o
    join d in o.details
  group by o.id
)
```

### サブクエリを使って集計する

サブクエリを使って合計を集計するクエリです。このクエリでも同じ結果を取得できました。

##### クエリ：
```sql
select
  o.id,
  (select value sum(d.price * d.quantity) from d in o.details) as total
from o
order by o.id
```

##### 結果：

```json
[
  {
    "id": "1",
    "total": 1740
  },
  {
    "id": "2",
    "total": 660
  }
]
```

### 参考

- [Azure Cosmos DB の SQL JOIN クエリ &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-join)
- [Azure Cosmos DB の SQL サブクエリ &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-subquery)

