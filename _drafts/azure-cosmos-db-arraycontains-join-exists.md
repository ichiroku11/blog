---
layout: post
title: "Azure Cosmos DB - SQLクエリのARRAY_CONTAINS関数を確認する"
date: 
tags: azure-cosmos-db
---

サンプルデータを用意してARRAY_CONTAINS関数を確認してみます。
ARRAY_CONTAINS関数はJOINを使ったクエリとEXISTSを使ったクエリに書き直すことができるので、あわせて確認したいと思います。

まずはデータを用意しました。アイテムが3つあります。
いつものようにどこかの焼き鳥屋さんの注文と注文明細のようなデータをイメージしてもらえると。
システムによって生成されるプロパティは省略しています。
```json

```

明細のメニュー"しろ"を含んでいる注文を検索してみましょう。


### ARRAY_CONTAINS関数

関数の引数にはをそれずれ下記を指定します。
- 1つ目の引数は検索対象となる配列
- 2つ目の引数は検索する値
- 3つ目の引数は部分一致で検索するかどうか（省略した場合はfalse）

詳しくはDocs参照で。

```
```



##### クエリ：
```sql
```

##### 結果：
```
```


### JOINを使ったクエリで書き直す

##### クエリ：
```sql
```

### EXISTSを使ったクエリで書き直す

EXITSを使って書き直します。サブクエリですね。

##### クエリ：
```sql
```


### 参考

- [Azure Cosmos DB クエリ言語の ARRAY_CONTAINS &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-array-contains)
- [Azure Cosmos DB の SQL サブクエリ &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-subquery#example-rewriting-array_contains-and-join-as-exists)

