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

any演算子の後の`p:p`って何？と思って調べたところ、ドキュメントに記載がありました。

https://learn.microsoft.com/ja-jp/graph/filter-query-parameter?tabs=http#any-operator

// todo: p:pは何でも良い
// todo: idはtargetResourceのプロパティ
