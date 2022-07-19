---
layout: post
title: "Azure Cosmos DB - SQLクエリのFROM INを確認する"
date: 2022-07-20
tags: azure-cosmos-db
---

今回はFROM句を使った下記2つのクエリの違いについて確認したいと思います。

- FROM句だけを使ったクエリ
- FROM句とINキーワードを使ったクエリ

ドキュメントはこのあたりですね。

https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-object-array#Iteration

FROM句でINキーワードを使うと配列を平坦化（フラット化）できます。Docs内では「イテレーション（反復）」といった用語が使われています。
C#のLINQでいうSelectManay、JavaScriptのArray.flatのような機能ですね。

クエリと結果を確認していきましょう。

次のようなデータがあるとします。アイテムが2つ。システムによって生成されるプロパティは省略しています。
どこかの焼き鳥屋さんの注文のようなデータをイメージしてもらえると。

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
    ],
  },
  {
    "id": "2",
    "details": [
      {
        "menu": "若皮",
        "price": 330,
        "quantity": 2
      }
    ],
  }
]
```

### FROM句だけを使ったクエリ

from句に`details`プロパティを指定して注文明細を取得してみましょう。

##### クエリ：
```sql
select *
from c.details
```

`details`プロパティの配列だけが抜き出されたような結果を取得できます。

##### 結果：
```json
[
  [
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
  ],
  [
    {
      "menu": "若皮",
      "price": 330,
      "quantity": 2
    }
  ]
]
```

### FROM句とINキーワードを使ったクエリ

続いてfrom句にinキーワードを使ってみると。

##### クエリ：
```sql
select *
from c in c.details
```

`details`プロパティの項目が平坦化されて結果全体が配列1つになります。

##### 結果：
```json
[
  {
    "menu": "純けい",
    "price": 360,
    "quantity": 3
  },
  {
    "menu": "しろ",
    "price": 330,
    "quantity": 2
  },
  {
    "menu": "若皮",
    "price": 330,
    "quantity": 2
  }
]
```
