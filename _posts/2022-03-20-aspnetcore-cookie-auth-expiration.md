---
layout: post
title: "ASP.NET Coreの認証クッキーの有効期限のデフォルト値と変更方法"
date: 2022-03-20
tags: aspnetcore
---

ASP.NET Coreでのクッキー認証の有効期限は`CookieAuthenticationOptions.ExpireTimeSpan`で設定します。

[CookieAuthenticationOptions.ExpireTimeSpan プロパティ (Microsoft.AspNetCore.Authentication.Cookies) &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.expiretimespan)

### デフォルト値

デフォルト値は14日のようです。Docsに記載がなさそうですが、CookieAuthenticationOptionsのコンストラクターで設定している値がそれかなと。

```csharp
ExpireTimeSpan = TimeSpan.FromDays(14);
```

[aspnetcore/CookieAuthenticationOptions.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/Cookies/src/CookieAuthenticationOptions.cs#L30)

### 変更方法

変更するには次のような感じで。

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

[CookieAuthenticationOptions.Cookie Property (Microsoft.AspNetCore.Authentication.Cookies) &#124; Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.cookie?view=aspnetcore-6.0)

ASP.NET Core 6.0で確認しました。

#### 参考
- [ASP.NET Core Identity を使用せずに cookie 認証を使用する &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/security/authentication/cookie?view=aspnetcore-6.0)

