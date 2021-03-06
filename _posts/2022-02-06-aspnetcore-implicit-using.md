---
layout: post
title: "ASP.NET CoreのImplicit using"
date: 2022-02-06
tags: aspnetcore
---

ASP.NET Core 6.0で自動的に追加される暗黙的なusingディレクティブ：

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Routing;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading;
using System.Threading.Tasks;
```

コンソールアプリで自動的に追加される`global using`については下記ドキュメントにありましたが、Webアプリケーションの場合は見つからなかったので、ASP.NET Coreの空プロジェクトを作成して、obj/Debug/net6.0フォルダにある`{プロジェクト名}.GlobalUsings.g.cs`を確認してみました。

[.NET 6 での C# テンプレートの変更 - .NET &#124; Microsoft Docs](https://docs.microsoft.com/ja-jp/dotnet/core/tutorials/top-level-templates#implicit-using-directives)
