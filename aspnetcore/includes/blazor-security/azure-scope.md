> [!NOTE]
> Azure portal でアプリに対してスコープ URI が提供されていて、API から *401 Unauthorized* 応答を受信したときにアプリでハンドルされない例外がスローされる場合は、スキームとホストが含まれないスコープ URI を使用してみてください。 たとえば、Azure portal では、次のいずれかのスコープ URI 形式が提供されている場合があります。
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> スキームとホストなしでスコープ URI を指定してみてください。
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> 次に例を示します。
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
