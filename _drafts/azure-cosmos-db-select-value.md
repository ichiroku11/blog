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

SELECT句ではオブジェクト型のプロパティも指定できます。

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

オブジェクト型に対してSELECT VALUEとして値を取得すると、次のような結果になります。

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

次に2つのクエリは同じ結果になります。
```sql
select 1 as x
select value { x: 1 }
```

というよりも
```sql
select 1 as x
```
は、次の「糖衣構文」とのことです。
```sql
select value { x: 1 }
```


### 参考

- [ // todo:  ](https://docs.microsoft.com/ja-jp/azure/cosmos-db/sql/sql-query-select)