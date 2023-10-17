---
layout: post
title: "EF Core - FromSqlとFromSqlInterpolatedは同じ"
date: 2023-10-18
tags: efcore
---

.NET 7.0から生SQLを実行する`FromSql`メソッドが追加されました。
既存の`FromSqlInterpolated`メソッドや`FromSqlRaw`メソッドとの違いが気になったので調べたみたところ、`FromSql`メソッドと`FromSqlInterpolated`メソッドは同じ機能のようです。

|メソッド|SQLインジェクション攻撃に対して|
|--|--|
|[`FromSql`](https://learn.microsoft.com/ja-jp/dotnet/api/microsoft.entityframeworkcore.relationalqueryableextensions.fromsql)|安全（パラメーター化される）|
|[`FromSqlInterpolated`](https://learn.microsoft.com/ja-jp/dotnet/api/microsoft.entityframeworkcore.relationalqueryableextensions.fromsqlinterpolated)|安全（パラメーター化される）|
|[`FromSqlRaw`](https://learn.microsoft.com/ja-jp/dotnet/api/microsoft.entityframeworkcore.relationalqueryableextensions.fromsqlraw)|安全**ではない**（パラメーター化**されない**）|

追加された理由を調べてみるとイシューが見つかりました。

[Add FromSql and ExecuteSql accepting FormattableString · Issue #28609 · dotnet/efcore](https://github.com/dotnet/efcore/issues/28609)

生SQLを実行するデフォルトのメソッドとしてより選択されやすいように、サフィックスがない`FromSql`メソッドを用意したという感じですね。

### 参考
- [SQL クエリ - EF Core &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/ef/core/querying/sql-queries)
