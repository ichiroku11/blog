---
layout: post
title: "Azure AD B2C - OIDCのprompt=noneに対応しているみたい"
date: 2026-01-03
tags: azure-ad-b2c
---

Azure AD B2CにおけるAuthentication Requestのパラメーターについてのメモです。

下記ドキュメントには`prompt`パラメーターの値として`login`のみ有効とありますが、どうも`none`も対応しているようです。

[OpenID Connect での Web サインイン - Azure AD B2C &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/active-directory-b2c/openid-connect#send-authentication-requests)

promptの説明より：
```
ユーザーに要求する対話の種類。
現時点で有効な値は login だけです。これはユーザーに、その要求に関する資格情報を強制的に入力させます。

（原文）
The type of user interaction that you require.
The only valid value at this time is login, which forces the user to enter their credentials on that request.
```

試しに`prompt=none`を指定する次のアクションを用意して、B2Cにログインしていない状態で呼び出してみます。
するとログイン画面は表示されず、エラーコード`AADB2C90077`を含んだエラーメッセージがコールバックURLに対してPOSTされます。

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
    };

    // promptにnoneを指定する
    properties.SetParameter(OpenIdConnectParameterNames.Prompt, OpenIdConnectPrompt.None);

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

[エラー コード リファレンス - Azure AD B2C &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/azure/active-directory-b2c/error-codes)

エラーメッセージ（`AADB2C90077`）：
```
User does not have an existing session and request prompt parameter has a value of 'None'.
```

ログイン画面を表示させず、B2Cにログインしているかどうかの判定に使えそうですね。


### 参考

- [Final: OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)
