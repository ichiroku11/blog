---
layout: post
title: "ASP.NET Core - AuthenticationProperties.RedirectUriはstateパラメーターにシリアライズされる"
date: 
tags: aspnetcore openid-connect
---

ASP.NET CoreでOpenID Connectによるログインを実装して、IdPでログインした後、任意のURLにリダイレクトするときのお話です。

Authentication Requestの`redirect_uri`パラメーターで指定するURLはIdPに事前に登録する必要があるため、
IdPでログインした後の任意のURLにリダイレクトしたい場合、`AuthenticationProperties.RedirectUri`プロパティを利用すると思います。

次のようなコードでリダイレクト先を指定するときですね。

```cs
public IActionResult SignIn() {
    var properties = new AuthenticationProperties {
        // ログイン後のリダイレクト先を指定する
        // 内部的にItemsプロパティに追加される
        RedirectUri = Url.Action("Index", "Home"),
    };

    return Challenge(properties, OpenIdConnectDefaults.AuthenticationScheme);
}
```

ログイン後の動きとしては、2回リダイレクトが発生します。
1. Authentication Requestの`redirect_uri`で指定したURL（IdPに登録したコールバックURL）にリダイレクト
2. `AuthenticationProperties.RedirectUri`プロパティで指定したURLにリダイレクト

また、`AuthenticationProperties.RedirectUri`で指定したURLは、`redirect_uri`パラメーターには含まれないのですが、
どうやって覚えているかというと`state`パラメーターにシリアライズされIdPに渡されて、リダイレクト後のコールバックで返されます。

ということで、`state`パラメーターにシリアライズされている部分のコードを確認してみたいと思います。

まず、IdPへのリダイレクトを処理している`OpenIdConnectHandler.HandleChallengeAsyncInternal`メソッドを確認してみましょう。
`AuthenticationProperties`の値を何かしら保護（加工）して`state`パラメーターに設定してます。

```cs
message.State = Options.StateDataFormat.Protect(properties);
```

- [aspnetcore/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectHandler.cs#L480)

次に、`OpenIdConnectOptions.StateDataFormat`プロパティを確認すると、`ISecureDataFormat<AuthenticationProperties>`という型になっていることがわかります。

```cs
public ISecureDataFormat<AuthenticationProperties> StateDataFormat { get; set; } = default!;
```

- [aspnetcore/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectOptions.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/OpenIdConnect/src/OpenIdConnectOptions.cs#L263)


このインターフェイスは`PropertiesDataFormat`、さらに`SecureDataFormat<TData>`で実装されています。
`SecureDataFormat.Protect`メソッドは、TData型のデータをシリアライズして暗号化するメソッドです。

```cs
public string Protect(TData data, string? purpose)
{
    var userData = _serializer.Serialize(data);

    var protector = _protector;
    if (!string.IsNullOrEmpty(purpose))
    {
        protector = protector.CreateProtector(purpose);
    }

    var protectedData = protector.Protect(userData);
    return Base64UrlTextEncoder.Encode(protectedData);
}
```

- [aspnetcore/src/Security/Authentication/Core/src/PropertiesDataFormat.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/Core/src/PropertiesDataFormat.cs)
- [aspnetcore/src/Security/Authentication/Core/src/SecureDataFormat.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/Core/src/SecureDataFormat.cs#L35-L47)

`PropertiesSerializer.Serialize`メソッドを呼び出していて、コードをたどっていくと、`AuthenticationProperties.Items`をシリアライズしていることがわかります。

```cs
public virtual byte[] Serialize(AuthenticationProperties model)
{
    using (var memory = new MemoryStream())
    {
        using (var writer = new BinaryWriter(memory))
        {
            Write(writer, model);
            writer.Flush();
            return memory.ToArray();
        }
    }
}

public virtual void Write(BinaryWriter writer, AuthenticationProperties properties)
{
    ArgumentNullException.ThrowIfNull(writer);
    ArgumentNullException.ThrowIfNull(properties);

    writer.Write(FormatVersion);
    writer.Write(properties.Items.Count);

    foreach (var item in properties.Items)
    {
        writer.Write(item.Key ?? string.Empty);
        writer.Write(item.Value ?? string.Empty);
    }
}
```

- [aspnetcore/src/Security/Authentication/Core/src/PropertiesSerializer.cs at main · dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/blob/main/src/Security/Authentication/Core/src/PropertiesSerializer.cs#L33-L58)



/*
[Final: OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)
*/