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

各サンプルで利用するインターフェイスと実装クラスを用意します。

```csharp
public interface IService {
}

public class ServiceA : IService {
}

public class ServiceB : IService {
}
```

### AddScopedメソッド

AddScopedメソッド（AddTransient、AddSingletonを含めたAdd系メソッド）は、同じサービスの実装を複数登録できます。

```csharp
var services = new ServiceCollection();
services.AddScoped<IService, ServiceA>();

// 同じサービスを複数登録できる
services
	// 同じサービスの同じ実装
	.AddScoped<IService, ServiceA>()
	// 同じサービスの別の実装
	.AddScoped<IService, ServiceB>();

foreach (var service in services) {
	Console.WriteLine(service.ImplementationType?.Name);
}
// ServiceA
// ServiceA
// ServiceB
```

### TryAddScopedメソッド

TryAddScopedメソッド（TryAddTransient、TryAddSingletonを含めたTryAdd系メソッド）は、同じサービスを複数登録できません。

```csharp
var services = new ServiceCollection();
services.AddScoped<IService, ServiceA>();

// 同じサービスを複数登録できない
services.TryAddScoped<IService, ServiceA>();
services.TryAddScoped<IService, ServiceB>();

foreach (var service in services) {
	Console.WriteLine(service.ImplementationType?.Name);
}
// ServiceA
```

### TryAddEnumerableメソッド

TryAddEnumerableメソッドは、同じサービスの同じ実装は登録できませんが、別の実装であれば登録できます。

```csharp
var services = new ServiceCollection();
services.AddScoped<IService, ServiceA>();

// 同じ実装は登録できない
services.TryAddEnumerable(ServiceDescriptor.Scoped<IService, ServiceA>());
// 別の実装であれば登録できる
services.TryAddEnumerable(ServiceDescriptor.Scoped<IService, ServiceB>());

foreach (var service in services) {
	Console.WriteLine(service.ImplementationType?.Name);
}
// ServiceA
// ServiceB
```
