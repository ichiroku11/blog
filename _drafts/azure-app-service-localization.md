---
layout: post
title: "Azure App Service - ASP.NET Coreのカルチャーを変更する"
date: 
tags: azure-app-service aspnetcore
---

Azure App ServiceでASP.NET Core 6のプログラムを動かしたらカルチャーが英語（en-US）だったので日本語に変更する方法。

RequestLocalizationOptions.SetDefaultCultureメソッドとRequestLocalizationMiddlewareを使う感じで良さそう。

```csharp
using System.Globalization;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

builder.Services.Configure<RequestLocalizationOptions>(options => {
	// 日本語に変更する
	options.SetDefaultCulture("ja-JP");
});

var app = builder.Build();

app.UseRequestLocalization();

app.MapGet("/", () => {
	var content = new StringBuilder()
		.AppendLine($"{nameof(CultureInfo.CurrentCulture)}: {CultureInfo.CurrentCulture}")
		.AppendLine($"{nameof(CultureInfo.CurrentUICulture)}: {CultureInfo.CurrentUICulture}")
		.ToString();
	return content;
});

app.Run();
```

### 参考

- [ASP.NET Core のグローバリゼーションおよびローカリゼーション &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/fundamentals/localization?view=aspnetcore-6.0)
- [RequestLocalizationOptions.SetDefaultCulture(String) メソッド (Microsoft.AspNetCore.Builder) &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/api/microsoft.aspnetcore.builder.requestlocalizationoptions.setdefaultculture?view=aspnetcore-6.0)

