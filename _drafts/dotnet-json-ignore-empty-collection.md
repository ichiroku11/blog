---
layout: post
title: ".NET - System.Text.Jsonで空のコレクションのプロパティをシリアライズしない"
date: 
tags: dotnet
---

System.Text.Jsonを使っていて、空のコレクションを返すプロパティをシリアライズしたくない（出力したくないというか無視したいというか）ときがあったので、対応を考えてみましたという内容です。

// todo:
/*
nullやデフォルト値の場合にシリアライズしない方法は、JsonIgnoreConditionを使うとあっさりできます。下記を参照ください。
// todo:
- https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/ignore-properties
- https://learn.microsoft.com/ja-jp/dotnet/api/system.text.json.serialization.jsonignorecondition

シリアライズ対象のクラスを編集できるのであれば、JsonIgnoreCondition.WhenWritingNull

JsonIgnoreConditionにWhenWritingEmptyみたいなのがあればよかったのですが残念ながらないので作ってみましょうと。
*/

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

### シリアライズ対象のクラスを編集できるとき

// todo: 

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

### シリアライズ対象のクラスを編集できないとき

// todo:

```csharp
using System.Text.Json.Serialization.Metadata;
using System.Text.Json.Serialization;
using System.Text.Json;

// 空のコレクションを返すプロパティのJSONコントラクトを変更する
// 参考
// https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/custom-contracts
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

