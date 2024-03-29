---
layout: post
title: "ASP.NET Core - 実行中のアプリケーションの絶対URLを生成する"
date: 2022-08-05
tags: aspnetcore
---

UrlHelperのActionLink拡張メソッドを使うと実行中のアプリケーションの絶対URLを作成できます。

[UrlHelper.ActionLinkメソッドで絶対URLを取得する - ASP.NET Core MVC - いちろぐ](https://ichiroku11.hatenablog.jp/entry/2020/07/23/202404)

ソースコード上で確認してみました。

### UrlHelperBase.GenerateUrlメソッド

まずはUrlHelperのURLを作成するGenerateUrlメソッドの抜粋です。メソッドの引数`protocol`または`host`が指定されている場合、絶対URLを構築しているようです。（elseの方）

```csharp
protected string? GenerateUrl(string? protocol, string? host, string? virtualPath, string? fragment)
{
	// 省略

	try
	{
		var pathBase = ActionContext.HttpContext.Request.PathBase;

		if (string.IsNullOrEmpty(protocol) && string.IsNullOrEmpty(host))
		{
			AppendPathAndFragment(builder, pathBase, virtualPath, fragment);
			// We're returning a partial URL (just path + query + fragment), but we still want it to be rooted.
			if (builder.Length == 0 || builder[0] != '/')
			{
				builder.Insert(0, '/');
			}
		}
		else
		{
			protocol = string.IsNullOrEmpty(protocol) ? "http" : protocol;
			builder.Append(protocol);

			builder.Append(Uri.SchemeDelimiter);

			host = string.IsNullOrEmpty(host) ? ActionContext.HttpContext.Request.Host.Value : host;
			builder.Append(host);
			AppendPathAndFragment(builder, pathBase, virtualPath, fragment);
		}

		var path = builder.ToString();
		return path;
	}

	// 省略
}
```

[aspnetcore/UrlHelperBase.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Mvc/Mvc.Core/src/Routing/UrlHelperBase.cs#L116-L165)

### UrlHelperExtensions.ActionLinkメソッド

ActionLink拡張メソッドでは、引数の`protocol`と`host`が省略された場合にHTTPリクエストから取得した値を指定してActionメソッドを呼び出していることがわかります。

```csharp
public static string? ActionLink(
	this IUrlHelper helper,
	string? action = null,
	string? controller = null,
	object? values = null,
	string? protocol = null,
	string? host = null,
	string? fragment = null)
{
	if (helper == null)
	{
		throw new ArgumentNullException(nameof(helper));
	}

	var httpContext = helper.ActionContext.HttpContext;

	if (protocol == null)
	{
		protocol = httpContext.Request.Scheme;
	}

	if (host == null)
	{
		host = httpContext.Request.Host.ToUriComponent();
	}

	return Action(helper, action, controller, values, protocol, host, fragment);
}
```

[aspnetcore/UrlHelperExtensions.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Mvc/Mvc.Core/src/UrlHelperExtensions.cs#L559-L586)

以上、ActionLinkメソッドを使うと絶対URLになる理由というか仕組みでした。