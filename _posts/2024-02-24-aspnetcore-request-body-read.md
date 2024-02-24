---
layout: post
title: "ASP.NET Core - リクエストボディの読み取りは1回だけ"
date: 2024-02-24
tags: aspnetcore
---

デフォルトの動きとしては、リクエストボディの読み取りは1回だけのようです。

コントローラーのアクション内で`HttpRequest.Body`からコンテンツを読み取れない場合があって調べた際に知りました。

下記ドキュメントより引用します。

[ASP.NET Core で HttpContext を使用する &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/aspnet/core/fundamentals/use-http-context?view=aspnetcore-8.0#enable-request-body-buffering)

###### 日本語
```
要求本文は、最初から最後まで 1 回のみ読み取ることができます。
要求本文の順方向専用読み取りにより、要求本文全体に対してバッファーリングを行うオーバーヘッドを回避し、メモリ使用量を減らします。
```

###### 英語
```
The request body can only be read once, from beginning to end.
Forward-only reading of the request body avoids the overhead of buffering the entire request body and reduces memory usage.
```

Minimal APIとMVCそれぞれでコードを書いて1回のみ読み取れることを確認したいと思います。

### Minimal APIで動きを確認する

Minimal APIでリクエストボディを2回読み取るEndpointを定義します。

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseRouting();
// HTTPボディを確認するEndpoint
app.MapPost("/body", async context => {
	// シークできるか
	var canSeek = context.Request.Body.CanSeek;

	// 1回目の読み取り
	var first = await new StreamReader(context.Request.Body, leaveOpen: true).ReadToEndAsync();

	// Positionを操作して巻き戻し
	var thrown = false;
	try {
		context.Request.Body.Position = 0;
	} catch (NotSupportedException) {
		thrown = true;
	}

	// 2回目の読み取り
	var second = await new StreamReader(context.Request.Body, leaveOpen: true).ReadToEndAsync();

	// レスポンス
	var json = JsonSerializer.Serialize(
		new {
			canSeek,
			first,
			second,
			thrown
		},
		new JsonSerializerOptions {
			DictionaryKeyPolicy = JsonNamingPolicy.CamelCase,
			PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
			WriteIndented = true,
		});
	await context.Response.WriteAsync(json);
});
app.Run();
```

このエンドポイントに対して適当なテキストをPOSTしてみると、

###### リクエスト
```
POST https://localhost/body
Content-Type: text/plain; charset=utf-8

content
```

次のようなJSONを取得できます。
1回目のリクエストボディの読み取りはできていますが、その後のシークに失敗し、2回目の読み取りはできていません。

###### レスポンスボディ
```json
{
	"canSeek": false,
	"first": "content",
	"second": "",
	"thrown": true
}
```

### MVC（APIコントローラー）で動きを確認する

次は下記のようなMVCのコントローラーを用意して確認しました。

```csharp
public class RequestBodyController : ControllerBase {
	// モデル
	public class Sample {
		public string Value { get; init; } = "";
	}

	// モデルにバインド済みなのでRequest.Bodyの中身は空文字
	[HttpPost]
	public async Task<IActionResult> JsonWithBind([FromBody] Sample model) {
		// bodyは空文字になる
		using var reader = new StreamReader(Request.Body);
		var body = await reader.ReadToEndAsync();

		return Ok(new { body, model.Value });
	}

	// モデルにバインドしていないのでRequest.Bodyから読み取れる
	[HttpPost]
	public async Task<IActionResult> JsonWithoutBind() {
		using var reader = new StreamReader(Request.Body);
		var body = await reader.ReadToEndAsync();

		return Ok(new { body });
	}
}
```

JsonWithBindアクションに対してJSONをPOSTしてみると、

###### リクエスト
```
POST https://localhost/requestbody/jsonwithbind
Content-Type: application/json; charset=utf-8;

{"value":"abc"}
```

レスポンスは次のJSONが返ってきます。
モデルにバインドしているため（すでにリクエストボディを読み取っているはずなので）アクション内でリクエストボディを読み取れません。空文字になります。

###### レスポンスボディ
```json
{
	"body": "",
	"value": "abc"
}
```

一方、JsonWithoutBindアクションでは、モデルにバインドしていないので（まだリクエストボディを読み取っていないはずなので）アクション内でリクエストボディを読み取れました。

###### リクエスト
```
POST https://localhost/requestbody/jsonwithoutbind
Content-Type: application/json; charset=utf-8;

{"value":"abc"}
```

###### レスポンスボディ
```json
{
	"body": "{\"value\":\"abc\"}"
}
```
