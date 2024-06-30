---
layout: post
title: "Azure AD B2C - Graph APIを使って監査ログからユーザーがサインインしたアプリケーション（クライアント）と日時を取得する"
date: 2024-07-01
tags: azure-ad-b2c ms-graph
---

Azure AD B2Cにおいて、ユーザーがサインインしたアプリケーション（クライアント）と日時を確認したい場合、AzureポータルではAzure AD B2Cの「監査ログ」で確認できます。

[監査ログへのアクセスと確認 - Azure AD B2C &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/active-directory-b2c/view-audit-logs)

Azureポータルでは、アクティビティを`Issue an id_token to the application`とフィルターすればOKのようです。

[Microsoft Entra audit log activity reference - Microsoft Entra ID &#124; Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/reference-audit-activities#b2c)

|Audit Category|Activity|
|--|--|
|Authentication|Issue an id_token to the application|

ただ、ドキュメントにもあるようにログが保存されるのは7日間のみとのことです。より長期間保存したい場合は何か考える必要があります。例えば次のような方法がありそうです。

- Azure ポータルでログをダウンロードする
- Graph APIでログを取得する
- Log Anlyticsにログを転送する

今回はGraph API（C#）を使ってログを取得してみました。

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

// 監査ログ一覧を取得
var response = await client.AuditLogs.DirectoryAudits.GetAsync(config => {
    // 「IDトークンの発行」=「サインイン」と判断する
    config.QueryParameters.Filter = "activityDisplayName eq 'Issue an id_token to the application'";
});

foreach (var audit in response?.Value ?? []) {
    var json = JsonSerializer.Serialize(new {
        // アクティビティが実行された日時（UTC）
        // サインイン日時とする
        audit.ActivityDateTime,
        // アクティビティ名：'Issue an id_token to the application'
        audit.ActivityDisplayName,
        // アクティビティの結果
        audit.Result,
        // アクティビティを開始したアプリケーション
        // サインインしたアプリケーションのクライアントID
        audit.InitiatedBy?.App?.ServicePrincipalName,
        // ターゲットリソース
        // サインインしたユーザーのID
        // たぶん1つなんだろうけどコレクションなので配列として出力
        objectIds = audit.TargetResources?.Select(resource => resource.Id),
    });
    Console.WriteLine(json);
}
```

上記コードでは下記パッケージを利用しています。

- Azure.Identity
    - [NuGet Gallery &#124; Azure.Identity 1.12.0](https://www.nuget.org/packages/Azure.Identity)
- Microsoft.Graph
    - [NuGet Gallery &#124; Microsoft.Graph 5.56.0](https://www.nuget.org/packages/Microsoft.Graph)

また、コードを実行にするには`AuditLog.Read.All`のアクセス許可も必要です。

[Microsoft Graph のアクセス許可のリファレンス - Microsoft Graph &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/graph/permissions-reference#auditlogreadall)
