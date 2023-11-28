---
layout: post
title: ".NET - System.Text.Jsonで空のコレクションのプロパティをシリアライズしない"
date: 
tags: dotnet
---

System.Text.Jsonを使っていて、空のコレクションを返すプロパティをシリアライズしたくない（出力したくないというか無視したいというか）ときがあったので、対応を考えてみましたという内容です。

シリアライズするクラスを編集できるのであれば、`JsonIgnoreCondition.WhenWritingNull`を使って実現します。編集できないクラスであれば、JSONコントラクトというメタデータ（`JsonTypeInfo`）を使って実現します。

nullやデフォルト値の場合にシリアライズしない方法は、JsonIgnoreConditionを使うとあっさりできます。下記を参照ください。
- [System.Text.Json でプロパティを無視する方法 - .NET &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/ignore-properties)
- [JsonIgnoreCondition 列挙型 (System.Text.Json.Serialization) &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/api/system.text.json.serialization.jsonignorecondition)

### 既存の動き

まず既存の動きを確認しましょう。
空のコレクションは当然ですがJSON上で空配列（`[]`）としてシリアライズされます。

```csharp
using System.Text.Json;

var options = new JsonSerializerOptions {
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
};

var json = JsonSerializer.Serialize(new Sample(), options);
Console.WriteLine(json);
/*
{"values":[]}
*/

public class Sample {
    // 空のコレクションを返すプロパティ
    public IEnumerable<int> Values { get; init; } = Enumerable.Empty<int>();
}
```

### シリアライズするクラスを編集できるとき

シリアライズ対象を編集できるのであれば、空のコレクションのプロパティに対して、
1. `JsonIgnoreCondition.WhenWritingNull`の`JsonIgnoreAttribute`を指定する
- getアクセサーをコレクションが空であれば`null`を返すようにする
という実装で少しトリッキーな気もしますがいけるかなと。

```csharp
using System.Text.Json.Serialization;
using System.Text.Json;

var options = new JsonSerializerOptions {
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
};

var json = JsonSerializer.Serialize(new Sample(), options);
Console.WriteLine(json);
/*
{}
*/

public class Sample {
    private IEnumerable<int> _values = new List<int>();

    // 取得できる値がnullのときはシリアライズしない
    [JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingNull)]
    public IEnumerable<int>? Values {
        get {
            // 空であればnullを返す
            return _values.Any() ? _values : null;
        }
        init {
            _values = value ?? throw new InvalidOperationException();
        }
    }
}
```

### シリアライズするクラスを編集できないとき

コレクションのプロパティが空であれば`null`を返すという上記実装ができない場合、
1. `JsonSerializerOptions.DefaultIgnoreCondition`に`JsonIgnoreCondition.WhenWritingNull`を指定する
- JSONコントラクトというメタデータをカスタマイズして、コレクションが空なら`null`を返す
という実装で実現できます。

JSONコントラクトのカスタマイズについては下記ドキュメントが参考になります。
- [カスタム シリアル化コントラクトと逆シリアル化コントラクト - .NET &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/custom-contracts)

```csharp
using System.Text.Json.Serialization.Metadata;
using System.Text.Json.Serialization;
using System.Text.Json;

// 空のコレクションを返すプロパティのJSONコントラクトをカスタマイズする
var modifier = (JsonTypeInfo typeInfo) => {
    if (typeInfo.Kind is not JsonTypeInfoKind.Object) {
        return;
    }
    var properties = typeInfo.Properties.Where(property => property.PropertyType.IsAssignableTo(typeof(IEnumerable)));
    foreach (var property in properties) {
        var getter = property.Get;
        // プリパティの値が空のコレクションであればnullを返す
        property.Get = (obj) => {
            if (getter?.Invoke(obj) is not IEnumerable values) {
                return null;
            }

            return values.Cast<object>().Any()
                ? values
                : null;
        };
    }
};

var options = new JsonSerializerOptions {
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
    // nullをシリアライズしない
    DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingNull,
    TypeInfoResolver = new DefaultJsonTypeInfoResolver {
        // JSONコントラクトをカスタマイズする
        Modifiers = { modifier }
    }
};

var json = JsonSerializer.Serialize(new Sample(), options);
Console.WriteLine(json);
/*
{}
*/

public class Sample {
    public IEnumerable<int> Values { get; init; } = Enumerable.Empty<int>();
}
```
