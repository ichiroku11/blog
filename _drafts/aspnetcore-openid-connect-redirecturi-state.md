---
layout: post
title: "ASP.NET Core - AuthenticationProperties.RedirectUriはstateパラメーターにシリアライズされる"
date: 
tags: aspnetcore openid-connect
---

ASP.NET CoreでOpenID Connectによるログインを実装して、IdPでログインした後、任意のURLにリダイレクトしたいときのお話です。

Authentication Requestの`redirect_uri`パラメーターで指定するURLはIdPに事前に登録する必要があるため、
IdPでログインした後の任意のURLにリダイレクトしたい場合、`AuthenticationProperties.RedirectUri`プロパティを利用すると思います。
次のようなコードですね。

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
        // ログイン後のリダイレクト先を指定する
        RedirectUri = Url.Action("Index", "Home"),
    };

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

このときログイン後の動きとしては、
1. Authentication Requestの`redirect_uri`で指定したURL（IdPに登録したコールバックURL）にリダイレクト
2. `AuthenticationProperties.RedirectUri`プロパティで指定したURLにリダイレクト
と2回リダイレクトが発生します。

`AuthenticationProperties.RedirectUri`で指定されたURLは、`redirect_uri`パラメーターには含まれないのですが、
どうしているかというと`state`パラメーターにシリアライズされてIdPに渡されて、リダイレクト後のコールバックで返されます。





/*
[Final: OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs#L480
*/