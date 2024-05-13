---
layout: post
title: "Azure AD B2C - Graph APIを使って監査ログからユーザーがサインインしたアプリケーション（クライアント）と日時を取得する"
date:
tags: azure-ad-b2c ms-graph
---

Azure AD B2Cにおいて、ユーザーがサインインしたアプリケーション（クライアント）と日時を確認したい場合、AzureポータルではAzure AD B2Cの「監査ログ」で確認できます。

[監査ログへのアクセスと確認 - Azure AD B2C &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/active-directory-b2c/view-audit-logs)

アクティビティを`Issue an id_token to the application`とフィルターすればOKのようです。

[Microsoft Entra audit log activity reference - Microsoft Entra ID &#124; Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/reference-audit-activities)

|Audit Category|Activity|
|--|--|
|Authentication|Issue an id_token to the application|

ただ、ドキュメントにもあるようにログが保存されるのは7日間のみとのことで、より長期間保存したい場合は次のような方法があるようです。

- Azure ポータルでログをダウンロードする
- Graph APIでログを取得する
- Log Anlyticsにログを転送する

今回はGraph APIを使ってログを取得したいと思います。

// todo:

