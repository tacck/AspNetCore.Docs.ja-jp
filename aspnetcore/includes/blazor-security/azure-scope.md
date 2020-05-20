> [!NOTE]
> Azure portal がアプリのスコープ URI を提供し、アプリが API から*401 の承認*されていない応答を受信したときにハンドルされない例外をスローした場合、スキームとホストを含まないスコープ uri を使用してみてください。 たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> スキームとホストなしでスコープ URI を指定してみてください。
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
