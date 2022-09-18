---
layout: post
title: "Azure Cosmos DB - 結合がクロス積になることを確認する"
date: 
tags: azure-cosmos-db
---


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
