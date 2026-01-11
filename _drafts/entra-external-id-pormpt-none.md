---
layout: post
title: "Entra External ID - OIDCのprompt=noneに対応している"
date: 
tags: entra-external-id
---

Entra External IDでもOIDCの`prompt=none`を試したところ対応していました（Azure AD B2Cと同じ動きになりました）というお話です。

Azure AD B2Cで試したときと同じアクションメソッドを用意して、Entra External IDにログインしていない状態で呼び出してみました。

[Azure AD B2C - OIDCのprompt=noneに対応しているみたい &#124; いちろぐ](/2026/01/03/azure-adb2c-prompt-none.html)

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
    };

    // promptにnoneを指定する
    properties.SetParameter(OpenIdConnectParameterNames.Prompt, OpenIdConnectPrompt.None);

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

するとログイン画面は表示されず、エラーコード`AADSTS50058`を含んだエラーメッセージがコールバックURLに対してPOSTされました。

エラーコード`AADSTS50058`は下記より確認できます。

[Microsoft Entra 認証と承認のエラー コード - Microsoft identity platform &#124; Microsoft Learn](https://learn.microsoft.com/ja-jp/entra/identity-platform/reference-error-codes)

説明の抜粋：
|エラー|説明|
|--|--|
|AADSTS50058|UserInformationNotProvided - シングル サインオンに関するセッション情報が不十分です。 これは、ユーザーがサインインしていないことを意味します。 これは、ユーザーが認証されておらず、まだサインインしていないときに想定される一般的なエラーです。 <br>ユーザーがその前にサインインした SSO のコンテキストでこのエラーが発生する場合は、SSO セッションが見つからなかったか無効であったことを意味します。<br>prompt=none が指定されている場合、このエラーがアプリケーションに返される可能性があります。<br>以下原文：<br>UserInformationNotProvided - Session information isn't sufficient for single-sign-on. This means that a user isn't signed in. This is a common error that's expected when a user is unauthenticated and hasn't yet signed in.<br>If this error is encountered in an SSO context where the user has previously signed in, this means that the SSO session was either not found or invalid.<br>This error might be returned to the application if prompt=none is specified.|

エラーについての最新情報は、[https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058)で確認できるとのことです。現時点ではだいたい似たようなことが書かれているのかなと。

```
Error Code：50058
Message：Session information is not sufficient for single-sign-on.
Remediation：This means that a user is not signed in. This is a common error that's expected when a user is unauthenticated and has not yet signed in. If this error is encountered in an SSO context where the user has previously signed in, this means that the SSO session was either not found or invalid. This error may be returned to the application if prompt=none is specified.
```

