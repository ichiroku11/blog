---
layout: post
title: "T-SQL - sp_executesqlを使ったクエリの実行を確認する"
date: 
tags: t-sql
---

EF CoreやADO.NETでは、パラメーターがあるSELECT文などを実行するときに`sp_executesql`ストアドプロシージャが使われます。

[SqlCommand Class (Microsoft.Data.SqlClient) &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/api/microsoft.data.sqlclient.sqlcommand#remarks)

Remarksの表にある`ExecuteReader`の説明には次のようにあります。
```
Executes commands that return rows. For increased performance, ExecuteReader invokes commands using the Transact-SQL sp_executesql system stored procedure. Therefore, ExecuteReader might not have the effect that you want if used to execute commands such as Transact-SQL SET statements.

行を返すコマンドを実行します。パフォーマンスを向上させるために、ExecuteReaderはTransact-SQLのsp_executesqlシステムストアドプロシージャを使用してコマンドを呼び出します。したがって、Transact-SQLのSET文などのコマンドを実行するためにExecuteReaderを使用すると、期待する効果が得られない場合があります。
```

そういえば`sp_executesql`は手が書いて実行したことがないなと思ったので、`sp_executesql`を使ったクエリを確認してみました。

[sp_executesql (Transact-SQL) - SQL Server &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)

## パラメーターを1つ渡す

パラメーターを1つ渡す場合は次のようにします。それぞれの引数は次のような意味です。
1. SQL文（文字列）
2. パラメーター名＋型（文字列）
3. パラメーターの値（文字列）

```sql
execute sp_executesql
    N'select @p as value',
    N'@p int',
    '1';

-- 実行結果
/*
Value
1
*/
```

パラメーター名を指定する場合は以下のようになります。
パラメーターの値は、`@p`のようにSQL文内に埋め込むパラメーター名を指定します。

```sql
-- パラメーター名を指定する
execute sp_executesql
    @stmt = N'select @p as Value',
    @params = N'@p int',
    @p = '1';
```

## パラメーターを2つ（複数）渡す

パラメーターを2つ渡す場合です。
パラメーターの型を指定（`@params`）では、`@p1 int, @p2 int`のようにカンマ区切りで複数のパラメーターを指定します。

```sql
execute sp_executesql
    N'select @p1 as Value1, @p2 as Value2',
    N'@p1 int, @p2 int',
    '1',
    '2';

-- 実行結果
/*
Value1	Value2
1	2
*/

-- パラメーター名を指定する
execute sp_executesql
    @stmt = N'select @p1 as Value1, @p2 as Value2',
    @params = N'@p1 int, @p2 int',
    @p1 = '1',
    @p2 = '2';
```

## OUT、OUTPUTパラメーターを使う

OUT、OUTPUTパラメーターを使う場合は、次のように指定します。どうも`out`のキーワードを2カ所指定する必要があるようです。

```sql
declare @p int;

execute sp_executesql
    N'select @p = 1',
    N'@p int out',
    @p out;

select @p as Value;
-- 実行結果
/*
Value
1
*/
```

## 変数を使ってストアドプロシージャを呼び出す

// todo: