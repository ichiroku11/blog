---
layout: post
title: ".NET - DIのサービス登録メソッド（Add系/TryAdd系/TryAddEnumerable）の違いを確認する"
date: 
tags: dotnet aspnetcore
---

.NETでDIを利用するときサービスを登録しますが、名前が`Add`から始まるメソッド、`TryAdd`から始まるメソッド、`TryAddEnumerable`メソッドの違いがよく分かっていなかったので軽くまとめたいと思います。

今回は`AddTransient`、`AddScoped`、`AddSingleton`といったサービスの有効期間（Lifetime）についてはふれません。

// todo:

例としてScopedを扱って下記メソッドの違いがわかるサンプルコードを残しておきます。
- AddScopedメソッド
- TryAddScopedメソッド
- TryAddEnumerableメソッド

