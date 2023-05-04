---
layout: post
title: ".NET - DIのサービス登録メソッド（Add系/TryAdd系/TryAddEnumerable）の違いを確認する"
date: 2023-05-05
tags: dotnet aspnetcore
---

.NETのDIで利用するサービスを登録するメソッドがあります。
名前が`Add`から始まるメソッド、`TryAdd`から始まるメソッド、`TryAddEnumerable`メソッドの違いがよく分かっていなかったので軽くまとめたいと思います。
（この投稿では`AddTransient`、`AddScoped`、`AddSingleton`といったサービスの有効期間についてはふれません。）

それぞれのサービス登録メソッドの違いは、同じサービス（の実装）を複数登録したときに登録できるかどうかです。
TryAdd系メソッドは同じサービスに対して複数の実装を登録できないのに対して、TryAddEnumerableメソッドは同じサービスに対して異なる実装であれば複数登録できます。

| メソッド | 同じサービス（の実装）を複数登録したときの動き |
|--|--|
| Add系メソッド | 登録できる |
| TryAdd系メソッド | 登録できない |
| TryAddEnumerableメソッド | 異なる実装であれば登録できる |

例としてScopedを扱って下記メソッドの違いを確認できるサンプルコードを残しておきます。
- [`AddScoped`メソッド](#addscopedメソッド)
- [`TryAddScoped`メソッド](#tryaddscopedメソッド)
- [`TryAddEnumerable`メソッド](#tryaddenumerableメソッド)

まず、サンプルで利用するインターフェイスと実装クラスです。

```csharp
public interface IService {
}

public class ServiceA : IService {
}

public class ServiceB : IService {
}
```

### AddScopedメソッド

`AddScoped`メソッド（`AddTransient`、`AddSingleton`を含めたAdd系メソッド）は、同じサービスの実装を複数登録できます。

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

`TryAddScoped`メソッド（`TryAddTransient`、`TryAddSingleton`を含めたTryAdd系メソッド）は、同じサービスを複数登録できません。

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

`TryAddEnumerable`メソッドは、同じサービスの同じ実装は登録できませんが、別の実装であれば登録できます。

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

### 参考

- [依存関係の挿入 - .NET &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/dotnet/core/extensions/dependency-injection#service-registration-methods)
- [runtime/ServiceCollectionServiceExtensions.cs at main · dotnet/runtime · GitHub](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.DependencyInjection.Abstractions/src/ServiceCollectionServiceExtensions.cs)
	- Add系メソッドの実装
- [runtime/ServiceCollectionDescriptorExtensions.cs at main · dotnet/runtime · GitHub](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.DependencyInjection.Abstractions/src/Extensions/ServiceCollectionDescriptorExtensions.cs)
	- TryAdd系メソッド、TryAddEnumerableメソッドの実装
