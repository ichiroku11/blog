---
layout: post
title: "Azure App Service - ランタイム監視とSDKによる手動監視が有効な場合は手動監視が有効になる"
date:
tags: azure-app-service azure-monitor azure-app-insights
---

Azure App ServiceでApplication Insightsを利用する方法は2つあるようです。

- ランタイム監視
    - ポータルで有効化する
- 手動監視
    - SDKを使う（NuGetでMicrosoft.ApplicationInsightsをインストールする）

どちらも設定した場合はどうなるのか気になったので調べてみたら、ドキュメントに記載がありました。

https://learn.microsoft.com/ja-jp/azure/azure-monitor/app/azure-web-apps
