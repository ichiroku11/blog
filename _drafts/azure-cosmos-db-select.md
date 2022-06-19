---
layout: post
title: "Azure Cosmos DB - SQLクエリのSELECT句を確認する"
date: 
tags: azure-cosmos-db
---

Azure Cosmos DBのSQLクエリのSELECT句を確認します。

SELECT句で指定したプロパティを持つJSONオブジェクトを取得できます。
クエリの結果はJSONオブジェクトの配列になります。

下記クエリを実行してると、次の結果を取得できます。

```sql
select 1, true
```

```json
[
	{
		"$1": 1,
		"$2": true
	}
]
```

このクエリではSELECT句でプロパティのキーを指定していないので、プロパティ名は"$1"、"$2"といった名前が使われています。
暗黙の引数変数（implicit argument variable）と呼ぶようです。

結果のJSONオブジェクトのプロパティ名を指定するには次のようにします。"as"は省略できます。

```sql
select 1 as x, true as y
```

```json
[
	{
		"x": 1,
		"y": true
	}
]
```

### 参考

- [Azure Cosmos DB での JSON の使用 &#124; Microsoft Docs](https://docs.microsoft.com/en-us/azure/cosmos-db/sql/sql-query-working-with-json)

