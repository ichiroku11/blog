---
layout: post
title: ".NET - DIのサービス登録メソッド（Add系/TryAdd系/TryAdd/TryAddEnumerable）の違いを確認する"
date: 
tags: dotnet aspnetcore
---

.NETでDIを利用するときサービスを登録しますが、名前が`Add`から始まるメソッド、`TryAdd`から始まるメソッド、`TryAddEnumerable`メソッドの違いがよく分かっていなかったので軽くまとめたいと思います。

今回は`AddTransient`、`AddScoped`、`AddSingleton`といったサービスの有効期間（Lifetime）についてはふれません。

サービス登録メソッドの違いについてざっくり言うと、同じサービス（インタフェース、抽象クラス）を複数登録できるか、同じサービスの実装（具象クラス）を複数登録できるかです。

| メソッド | 同じサービスを複数登録できる | 同じサービスの実装を複数登録できる |
|--|--|--|
| `Add`系メソッド | ○ | ○ |
| `TryAdd`系メソッド | × | ○ |
| `TryAddEnumerable`メソッド | × | × |

以下、例としてScopedを扱って、下記メソッドの違いがわかるサンプルコードです。
- `AddScoped`
- `TryAddScoped`
- `TryAddEnumerable`

