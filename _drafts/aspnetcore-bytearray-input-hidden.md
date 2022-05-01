---
layout: post
title: "ASP.NET Core - タグヘルパーでtype="hidden"のinput要素に出力するとき、バイト配列はBase64エンコードされる"
date: 
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

Docsには記載はなさそう（見落としているかな）ですが。

https://docs.microsoft.com/ja-jp/aspnet/core/mvc/views/working-with-forms?view=aspnetcore-6.0#the-input-tag-helper

// todo: GitHubのソース