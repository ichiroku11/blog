---
layout: post
title: "Azure Cosmos DB - 結合がクロス積になることを確認する"
date: 2022-10-23
tags: azure-cosmos-db
---

結合（JOIN）がクロス積になることを確認したいと思います。

まずは数値の配列からJOINしない結果を確認します。

##### クエリ：

```sql
select x
from x in (select value [1, 2, 3])
```

##### 結果：

```json
[
  {
    "x": 1
  },
  {
    "x": 2
  },
  {
    "x": 3
  }
]
```

このデータに2つのアイテムを持つ配列を結合してみると、3アイテムと2アイテムがクロス結合されて6アイテム取得できることが確認できます。

##### クエリ：

```sql
select x, y
from x in (select value [1, 2, 3])
join y in (select value ["a", "b"])
```

##### 結果：

```json
[
  {
    "x": 1,
    "y": "a"
  },
  {
    "x": 1,
    "y": "b"
  },
  {
    "x": 2,
    "y": "a"
  },
  {
    "x": 2,
    "y": "b"
  },
  {
    "x": 3,
    "y": "a"
  },
  {
    "x": 3,
    "y": "b"
  }
]
```
