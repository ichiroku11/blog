---
layout: post
title: ".NET - オプションのConfigureとPostConfigureを確認する"
date: 
tags: dotnet aspnetcore
---

.NETのオプションパターンを使用するとき、`Configure`メソッドや`PostConfigure`メソッドを使ってオプションの値を構成するアクションを指定すると思います。

この投稿では次の2点を確認したいと思います。

- `Configure`メソッドと`PostConfigure`メソッドで指定したアクションは、オプションがDIで解決されるときに呼び出されること
- `PostConfigure`で指定したアクションは、`Configure`で指定したアクションが呼び出された後に呼び出されること

### ConfigureメソッドとPostConfigureメソッドで指定したアクションの呼び出しを確認する

この2点を確認するサンプルコードです。

```csharp
// DIで解決するオプション
private class SampleOptions {
	public int Value { get; set; }
}

var services = new ServiceCollection();
services
	.AddOptions<SampleOptions>()
	.Configure(options => {
		// オプションを構成する
		Console.WriteLine("Configure");
		options.Value = 1;
	})
	.PostConfigure(options => {
		// オプションを構成する
		Console.WriteLine("PostConfigure");
		options.Value = 2;
	});
var serviceProvider = services.BuildServiceProvider();

// IOptionsを解決する
Console.WriteLine("GetRequiredService");
var optionsProvider = serviceProvider.GetRequiredService<IOptions<SampleOptions>>();

// IOptionsからSampleOptionsを取り出す
// このときConfigureやPostConfigureで指定したアクションが呼び出される
Console.WriteLine("IOptions.Value");
var options = optionsProvider.Value;
Console.WriteLine(options.Value);

/*
// 実行結果
GetRequiredService
IOptions.Value
Configure
PostConfigure
2
*/
```

実行結果から、`IOptions.Value`プロパティで`SampleOptions`のインスタンスを取得（解決）するときに、
`Configure`メソッドや`PostConfigure`メソッドで指定したアクションが呼び出されることを確認できます。

さらに、`Configure`メソッドで指定したアクション、`PostConfigure`メソッドで指定したアクションの順番に呼び出され、`SampleOptions.Value`の値が上書きされていることも確認できます。

### .NETの実装を確認する

これらの動きを実装している部分を.NETのコードから確認したいと思います。

まず`AddOptions`メソッドの実装を確認しましょう。`IOptions<>`に対して`UnnamedOptionsManager<>`、`IOptionsFactory<>`に対して`OptionsFactory<>`が登録されています。この2つの登録以外はこの投稿では扱いません。無視してください。

```csharp
public static class OptionsServiceCollectionExtensions
{
    public static IServiceCollection AddOptions(this IServiceCollection services)
    {
        ThrowHelper.ThrowIfNull(services);

        services.TryAdd(ServiceDescriptor.Singleton(typeof(IOptions<>), typeof(UnnamedOptionsManager<>)));
        services.TryAdd(ServiceDescriptor.Scoped(typeof(IOptionsSnapshot<>), typeof(OptionsManager<>)));
        services.TryAdd(ServiceDescriptor.Singleton(typeof(IOptionsMonitor<>), typeof(OptionsMonitor<>)));
        services.TryAdd(ServiceDescriptor.Transient(typeof(IOptionsFactory<>), typeof(OptionsFactory<>)));
        services.TryAdd(ServiceDescriptor.Singleton(typeof(IOptionsMonitorCache<>), typeof(OptionsCache<>)));
        return services;
    }
}
```
[runtime/OptionsServiceCollectionExtensions.cs at main · dotnet/runtime · GitHub](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.Options/src/OptionsServiceCollectionExtensions.cs)

次に`UnnamedOptionsManager.Value`プロパティを確認すると、`IOptionsFactory.Create`メソッドを呼び出してオプションを生成していることがわかります。

```csharp
internal sealed class UnnamedOptionsManager<TOptions> : IOptions<TOptions> where TOptions : class
{
    private readonly IOptionsFactory<TOptions> _factory;

    // 省略

    public TOptions Value
    {
        get
        {
            if (_value is TOptions value)
            {
                return value;
            }

            lock (_syncObj ?? Interlocked.CompareExchange(ref _syncObj, new object(), null) ?? _syncObj)
            {
                return _value ??= _factory.Create(Options.DefaultName);
            }
        }
    }
}
```

[runtime/UnnamedOptionsManager.cs at main · dotnet/runtime · GitHub](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.Options/src/UnnamedOptionsManager.c)

最後に`OptionsFactory.Create`メソッドを見てみると、`IConfigureOptions<TOptions>.Configure`メソッド、`IPostConfigureOptions<TOptions>.PostConfigure`メソッドを順番に呼び出していることがわかります。

```csharp
public class OptionsFactory<TOptions> : IOptionsFactory<TOptions> where TOptions : class
{
    // 省略

    public TOptions Create(string name)
    {
        TOptions options = CreateInstance(name);
        foreach (IConfigureOptions<TOptions> setup in _setups)
        {
            if (setup is IConfigureNamedOptions<TOptions> namedSetup)
            {
                namedSetup.Configure(name, options);
            }
            else if (name == Options.DefaultName)
            {
                setup.Configure(options);
            }
        }
        foreach (IPostConfigureOptions<TOptions> post in _postConfigures)
        {
            post.PostConfigure(name, options);
        }

        // 省略

        return options;
    }
}
```
[runtime/OptionsFactory.cs at main · dotnet/runtime · GitHub](https://github.com/dotnet/runtime/blob/main/src/libraries/Microsoft.Extensions.Options/src/OptionsFactory.cs)

以上、`Configure`メソッドと`PostConfigure`メソッドについて、サンプルと.NETの実装から確認してみました。