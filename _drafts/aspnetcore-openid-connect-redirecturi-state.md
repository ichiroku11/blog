---
layout: post
title: "ASP.NET Core - AuthenticationProperties.RedirectUriはstateパラメーターにシリアライズされる"
date: 
tags: aspnetcore openid-connect
---

ASP.NET CoreでOpenID Connectによるログインを実装して、IdPでログインした後、任意のURLにリダイレクトするときのお話です。

Authentication Requestの`redirect_uri`パラメーターで指定するURLはIdPに事前に登録する必要があるため、
IdPでログインした後の任意のURLにリダイレクトしたい場合、`AuthenticationProperties.RedirectUri`プロパティを利用すると思います。

次のようなコードでリダイレクト先を指定するときですね。

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
        // ログイン後のリダイレクト先を指定する
        RedirectUri = Url.Action("Index", "Home"),
    };

    // ログイン後の動きとしては、2回リダイレクトが発生する
    // 1. Authentication Requestのredirect_uriで指定したURL（IdPに登録したコールバックURL）にリダイレクト
    // 2. RedirectUriプロパティで指定したURLにリダイレクト
    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

`AuthenticationProperties.RedirectUri`で指定されたURLは、`redirect_uri`パラメーターには含まれないのですが、
どうしているかというと`state`パラメーターにシリアライズされてIdPに渡されて、リダイレクト後のコールバックで返されます。
といったあたりをコードから確認してみたいと思います。

IdPへのリダイレクトを処理している`OpenIdConnectHandler.HandleChallengeAsyncInternal`メソッドで、
`AuthenticationProperties`の値を何かしら保護（加工）して`state`パラメーターに設定してます。

[aspnetcore/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs#L480)

```cs
message.State = Options.StateDataFormat.Protect(properties);
```

// todo:

[aspnetcore/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectOptions.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectOptions.cs#L263)

```cs
```

// todo:

[aspnetcore/src/Security/Authentication/Core/src/SecureDataFormat.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/Core/src/SecureDataFormat.cs#L35-L47)

```cs
```


/*
[Final: OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)
*/