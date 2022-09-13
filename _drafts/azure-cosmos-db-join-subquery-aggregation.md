---
layout: post
title: "Azure Cosmos DB - SQLクエリの結合とサブクエリを使って集計する"
date: 
tags: azure-cosmos-db
---

サンプルデータを用意して、結合・サブクエリを使った集計を試します。

いつものようにどこかの焼き鳥屋さんの注文と注文明細のようなサンプルデータを用意しました。システムによって生成されるプロパティは省略しています。

この注文データに対して`id`別の注文合計金額（`details.price * details.quantity`の合計）をクエリで取得してみたいと思います。

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

JOINを使って取得するクエリは次のようになります。
// todo: 

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

// todo: 並び替えしようと思ったら、select from nestにする必要がある？
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

https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-join
