---
layout: post
title: "ASP.NET Core - タグヘルパーでtype=\"hidden\"のinput要素にバイト配列のプロパティを指定するとBase64エンコードされる"
date: 2022-05-18
tags: aspnetcore
---

ASP.NET Coreのタグヘルパーのお話。

次のようにtype="hidden"のinput要素を出力するとき、asp-forでバイト配列のプロパティを指定するとBase64にエンコードされた値がvalue属性に出力されます。

```html
@model SampleModel

<input id="id2" asp-for="Bytes" type="hidden" />
```

モデルはこんな感じ。

```cs
public class SampleModel {
	public byte[] Bytes { get; init; }
}
```

Docsには記載がなさそうな感じです。

[ASP.NET Core のフォームのタグ ヘルパー | Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/mvc/views/working-with-forms?view=aspnetcore-6.0#the-input-tag-helper)

ソースを調べてみると、DefaultHtmlGenerator.GenerateHiddenメソッドでバイト配列に対して特別処理しているがそれかなと思います。（デバッグ実行で確かめたわけではないです。）

```csharp
// Special-case opaque values and arbitrary binary data.
if (value is byte[] byteArrayValue)
{
	value = Convert.ToBase64String(byteArrayValue);
}
```

[https://github.com/dotnet/aspnetcore/blob/ac39742bf152a0d2980059289822e1d3526a880a/src/Mvc/Mvc.ViewFeatures/src/DefaultHtmlGenerator.cs#L389-L393](https://github.com/dotnet/aspnetcore/blob/ac39742bf152a0d2980059289822e1d3526a880a/src/Mvc/Mvc.ViewFeatures/src/DefaultHtmlGenerator.cs#L389-L393)
