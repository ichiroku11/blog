---
layout: post
title: "T-SQL - sp_executesqlを使ったクエリの実行を確認する"
date: 
tags: t-sql
---

EF CoreやADO.NETでパラメーターがあるSQLを実行するときに`sp_executesql`ストアドプロシージャが使われます。

// todo:
SqlCommandのリンクと引用

- [sp_executesql (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)

そういえば手が書いて実行したことがないなと思ったので、`sp_executesql`を使ったクエリの実行を確認してみました。

### パラメーターを1つ渡す

```sql
execute sp_executesql
    N'select @p as value',
    N'@p int',
    '1';

-- todo: 実行結果
```

```sql
execute sp_executesql
    @stmt = N'select @p as Value',
    @params = N'@p int',
    @p = '1';

-- todo: 実行結果
```
