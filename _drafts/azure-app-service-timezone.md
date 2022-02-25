---
layout: post
title: "Azure App Service - タイムゾーンの変更"
date: 
tags: azure-app-service
---

Azure App Serviceのタイムゾーンを変更する方法。

日本のタイムゾーンを指定するには、アプリケーション設定に下記を追加する。
```
Key = WEBSITE_TIME_ZONE
Value = Tokyo Standard Time
```

### 参考
- [構成に関する FAQ - Azure App Service &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/app-service/faq-configuration-and-management)