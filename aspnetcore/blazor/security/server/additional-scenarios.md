---
title: ASP.NET Core Blazor Server のセキュリティに関するその他のシナリオ
author: guardrex
description: セキュリティに関するその他のシナリオ用に Blazor Server を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 46de9a22dec540b8dfda7583b7a3c5c2dcbbc549
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402326"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="80eff-103">ASP.NET Core Blazor Server のセキュリティに関するその他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="80eff-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="80eff-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="80eff-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="80eff-105">Blazor Server アプリにトークンを渡す</span><span class="sxs-lookup"><span data-stu-id="80eff-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="80eff-106">Blazor Server アプリの Razor コンポーネントの外部で使用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="80eff-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="80eff-107">`Startup.ConfigureServices` の完全な例を含むサンプル コードについては、「[サーバー側の Blazor アプリケーションにトークンを渡す](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="80eff-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="80eff-108">通常の Razor Pages や MVC アプリの場合と同じように、Blazor Server アプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="80eff-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="80eff-109">トークンをプロビジョニングし、認証 cookie に保存します。</span><span class="sxs-lookup"><span data-stu-id="80eff-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="80eff-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="80eff-110">For example:</span></span>

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

<span data-ttu-id="80eff-111">アクセス トークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="80eff-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="80eff-112">[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) からトークンを解決するために Blazor アプリ内で使用できる**スコープを持つ**トークン プロバイダー サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="80eff-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="80eff-113">`Startup.ConfigureServices` で、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="80eff-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="80eff-114">`_Host.cshtml` ファイルで、`InitialApplicationState` のインスタンスを作成し、それをパラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="80eff-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="80eff-115">`App` コンポーネント (`App.razor`) で、サービスを解決し、パラメーターからのデータを使用してそれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="80eff-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="80eff-116">セキュリティで保護された API 要求を行うサービスで、トークン プロバイダーを挿入し、API を呼び出すトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="80eff-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="80eff-117">認証スキームを設定する</span><span class="sxs-lookup"><span data-stu-id="80eff-117">Set the authentication scheme</span></span>

<span data-ttu-id="80eff-118">複数の認証ミドルウェアを使用していることにより、複数の認証スキームがあるアプリの場合、Blazor で使用されるスキームを、`Startup.Configure` のエンドポイント構成で明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="80eff-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="80eff-119">次の例では、Azure Active Directory スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="80eff-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="80eff-120">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="80eff-120">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="80eff-121">認証ライブラリと Blazor テンプレートでは、Open ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="80eff-121">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="80eff-122">v2.0 エンドポイントを使用するには、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> で <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> オプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="80eff-122">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="80eff-123">または、アプリ設定ファイル (`appsettings.json`) で設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="80eff-123">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="80eff-124">証明機関へのセグメントを追跡することがアプリの OIDC プロバイダー (AAD 以外のプロバイダーなど) にとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。</span><span class="sxs-lookup"><span data-stu-id="80eff-124">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="80eff-125"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> またはアプリ設定ファイルで <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> キーを使用してプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="80eff-125">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="80eff-126">コード変更</span><span class="sxs-lookup"><span data-stu-id="80eff-126">Code changes</span></span>

* <span data-ttu-id="80eff-127">ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。</span><span class="sxs-lookup"><span data-stu-id="80eff-127">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="80eff-128">詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を</span><span class="sxs-lookup"><span data-stu-id="80eff-128">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="80eff-129">Azure のドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="80eff-129">in the Azure documentation.</span></span>
* <span data-ttu-id="80eff-130">リソースは v2.0 エンドポイントのスコープ URI で指定されているため、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> の <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> プロパティ設定を削除します。</span><span class="sxs-lookup"><span data-stu-id="80eff-130">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

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

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="80eff-131">使用するアプリ ID URI は、OIDC プロバイダーのアプリ登録の説明で見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="80eff-131">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>