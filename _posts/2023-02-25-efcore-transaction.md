---
layout: post
title: "EF Core - トランザクション中のリトライ処理はエラーになる"
date: 2023-02-25
tags: efcore
---

EF Coreでリトライ処理を指定した場合、トランザクションを使っている部分でInvalidOperationExceptionが発生するようです。

```
InvalidOperationException: 構成された実行戦略 'SqlServerRetryingExecutionStrategy' は、ユーザーが開始したトランザクションをサポートしていません。
'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。
```

こちらのドキュメントより。

[接続の回復 - EF Core &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/ef/core/miscellaneous/connection-resiliency#execution-strategies-and-transactions)

コード上ではこのあたりかなと。

[efcore/ExecutionStrategy.cs at main · dotnet/efcore · GitHub](https://github.com/dotnet/efcore/blob/main/src/EFCore/Storage/ExecutionStrategy.cs#L385-L406)

```csharp
protected virtual void OnFirstExecution()
{
    if (RetriesOnFailure
        && (Dependencies.CurrentContext.Context.Database.CurrentTransaction is not null
            || Dependencies.CurrentContext.Context.Database.GetEnlistedTransaction() is not null
            || (((IDatabaseFacadeDependenciesAccessor)Dependencies.CurrentContext.Context.Database).Dependencies
                .TransactionManager as
                ITransactionEnlistmentManager)?.CurrentAmbientTransaction is not null))
    {
        throw new InvalidOperationException(
            CoreStrings.ExecutionStrategyExistingTransaction(
                GetType().Name,
                nameof(DbContext)
                + "."
                + nameof(DbContext.Database)
                + "."
                + nameof(DatabaseFacade.CreateExecutionStrategy)
                + "()"));
    }

    ExceptionsEncountered.Clear();
}
```
