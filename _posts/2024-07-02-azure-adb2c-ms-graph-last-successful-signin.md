---
layout: post
title: "Azure AD B2C - Graph APIを使って最後に成功したサインイン日時（lastSuccessfulSignInDateTime）を取得する"
date: 2024-07-02
tags: azure-ad-b2c ms-graph
---

Microsoft Entra IDで最後に成功したサインイン日時を取得できるようになったと見つけたので、Azure AD B2Cでも取得できるか試してみました。
その結果、取得できました。という内容です。

[非アクティブな Microsoft Entra ID ユーザーを調査](https://zenn.dev/microsoft/articles/5c489b9f6c4bb6)

`signInActivity`のドキュメントにも`LastSuccessfulSignInDateTime`が記載されてます。

[signInActivity リソースの種類 - Microsoft Graph v1.0 &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/graph/api/resources/signinactivity?view=graph-rest-1.0)

C#で確認してみたコードはこんな感じです。

```csharp
// クレデンシャル
var credential = new ClientSecretCredential(
    tenantId: "{テナントID}",
    clientId: "{クライアントID}",
    clientSecret: "{クライアントシークレット}",
    options: new TokenCredentialOptions {
        AuthorityHost = AzureAuthorityHosts.AzurePublicCloud,
    });

// スコープ
var scopes = new[] { "https://graph.microsoft.com/.default" };

var authenticationProvider = new AzureIdentityAuthenticationProvider(credential: credential, scopes: scopes);
var client = new GraphServiceClient(authenticationProvider);

// ユーザー一覧を取得
var response = await client.Users.GetAsync(config => {
    config.QueryParameters.Select = [
        // ID（GUID）
        "id",
        // サインインアクティビティ
        "signInActivity"
    ];
});

foreach (var user in response?.Value ?? []) {
    var json = JsonSerializer.Serialize(new {
        user.Id,
        // 最新の成功したサインイン日時
        user.SignInActivity?.LastSuccessfulSignInDateTime,
        // Microsoft.Graphのビルドが古い場合はこのプロパティが存在しないので、
        // user.SignInActivity?.AdditionalDataからキー"lastSuccessfulSignInDateTime"で取得できるっぽい
    });
    Console.WriteLine(json);
}
```

`signInActivity`を取得するには、`AuditLog.Read.All`のアクセス許可が必要とのことです。

下記ドキュメントの例11にもリクエストとレスポンスのサンプルがありました。

[ユーザーを一覧表示する - Microsoft Graph v1.0 &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/graph/api/user-list?view=graph-rest-1.0&tabs=csharp#example-11-get-users-including-their-last-sign-in-time)
