---
layout: post
title: "EF Core - FromSqlとFromSqlInterpolatedは同じ"
date: 
tags: efcore
---

.NET 7.0から生SQLを実行する`FromSql`メソッドが追加されました。

// todo: link

既存の`FromSqlInterpolated`メソッドや`FromSqlRaw`メソッドとの違いが気になったので調べたみたところ、`FromSql`メソッドと`FromSqlInterpolated`メソッドは同じようです。

// todo: table?

追加された理由を調べてみるとイシューが見つかりました。

// todo:
https://github.com/dotnet/efcore/issues/28609

生SQLを実行するデフォルトのメソッドとして、
サフィックスがないメソッドを用意したという感じですね。

https://learn.microsoft.com/ja-jp/ef/core/querying/sql-queries
