Azure portal では、AAD テナントに[検証済みまたは未確認の発行元ドメイン](/azure/active-directory/develop/howto-configure-publisher-domain)があるかどうかに基づいて、次のアプリ ID URI 形式のいずれかが指定されています。

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

上記の 2 つのアプリ ID URI のうち、クライアント アプリでスコープ URI を指定するために後者が使用され、承認が成功しない場合は、スキームとホストなしでスコープ URI を指定してみてください。

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

例:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
