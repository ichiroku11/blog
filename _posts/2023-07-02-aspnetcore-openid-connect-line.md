---
layout: post
title: "ASP.NET Core - LINEログインに対応する"
date: 2023-07-02
tags: dotnet aspnetcore openid-connect line
---

ASP.NET CoreのWebアプリケーションにLINEログインを組み込むミニマムサンプルです。

[LINEログイン &#124; LINE Developers](https://developers.line.biz/ja/services/line-login/)

LINEログインはOpenID Connectをサポートしているので、OpenID Connect関連の設定を構成していく感じでOKです。

検索して見つかったサンプルは古いものが多かったような気がしたのでこの記事を書いてみました。

### LINログインに対応するためにすること

LINEログインに対応するには、次のことを行います。LINE Developersでプロバイダーとチャネルを生成する必要がありますが、この記事では省略しています。

- NuGetで`Microsoft.AspNetCore.Authentication.OpenIdConnect`をインストールする
- `AddOpenIdConnect`メソッドで`OpenIdConnectOptions`を構成する
	- チャネルIDとチャネルシークレットを指定する
	- ディスカバリーエンドポイントを指定する[^1]
	- `response_type`に`code`を指定する[^2]
	- チャネルシークレットを使って署名を検証する対称鍵を指定する[^1]

詳細は下記サンプルコードで。

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
		// Developersコンソールで確認できるチャネルIDとチャネルシークレット
		options.ClientId = "{チャネルID}";
		options.ClientSecret = "{チャネルシークレット}";

		// ディスカバリーエンドポイント
		options.MetadataAddress = "https://access.line.me/.well-known/openid-configuration";

		// response_typeはcode
		options.ResponseType = OpenIdConnectResponseType.Code;

		// 署名を検証する鍵
		// IDトークンは、クライアントシークレットの対称鍵で署名されている様子
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

ASP.NET Core 6.0で確認しました。

[^1]: [IDトークンからプロフィール情報を取得する &#124; LINE Developers](https://developers.line.biz/ja/docs/line-login/verify-id-token/#signature)

[^2]: [ウェブアプリにLINEログインを組み込む &#124; LINE Developers](https://developers.line.biz/ja/docs/line-login/integrate-line-login/#making-an-authorization-request)
