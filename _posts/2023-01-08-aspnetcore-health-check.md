---
layout: post
title: "ASP.NET Core - 複数のヘルスチェック結果を判定しているところ"
date: 2023-01-08
tags: aspnetcore
---

ASP.NET Coreのヘルスチェック（正常性チェック）では、複数のヘルスチェックを行った場合、その結果の中で最も悪いステータスが結果として返されます。
例えばヘルスチェックが2つあり、一方がHealthyでもう一方がUnhealthyの場合、結果としてはUnhealthyになります。

この処理をどのあたりのコードでやってるのかなと調べてみると、`HealthReport.CalculateAggregateStatus`で求めていました。引用します。

```csharp
private static HealthStatus CalculateAggregateStatus(IEnumerable<HealthReportEntry> entries)
{
    // This is basically a Min() check, but we know the possible range, so we don't need to walk the whole list
    var currentValue = HealthStatus.Healthy;
    foreach (var entry in entries)
    {
        if (currentValue > entry.Status)
        {
            currentValue = entry.Status;
        }

        if (currentValue == HealthStatus.Unhealthy)
        {
            // Game over, man! Game over!
            // (We hit the worst possible status, so there's no need to keep iterating)
            return currentValue;
        }
    }

    return currentValue;
```

[https://github.com/dotnet/aspnetcore/blob/main/src/HealthChecks/Abstractions/src/HealthReport.cs#L60-L79](https://github.com/dotnet/aspnetcore/blob/main/src/HealthChecks/Abstractions/src/HealthReport.cs#L60-L79)

### 参考
- [ASP.NET Core の正常性チェック &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/aspnet/core/host-and-deploy/health-checks?view=aspnetcore-7.0)
