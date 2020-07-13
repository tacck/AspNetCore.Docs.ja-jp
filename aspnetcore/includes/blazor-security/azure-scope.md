> [!NOTE]
> <span data-ttu-id="e6034-101">Azure portal でアプリに対してスコープ URI が提供されていて、API から *401 Unauthorized* 応答を受信したときにアプリでハンドルされない例外がスローされる場合は、スキームとホストが含まれないスコープ URI を使用してみてください。</span><span class="sxs-lookup"><span data-stu-id="e6034-101">If the Azure portal provides the scope URI for the app and the app throws an unhandled exception when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e6034-102">たとえば、Azure portal では、次のいずれかのスコープ URI 形式が提供されている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6034-102">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e6034-103">スキームとホストなしでスコープ URI を指定してみてください。</span><span class="sxs-lookup"><span data-stu-id="e6034-103">Try supplying the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{SERVER API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
>
> <span data-ttu-id="e6034-104">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e6034-104">For example:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
> ```
