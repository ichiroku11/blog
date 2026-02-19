---
layout: post
title: "Entra External ID - OIDCのprompt=createに対応している"
date: 
tags: entra-external-id
---

Entra External IDは、OIDCの`prompt=create`に対応しているようです。

ASP.NET Coreの下記コードで確認できました。
このアクションを呼び出すと、Entra External ID側ではサインアップの画面が表示されます。

```cs
public IActionResult SignUp() {
    var properties = new AuthenticationProperties {
    };

    // promptにcreateを指定する
    properties.SetParameter(OpenIdConnectParameterNames.Prompt, OpenIdConnectPrompt.Create);

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

ただ公開情報を確認してみましたが、`create`の記載はなさそうです。そのうち追記されるかも。

[OpenID Connect (OIDC) on the Microsoft identity platform - Microsoft identity platform &#124; Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity-platform/v2-protocols-oidc)

サインインのリクエストパラメーターの`prompt`の説明部分：

```
Indicates the type of user interaction that is required. The only valid values at this time are login, none, consent, and select_account. The prompt=login claim forces the user to enter their credentials on that request, which negates single sign-on. The prompt=none parameter is the opposite, and should be paired with a login_hint to indicate which user must be signed in. These parameters ensure that the user isn't presented with any interactive prompt at all. If the request can't be completed silently via single sign-on, the Microsoft identity platform returns an error. Causes include no signed-in user, the hinted user isn't signed in, or multiple users are signed in but no hint was provided. The prompt=consent claim triggers the OAuth consent dialog after the user signs in. The dialog asks the user to grant permissions to the app. Finally, select_account shows the user an account selector, negating single sign-out but allowing the user to pick which account they intend to sign in with, without requiring credential entry. You can't use both login_hint and select_account.
```

[Microsoft identity platform and OAuth 2.0 authorization code flow - Microsoft identity platform &#124; Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)

リクエストのパラメーターの`prompt`の説明部分：
```
Indicates the type of user interaction that is required. Valid values are login, none, consent, and select_account.
- prompt=login forces the user to enter their credentials on that request, negating single-sign on.
- prompt=none is the opposite. It ensures that the user isn't presented with any interactive prompt. If the request can't be completed silently by using single-sign on, the Microsoft identity platform returns an interaction_required error.
- prompt=consent triggers the OAuth consent dialog after the user signs in, asking the user to grant permissions to the app.
- prompt=select_account interrupts single sign-on providing account selection experience listing all the accounts either in session or any remembered account or an option to choose to use a different account altogether.
```

## 参考

- [Initiating User Registration via OpenID Connect 1.0](https://openid.net/specs/openid-connect-prompt-create-1_0.html)

