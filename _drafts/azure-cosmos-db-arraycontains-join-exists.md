---
layout: post
title: "Azure Cosmos DB - SQLクエリのARRAY_CONTAINS関数を確認する"
date: 
tags: azure-cosmos-db
---

サンプルデータを用意してARRAY_CONTAINS関数を確認してみます。
ARRAY_CONTAINS関数はJOINを使ったクエリとEXISTS式を使ったクエリに書き直すことができるので、あわせて確認したいと思います。

まずはデータを用意しました。アイテムが3つあります。
いつものようにどこかの焼き鳥屋さんの注文と注文明細のようなデータをイメージしてもらえるとありがたいです。
システムによって生成されるプロパティは省略しています。

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
  },
  {
    "id": "3",
    "details": [
      {
        "menu": "純けい",
        "price": 360,
        "quantity": 1
      },
      {
        "menu": "若皮",
        "price": 330,
        "quantity": 3
      }
    ]
  }
]
```

明細のメニュー"しろ"を含んでいる注文を検索してみましょう。

### ARRAY_CONTAINS関数

ARRAY_CONTAINS関数の引数は3つあり、それずれ下記を指定します。
- 1つ目の引数は検索対象となる配列
- 2つ目の引数は検索する値
- 3つ目の引数は部分一致で検索するかどうか（省略した場合はfalse）
詳しくはDocs参照で。

この関数を使って明細にメニュー"しろ"を含む注文を取得するクエリは次のようになります。

##### クエリ：

```sql
select *
from c
where array_contains(c.details, {menu: "しろ"}, true)
```

##### 結果：
```
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
  }
]
```

### JOINを使ったクエリで書き直す

JOIN（結合）を使っても同じ結果を取得できます。

##### クエリ：

```sql
select value c
from c
join d in c.details
where d.menu = "しろ"
```

### EXISTS式を使ったクエリで書き直す

今度はEXITS式を使って書き直してみます。サブクエリですね。

##### クエリ：

```sql
select *
from c
where exists(
    select *
    from d in c.details
    where d.menu = "しろ")
```

DocsによるとJOINよりEXISTS式の方がコストが低いとのことです。ARRAY_CONTAINS関数とEXISTS式の違いはそこまでないんですかね。

### 参考

- [Azure Cosmos DB クエリ言語の ARRAY_CONTAINS &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-array-contains)
- [Azure Cosmos DB の SQL サブクエリ &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-subquery#example-rewriting-array_contains-and-join-as-exists)

