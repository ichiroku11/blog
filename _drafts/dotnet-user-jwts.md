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

#### 実行結果

コマンドを実行するとJWTが生成されます。ID、指定した名前やスコープ、JWTを確認できます。

```
New JWT saved with ID '13330d77'.
Name: TestUser
Scopes: api:read, api:write

Token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1bmlxdWVfbmFtZSI6IlRlc3RVc2VyIiwic3ViIjoiVGVzdFVzZXIiLCJqdGkiOiIxMzMzMGQ3NyIsInNjb3BlIjpbImFwaTpyZWFkIiwiYXBpOndyaXRlIl0sImF1ZCI6WyJodHRwOi8vbG9jYWxob3N0OjI3Mjc5IiwiaHR0cHM6Ly9sb2NhbGhvc3Q6NDQzNTMiLCJodHRwczovL2xvY2FsaG9zdDo3MTg1IiwiaHR0cDovL2xvY2FsaG9zdDo1MjIzIl0sIm5iZiI6MTY3ODQ5NDE3MywiZXhwIjoxNjg2NDQyOTczLCJpYXQiOjE2Nzg0OTQxNzQsImlzcyI6ImRvdG5ldC11c2VyLWp3dHMifQ.50FR9zdxRV1J8qBiKrRHRjIYSDYIOL0oYtTIna5mII4
```

#### JWTの中身

// todo:
生成されたJWTをデコードして確認してみましょう。

ヘッダー：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

ペイロード：
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

// todo

- .csprojにシークレットが追加される
- dotnet user-secretsに署名鍵が格納される
- appsettings.Developent.jsonにBearerの設定が追加される

- dotnet user-jwts print 13330d77 --show-all
- dotnet user-jwts list
- dotnet user-jwts clear

