---
layout: post
title: ".NET - System.Text.Jsonで空のコレクションのプロパティをシリアライズしない"
date: 
tags: dotnet
---

System.Text.Jsonを使っていて、空のコレクションを返すプロパティをシリアライズしたくない（出力したくない・無視したい）ときがあったので、対応を考えてみましたという内容です。

nullやデフォルト値の場合にシリアライズしない方法は、JsonIgnoreConditionを使うとあっさりできます。下記を参照ください。

https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/ignore-properties

https://learn.microsoft.com/ja-jp/dotnet/api/system.text.json.serialization.jsonignorecondition

WhenWritingEmptyみたいなのがあればよかったのですが残念ながらないので。

### 既存の動き

まず既存の動きを確認しましょう。
空のコレクションは、JSON上で空配列（`[]`）として出力されます。

```csharp
// todo:
```

### 空コレクションを

ここから本題。この空配列を出力しないようにしたいと思います。

```csharp
// todo:
```

