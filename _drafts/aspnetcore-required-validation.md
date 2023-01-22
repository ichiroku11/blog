---
layout: post
title: "ASP.NET Core - Required属性は値がnullかどうかを判定する"
date: 
tags: aspnetcore
---

Rquired属性はプロパティの値がnullのときバリデーションエラーになるようです。なので値型のプロパティに対してはバリデーションエラーにならない。

以下、ドキュメントから引用。

```
On the server, a required value is considered missing if the property is null. A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.

サーバーでは、プロパティが null の場合、必須の値が不足しているものと見なされます。 null 非許容型フィールドは常に有効であり、[Required] 属性のエラー メッセージが表示されることはありません。
```

[ASP.NET Core MVC でのモデルの検証 &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/aspnet/core/mvc/models/validation?view=aspnetcore-7.0#required-validation-on-the-server)
