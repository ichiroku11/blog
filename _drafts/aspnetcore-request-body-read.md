---
layout: post
title: "ASP.NET Core - リクエストボディの読み取りは1回だけ"
date: 
tags: aspnetcore
---

デフォルトの動きとしては、リクエストボディの読み取りは1回だけのようです。

コントローラーのアクション内で`HttpRequest.Body`からコンテンツを読み取れない場合があって調べた際に知りました。

下記ドキュメントより引用します。
(ASP.NET Core で HttpContext を使用する &#124; Microsoft Learn)[https://learn.microsoft.com/ja-jp/aspnet/core/fundamentals/use-http-context?view=aspnetcore-7.0#enable-request-body-buffering]

##### 日本語
```
要求本文は、最初から最後まで 1 回のみ読み取ることができます。
要求本文の順方向専用読み取りにより、要求本文全体に対してバッファーリングを行うオーバーヘッドを回避し、メモリ使用量を減らします。
```

##### 英語
```
The request body can only be read once, from beginning to end.
Forward-only reading of the request body avoids the overhead of buffering the entire request body and reduces memory usage.
```

実際にコードを書いて1回のみ読み取れることを確認したいと思います。

### Minimal APIで動きを確認する

// todo:

### MVC（APIコントローラー）で動きを確認する

下記のようなコントローラーを用意しました。

JsonWithBindアクションでは、モデルにバインドしている（すでにリクエストボディを読み取っている）のでアクション内でリクエストボディを読み取れません。
一方、JsonWithoutBindアクションでは、モデルにバインドしていないのでアクション内でリクエストボディを読み取れました。

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

### EnableBuffering

// todo:
リクエストボディを読み取る前に
