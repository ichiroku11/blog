---
layout: post
title: "Azure Cosmos DB - SQLクエリのリテラルを確認する"
date: 2022-07-01
tags: azure-cosmos-db
---

Azure Cosmos DBのSQLクエリにおけるリテラル（定数）を確認します。

JavaScriptのリテラルと同じかなと思いました。

次のクエリを実行してみましょう。

```sql
select
	undefined,
	null,
	true,
	0,
	"",
	[],
	{}
```

だいたい想像通りかなと思いますが実行結果です。

```json
[
  {
    "$2": null,
    "$3": true,
    "$4": 0,
    "$5": "",
    "$6": [],
    "$7": {}
  }
]
```

undefinedは無視されるっぽいです。Docsでの記載はこのあたりのことかな。

```
オブジェクトを作成するときに、未定義の値が割り当てられているプロパティはすべてスキップされ、作成されたオブジェクトに含まれません。
```

[Azure Cosmos DB の SQL クエリでのスカラー式 &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-scalar-expressions)

### 参考

- [Azure Cosmos DB の SQL 定数 &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-constants)

