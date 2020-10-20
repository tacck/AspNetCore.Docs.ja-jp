<span data-ttu-id="fdd80-101">AAD に登録されているサーバー API を使用するときに、アプリの AAD 登録が[未確認の発行元ドメイン](/azure/active-directory/develop/howto-configure-publisher-domain)に依存するテナント内にある場合、サーバー API アプリのアプリ ID の URI は、`api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` ではなく `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` の形式になります。</span><span class="sxs-lookup"><span data-stu-id="fdd80-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="fdd80-102">このケースの場合、 *`Client`* アプリの `Program.Main` (`Program.cs`) の既定のアクセス トークン スコープは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="fdd80-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="fdd80-103">一致する対象ユーザーに対してサーバー API アプリを構成するには、 *`Server`* API アプリ設定ファイル (`appsettings.json`) の `Audience` を、Azure portal によって提供されるアプリの対象ユーザーと一致するように設定します。</span><span class="sxs-lookup"><span data-stu-id="fdd80-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="fdd80-104">上記の構成では、`Audience` 値の末尾に既定のスコープ `/{DEFAULT SCOPE}` が含まれて**いません**。</span><span class="sxs-lookup"><span data-stu-id="fdd80-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="fdd80-105">例:</span><span class="sxs-lookup"><span data-stu-id="fdd80-105">Example:</span></span>

<span data-ttu-id="fdd80-106">*`Client`* アプリの `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="fdd80-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="fdd80-107">一致する対象ユーザー (`Audience`) を使用して *`Server`* API アプリ設定ファイル (`appsettings.json`) を構成します。</span><span class="sxs-lookup"><span data-stu-id="fdd80-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="fdd80-108">上記の例では、`Audience` 値の末尾に既定のスコープ `/API.Access` が含まれて**いません**。</span><span class="sxs-lookup"><span data-stu-id="fdd80-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
