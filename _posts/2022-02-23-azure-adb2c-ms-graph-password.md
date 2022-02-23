---
layout: post
title: "Microsoft Graph APIでB2Cのユーザーのパスワードをリセットするにはユーザー管理者ロールが必要"
date: 2022-02-23
tags: azure-ad-b2c ms-graph
---

Microsoft Graph APIを使ってAzure AD B2Cのユーザーのパスワードをリセットするには、ユーザー管理者ロールが必要とのことです。

以下より引用。

[Microsoft Graph アプリケーションを登録する - Azure AD B2C &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/active-directory-b2c/microsoft-graph-get-started?tabs=app-reg-ga#optional-grant-user-administrator-role)

```
アプリケーションまたはスクリプトでユーザーのパスワードを更新する必要がある場合は、ユーザー管理者ロールをアプリケーションに割り当ててください。 ユーザー管理者ロールには、アプリケーションに付与する固定されたアクセス許可のセットがあります。
```

ちなみにGraph APIでパスワードをリセット（更新）するサンプルは下記を参照。

[ユーザーを更新する - Microsoft Graph v1.0 &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/graph/api/user-update?view=graph-rest-1.0&tabs=csharp#example-3-update-the-passwordprofile-of-a-user-to-reset-their-password)

ここの説明には`Directory.AccessAsUser.All`が必要とありますが、ユーザー管理者ロールに含まれているのかな。
