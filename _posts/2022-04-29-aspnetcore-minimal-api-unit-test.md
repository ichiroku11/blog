---
layout: post
title: "ASP.NET Core - Minimal APIをWebApplicationFactoryでテストする"
date: 2022-04-29
tags: aspnetcore
---

ASP.NET Core 6のMinimal APIを単体テストするとき、StartupクラスがないのでWebApplicationFactoryでテストするにはどうするんだろう？と思って調べたらDocsにありました。

[6.0 での新しい最小ホスティング モデルに移行されたコード サンプル &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/aspnet/core/migration/50-to-60-samples?view=aspnetcore-6.0#aspnet-core-6-9)

InternalsVisibleTo属性を使う方法とProgramクラスをpublicにする方法をがあるようです。

### InternalsVisibleTo属性を使う

```xml
<ItemGroup>
    <InternalsVisibleTo Include="MyTestProject" />
</ItemGroup>
```
または
```csharp
[assembly: InternalsVisibleTo("MyTestProject")]
```

.csprojにInternalsVisibleToを指定できることを初めて知りました。

[MSBuild プロジェクトの共通項目 - MSBuild &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/visualstudio/msbuild/common-msbuild-project-items?view=vs-2022#internalsvisibleto)

### Programクラスをpublicにする

```csharp
public partial class Program {
}
```

私はIClassFixtureを使うことが多いので、Programクラスをpublicにする方法を使うのかなと思いました。

```csharp
public class SampleControllerTest : IClassFixture<WebApplicationFactory<Program>> {
	private readonly WebApplicationFactory<Program> _factory;

	public SampleControllerTest(WebApplicationFactory<Program> factory) {
		_factory = factory;
	}
}
```
