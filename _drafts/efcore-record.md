---
layout: post
title: "EF Core - record型とエンティティ"
date: 
tags: efcore csharp
---

record型をEF Coreのエンティティとして使うのは良くないよねというメモ。

##### 日本語
```
すべてのデータ モデルが値の等価性に適しているわけではありません。
たとえば、Entity Framework Core では、概念的に 1 つのエンティティであるものに対して、エンティティ型の 1 つのインスタンスだけが確実に使用されるようにするために、参照の等価性に依存します。
このため、レコード型は Entity Framework Core でエンティティ型として使用するのに適していません。
```

[https://docs.microsoft.com/ja-jp/dotnet/csharp/fundamentals/types/records#value-equality](https://docs.microsoft.com/ja-jp/dotnet/csharp/fundamentals/types/records#value-equality)

##### 英語
```
Not all data models work well with value equality.
For example, Entity Framework Core depends on reference equality to ensure that it uses only one instance of an entity type for what is conceptually one entity.
For this reason, record types aren't appropriate for use as entity types in Entity Framework Core.
```

[https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records#value-equality](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records#value-equality)


もう1つ気になる記述も。

##### 日本語
```
不変性は、すべてのデータ シナリオに適しているわけではありません。
たとえば、Entity Framework Core では、不変のエンティティ型を使用した更新がサポートされていません。
```
[https://docs.microsoft.com/ja-jp/dotnet/csharp/fundamentals/types/records#immutability](https://docs.microsoft.com/ja-jp/dotnet/csharp/fundamentals/types/records#immutability)


##### 英語
```
Immutability isn't appropriate for all data scenarios.
Entity Framework Core, for example, doesn't support updating with immutable entity types.
```

[https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records#immutability](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records#immutability)
