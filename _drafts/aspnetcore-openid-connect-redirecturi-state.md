---
layout: post
title: "ASP.NET Core - AuthenticationProperties.RedirectUriはstateパラメーターにシリアライズされる"
date: 
tags: aspnetcore openid-connect
---

ASP.NET CoreでOpenID Connectによるログインを実装するときのお話。

IdPでログインした後のリダイレクト先を指定したい場合、`AuthenticationProperties.RedirectUri`プロパティを利用すると思います。次のようなコードですね。

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
        // ログイン後のリダイレクト先を指定する
        RedirectUri = Url.Action("Index", "Home"),
    };

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

Authentication Requestの`redirect_uri`はIdPに事前に登録する必要があるので、任意のURLにリダイレクトさせるには`AuthenticationProperties.RedirectUri`を利用することが多いのかなと思います。

実際には
1. Authentication Requestの`redirect_uri`で指定したURLにリダイレクト
2. `AuthenticationProperties.RedirectUri`プロパティで指定したURLにリダイレクト
と2回リダイレクトが発生します。


// todo:
/*
`AuthenticationProperties.RedirectUri`

[Final: OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs#L480
*/