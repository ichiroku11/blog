---
layout: post
title: ".NET - オプションのConfigureとPostConfigureを確認する"
date: 
tags: dotnet aspnetcore
---

.NETのオプションパターンを使用するとき、次のコードのように`Configure`メソッドや`PostConfigure`メソッドを使って、オプションの値を構成するアクションを指定すると思います。指定したアクションが呼び出されるタイミングを確認したいと思います。

```csharp
private class SampleOptions {
	public int Value { get; set; }
}

var services = new ServiceCollection();
services
    .AddOptions<SampleOptions>()
    .Configure(options => {
        // オプションを構成する
    })
    .PostConfigure(options => {
        // オプションを構成する
    });
```

`Configure`メソッドや`PostConfigure`メソッドで指定したアクションは、オプションがDIで解決されるとき（オプションが生成されるとき）に呼び出されます。
`PostConfigure`で指定したアクションは、必ず`Configure`で指定したアクションがすべて呼び出された後に呼び出されます。

```csharp
```


