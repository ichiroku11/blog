---
layout: post
title: "Azure App Service - ランタイム監視と手動監視が設定された場合は手動監視が有効になる"
date: 2023-02-04
tags: azure-app-service azure-monitor azure-app-insights
---

Azure App ServiceにデプロイしたASP.NET CoreアプリケーションでApplication Insightsを利用する方法は2つあるようです。

1. ランタイム監視（自動インストルメンテーションのアプリケーション監視）
    - ポータルで有効化する
2. 手動監視
    - SDKを利用する（NuGetでMicrosoft.ApplicationInsights.AspNetCoreをインストールする）
        - https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore

2つとも設定した場合はどうなるのか気になったので調べてみたらドキュメントに記載がありました。ASP.NET CoreではSDKによる手動監視になるようです。

以下引用です。

```
自動インストルメンテーションの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合、.NET では、手動のインストルメンテーション設定のみが受け入れられます。
```

[Azure App Service のパフォーマンスの監視 &#124; Azure Monitor &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/azure-monitor/app/azure-web-apps)
