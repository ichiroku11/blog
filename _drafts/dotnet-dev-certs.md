---
layout: post
title: "ASP.NET Core開発時の自己証明書を作り直す"
date: 
tags: dotnet aspnetcore cert
---

開発用の証明書の有効期限は1年のようなので、忘れたころに期限切れになりますね。

証明書を作り直すには.NET CLIのdev-certsコマンドで下記2つを実行します。

#### 証明書を削除する

```batch
dotnet dev-certs https --clean
```

カレントユーザーの個人の証明書から開発用の証明書が削除されます。

#### 証明書を作成して信頼する

```batch
dotnet dev-certs https --trust
```

カレントユーザーの個人の証明書には、`ASP.NET Core HTTPS development certificate`というフレンドリ名の証明書が生成されます。


### 参考
- [dotnet dev-certs コマンド - .NET CLI &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/core/tools/dotnet-dev-certs)
- [自己署名証明書の生成の概要 - .NET &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/core/additional-tools/self-signed-certificates-guide)
