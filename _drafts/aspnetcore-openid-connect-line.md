---
layout: post
title: "ASP.NET Core - LINEログインに対応する"
date: 
tags: dotnet aspnetcore openid-connect
---

ASP.NET CoreのWebアプリケーションにLINEログインを組み込むミニマムサンプルです。

[LINEログイン | LINE Developers](https://developers.line.biz/ja/services/line-login/)

LINEログインはOpenID Connectをサポートしているので、OpenID Connect関連の設定を構成していく感じでOKです。

この記事では、LINE Developers上の設定は省略します。

### 最低限すること

// todo:
- nugetで`Microsoft.AspNetCore.Authentication.OpenIdConnect`インストール
- LINE上のヘルプとか

### LINEログインに対応したミニマムなサンプル


```csharp
// Program.cs
using Microsoft.AspNetCore.Authentication.Cookies;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;
using Microsoft.IdentityModel.Tokens;
using System.Security.Claims;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

var services = builder.Services;

services
	.AddAuthentication(options => {
		options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
		options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
	})
	.AddCookie(options => {
	})
	.AddOpenIdConnect(options => {
		// todo:
		options.ClientId = "{チャネルID}";
		options.ClientSecret = "{チャネルシークレット}";

		// todo:
		options.MetadataAddress = "https://access.line.me/.well-known/openid-configuration";

		// todo:
		options.ResponseType = OpenIdConnectResponseType.Code;

		// todo:
		options.TokenValidationParameters.IssuerSigningKey = new SymmetricSecurityKey(Encoding.ASCII.GetBytes(options.ClientSecret));
	});

services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapGet("/", () => "Hello World!");
app.MapGet("/protected", (ClaimsPrincipal user) => $"Hello {user.Identity?.Name}!")
	.RequireAuthorization();

app.Run();
```

検索して見つかったサンプルは古いものが多かったような気がしたのでこの記事を書いてみました。

### 参考
- // todo:
