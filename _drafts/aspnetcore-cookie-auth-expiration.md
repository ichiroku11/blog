---
layout: post
title: "ASP.NET Coreの認証クッキーの有効期限のデフォルト値と変更方法"
date: 
tags: aspnetcore
---

ASP.NET Coreでのクッキー認証の有効期限のプロパティは`CookieAuthenticationOptions.ExpireTimeSpan`です。

// todo:
https://docs.microsoft.com/ja-jp/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan

デフォルト値は14日のようです。
Docsには記載がないようですが、CookieAuthenticationOptionsのコンストラクターで設定している値がそれかなと。

// todo:
https://github.com/dotnet/aspnetcore/blob/a708a5adf17510ecf297368b59ba5e5d20a20999/src/Security/Authentication/Cookies/src/CookieAuthenticationOptions.cs#L30

```csharp
ExpireTimeSpan = TimeSpan.FromDays(14);
```

変更する方法は次のような感じで。

```csharp
services
	.AddAuthentication(options => {
		options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
	})
	.AddCookie(options => {
		// 有効期限を1日にするとしたら
		options.ExpireTimeSpan = TimeSpan.FromDays(1);
	});
```

似ているプロパティに`CookieAuthenticationOptions.Cookie.Expiration`がありますが、この値は無視されるとのことです。注意しましょう。

```
Expiration is currently ignored. Use ExpireTimeSpan to control lifetime of cookie authentication.
```

// todo:
https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.cookie?view=aspnetcore-6.0

ASP.NET Core 6.0で確認。

#### 参考
// todo:
- https://docs.microsoft.com/ja-jp/aspnet/core/security/authentication/cookie?view=aspnetcore-6.0

