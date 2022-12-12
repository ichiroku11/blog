---
layout: post
title: "EF Core - トランザクション中のリトライ処理はエラーになる"
date: 
tags: efcore
---


EF Coreでリトライ処理を指定した場合、トランザクションを使っている部分でInvalidOperationExceptionが発生します。

```
InvalidOperationException: 構成された実行戦略 'SqlServerRetryingExecutionStrategy' は、ユーザーが開始したトランザクションをサポートしていません。
'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。
```

こちらのドキュメントより。

[接続の回復 - EF Core &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/ef/core/miscellaneous/connection-resiliency#execution-strategies-and-transactions)

コード上ではこのあたりかなと。

[efcore/ExecutionStrategy.cs at main · dotnet/efcore · GitHub](https://github.com/dotnet/efcore/blob/main/src/EFCore/Storage/ExecutionStrategy.cs#L385)
