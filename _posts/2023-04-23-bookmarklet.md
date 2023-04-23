---
layout: post
title: "使っているブックマークレット"
date: 2023-04-23
tags: bookmarklet javascript
---

使っているブックマークレットのメモ。

### 現在開いているページのタイトルを表示する

```js
javascript:(()=>{window.prompt("",document.title);})();
```

### クエリ文字列を削除する

```js
javascript:(()=>{var url=new URL(location.href);url.search="";location.href=url;})();
```

### Microsoft Learnのドキュメントページの英語と日本語を切り替える

正確に言うと、英語のページであれば別タブで日本語のページを開く、またはその逆。

```js
javascript:(()=>{var href=location.href;if(/en-us/.test(href))href=href.replace(/en-us/,"ja-jp");else if(/ja-jp/.test(href))href=href.replace(/ja-jp/,"en-us");else return;window.open(href)})();
```

