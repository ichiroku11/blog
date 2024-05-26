---
layout: post
title: "Microsoft Graphのフィルターで使うany演算子の書き方のメモ"
date:
tags: ms-graph
---

Microsoft Graph APIでフィルタリングを行うときに使うany演算子。

例えば、Microsoft Entra IDの監査ログから特定のユーザーのログを取得する場合、次のように使います。

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=targetResources/any(p:p/id eq '{オブジェクトID}')
```

この場合、any演算子は、
`targetResources`というコレクションの中から`id`と指定したオブジェクトIDが一致する`targetResource`は存在すればtrueを返す、
といったものです。

any演算子の後の`p:p`って何？と思って調べたところ、ドキュメントに記載がありました。

```
$filter=collection/any(property:property/subProperty eq 'value-to-match')

property:property は、イテレーション中にコレクションの現在の要素を保持する範囲変数です。 この変数には、 p:p など、ほぼすべての名前を付けることができます。
```

[$filter クエリ パラメーターを使用してオブジェクトのコレクションをフィルター処理する - Microsoft Graph &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/graph/filter-query-parameter?tabs=http#any-operator)

ほぼすべての名前を付けられるということなので、適当に試したところ本当に何でもよいみたいでした。

```
$filter=targetResources/any(property:property/id eq '{オブジェクトID}')
$filter=targetResources/any(tr:tr/id eq '{オブジェクトID}')
$filter=targetResources/any(p:p/id eq '{オブジェクトID}')
```

名前付けのルールとか見つけられなかったのですが、どこかに記載あるんでしょうかね。
