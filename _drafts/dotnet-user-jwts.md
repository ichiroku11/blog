---
layout: post
title: dotnet user-jwtsで開発用のJWTを用意する
date: 
tags: dotnet aspnetcore jwt
---

.NET 7で追加された`dotnet user-jwts`を使うと、開発用のJWTを作成したり管理できます。JWTを扱うASP.NET Core Web APIの開発が捗ります。

(Generate tokens with dotnet user-jwts &#124; Microsoft Learn)[https://learn.microsoft.com/en-us/aspnet/core/security/authentication/jwt-authn?view=aspnetcore-7.0&tabs=windows]

今回は、下記コマンドを実行したときに生成されるJWTなどを確認していきます。

```batch
dotnet user-jwts create --name TestUser --scope "api:read" --scope "api:write"
```

### コマンドの実行結果

コマンドを実行すると、JWTのID、指定した名前やスコープ、エンコードされたJWTが出力されます。

##### 実行結果：
```
New JWT saved with ID '13330d77'.
Name: TestUser
Scopes: api:read, api:write

Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IlRlc3RVc2VyIiwic3ViIjoiVGVzdFVzZXIiLCJqdGkiOiIxMzMzMGQ3NyIsInNjb3BlIjpbImFwaTpyZWFkIiwiYXBpOndyaXRlIl0sImF1ZCI6WyJodHRwOi8vbG9jYWxob3N0OjI3Mjc5IiwiaHR0cHM6Ly9sb2NhbGhvc3Q6NDQzNTMiLCJodHRwczovL2xvY2FsaG9zdDo3MTg1IiwiaHR0cDovL2xvY2FsaG9zdDo1MjIzIl0sIm5iZiI6MTY3ODQ5NDE3MywiZXhwIjoxNjg2NDQyOTczLCJpYXQiOjE2Nzg0OTQxNzQsImlzcyI6ImRvdG5ldC11c2VyLWp3dHMifQ.50FR9zdxRV1J8qBiKrRHRjIYSDYIOL0oYtTIna5mII4
```

生成されたJWTをデコードして確認してみましょう。

##### JWTのヘッダー：
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

##### JWTのペイロード：
```json
{
  "unique_name": "TestUser",
  "sub": "TestUser",
  "jti": "13330d77",
  "scope": [
    "api:read",
    "api:write"
  ],
  "aud": [
    "http://localhost:27279",
    "https://localhost:44353",
    "https://localhost:7185",
    "http://localhost:5223"
  ],
  "nbf": 1678494173,
  "exp": 1686442973,
  "iat": 1678494174,
  "iss": "dotnet-user-jwts"
}
```

JWTのペイロードのクレームは、次のようになっていました。
|クレーム|説明|
|-|-|
|`unique_name`|`--name`オプションで指定したユーザー名|
|`sub`|`--name`オプションで指定したユーザー名|
|`jti`|JWTのID|
|`scope`|`--scope`オプションで指定したスコープ|
|`aud`|ASP.NET CoreプロジェクトのlaunchSettings.jsonに含まれるURL|
|`nbf`|JWTの発行日時|
|`exp`|`nbf`の6ヶ月後|
|`iat`|おそらくJWTの発行日時（`nbf`と誤差ありますが）|
|`iss`|デフォルトでは`dotnet-user-jwts`|

コマンドのオプションとクレームの詳細は、Microsoft Learnの記事を確認しましょう。

### プロジェクトの変更点

JWTを生成するとプロジェクトに2つ変更が加えられました。

1つ目はプロジェクトにシークレットが追加されます。`dotnet user-secrets list`で確認したところ、署名鍵や発行者の情報が格納されていました。
```
Authentication:Schemes:Bearer:SigningKeys:0:Value = {なんとなく省略}
Authentication:Schemes:Bearer:SigningKeys:0:Length = 32
Authentication:Schemes:Bearer:SigningKeys:0:Issuer = dotnet-user-jwts
Authentication:Schemes:Bearer:SigningKeys:0:Id = 90d3d357
```

2つ目はappsettings.Developent.jsonにBearerを使った認証の設定が追加されます。

```json
{
  "Authentication": {
    "Schemes": {
      "Bearer": {
        "ValidAudiences": [
          "http://localhost:27279",
          "https://localhost:44353",
          "https://localhost:7185",
          "http://localhost:5223"
        ],
        "ValidIssuer": "dotnet-user-jwts"
      }
    }
  }
}
```

JWTの検証時にTokenValidationParametersクラスでこのあたりの情報が使われるんだろうと思います。

(TokenValidationParameters Class (Microsoft.IdentityModel.Tokens) &#124; Microsoft Learn)[https://learn.microsoft.com/en-us/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=msal-web-dotnet-latest]
