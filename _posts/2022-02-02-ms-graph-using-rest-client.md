---
layout: post
title: "REST Clientを使ってMicrosoft Graph APIを呼び出す"
date: 2022-02-
tags: vscode ms-graph
---

Visual Studio Codeの拡張機能「REST Client」を使ってMicrosoft Graph APIを呼び出し、Azure AD B2Cのユーザー一覧を取得するサンプル。

流れとしては次の2ステップ。

- アクセストークンを取得する
- アクセストークンを使用してAPIを呼び出す

```
### アクセストークンを取得
# @name getaccesstoken
POST https://login.microsoftonline.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id={clientId}
&client_secret={clientSecret}
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&grant_type=client_credentials

### ユーザー一覧を取得
GET https://graph.microsoft.com/v1.0/users
Authorization: Bearer {{getaccesstoken.response.body.$.access_token}}
Host: graph.microsoft.com
```

{tenant}、{clientId}、{clientSecret}はテナントのパラメータを指定してください。

1つ目のPOSTでアクセストークンを取得できると次のようなレスポンスを取得できます。
この中の`access_token`を使ってGraph APIを呼び出します。

```json
{
  "token_type": "Bearer",
  "expires_in": 0000,
  "ext_expires_in": 0000,
  "access_token": "eyJ***"
}
```

REST Clientでは`# @name getaccesstoken`でリクエストに名前を指定できて（Request Variablesと呼ぶみたい）、
レスポンスのアクセストークンを`{{getaccesstoken.response.body.$.access_token}}`で取得できます。


### 参考
- [ユーザーなしでアクセスを取得 - Microsoft Graph | Microsoft Docs](https://docs.microsoft.com/ja-jp/graph/auth-v2-service)
	4. アクセス トークンを取得する
	5. アクセス トークンを使用して、Microsoft Graph を呼び出す
- [REST Client - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
- [Microsoft Graph API を使用する - Microsoft Graph | Microsoft Docs](https://docs.microsoft.com/ja-jp/graph/use-the-api)
