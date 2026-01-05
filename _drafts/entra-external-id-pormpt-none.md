---
layout: post
title: "Entra External ID - OIDCのprompt=noneに対応している"
date: 
tags: entra-external-id
---

Entra External IDでもOIDCの`prompt=none`を試したところ対応していました（Azure AD B2Cと同じ動きになりました）というお話です。

Azure AD B2Cで試したときと同じアクションメソッドを用意して呼び出してみました。

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

// todo
AADSTS50058
https://learn.microsoft.com/ja-jp/entra/identity-platform/reference-error-codes

https://login.microsoftonline.com/error?code=50058

