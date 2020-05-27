---
<span data-ttu-id="67c02-101">タイトル: ' ASP.NET Core Blazor サーバーの追加のセキュリティシナリオ ' author: 説明: ' Blazor 追加のセキュリティシナリオ用にサーバーを構成する方法を説明します。 '</span><span class="sxs-lookup"><span data-stu-id="67c02-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="67c02-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="67c02-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="67c02-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="67c02-103">'Blazor'</span></span>
- <span data-ttu-id="67c02-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="67c02-104">'Identity'</span></span>
- <span data-ttu-id="67c02-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="67c02-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="67c02-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="67c02-106">'Razor'</span></span>
- <span data-ttu-id="67c02-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="67c02-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="67c02-108">ASP.NET Core Blazor サーバーの追加のセキュリティシナリオ</span><span class="sxs-lookup"><span data-stu-id="67c02-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="67c02-109">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="67c02-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="67c02-110">サーバーアプリにトークンを渡す Blazor</span><span class="sxs-lookup"><span data-stu-id="67c02-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="67c02-111">サーバーアプリのコンポーネントの外部で利用できるトークンは Razor Blazor 、このセクションで説明する方法を使用してコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="67c02-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="67c02-112">完全な例を含むサンプルコードについ `Startup.ConfigureServices` ては、「[サーバー側 Blazor アプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67c02-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="67c02-113">通常の Blazor ページや MVC アプリの場合と同様に、サーバーアプリを認証し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="67c02-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="67c02-114">トークンをプロビジョニングし、認証 cookie に保存します。</span><span class="sxs-lookup"><span data-stu-id="67c02-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="67c02-115">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="67c02-115">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="67c02-116">アクセストークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="67c02-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="67c02-117">**scoped** Blazor [依存関係の挿入 (DI)](xref:blazor/dependency-injection)からトークンを解決するためにアプリ内で使用できるスコープ付きトークンプロバイダーサービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="67c02-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="67c02-118">で `Startup.ConfigureServices` 、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="67c02-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="67c02-119">*_Host*ファイルで、のインスタンスを作成し、 `InitialApplicationState` それをパラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="67c02-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="67c02-120">`App`コンポーネント (*app.xaml*) で、サービスを解決し、パラメーターのデータで初期化します。</span><span class="sxs-lookup"><span data-stu-id="67c02-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="67c02-121">セキュリティで保護された API 要求を行うサービスで、トークンプロバイダーを挿入し、API を呼び出すトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="67c02-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="67c02-122">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="67c02-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="67c02-123">認証ライブラリとテンプレートでは、 Blazor OPEN ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="67c02-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="67c02-124">V2.0 エンドポイントを使用するには、 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> でオプションを構成し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="67c02-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="67c02-125">または、アプリ設定 (*appsettings. json*) ファイルで設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="67c02-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="67c02-126">証明機関へのトラッキングがアプリの OIDC プロバイダー (AAD 以外のプロバイダーを含む) に適していない場合は、プロパティを直接設定し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="67c02-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="67c02-127"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>またはアプリ設定ファイルのプロパティを、キーを使用して設定し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="67c02-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="67c02-128">コードの変更</span><span class="sxs-lookup"><span data-stu-id="67c02-128">Code changes</span></span>

* <span data-ttu-id="67c02-129">V2.0 エンドポイントの ID トークンの変更要求の一覧。</span><span class="sxs-lookup"><span data-stu-id="67c02-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="67c02-130">詳細については、「 [Microsoft identity platform (v2.0) を更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="67c02-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="67c02-131">Azure のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="67c02-131">in the Azure documentation.</span></span>
* <span data-ttu-id="67c02-132">リソースは、v2.0 エンドポイントのスコープ Uri で指定されているため、 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 次のようにのプロパティ設定を削除し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ます。</span><span class="sxs-lookup"><span data-stu-id="67c02-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="67c02-133">OIDC プロバイダーのアプリ登録の説明で使用するアプリ ID URI を見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="67c02-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
