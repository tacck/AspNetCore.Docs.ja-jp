> [!NOTE]
> <span data-ttu-id="b5945-101">Azure portal がアプリのスコープ URI を提供し、アプリが API から*401 の承認*されていない応答を受信したときにハンドルされない例外をスローした場合、スキームとホストを含まないスコープ uri を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="b5945-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="b5945-102">たとえば、Azure portal は、次のいずれかのスコープ URI 形式を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5945-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="b5945-103">スキームとホストなしでスコープ URI を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="b5945-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
