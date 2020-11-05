---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: のセキュリティに関するその他のシナリオ'
author: guardrex
description: 'セキュリティに関するその他のシナリオ用に :::no-loc(Blazor Server)::: を構成する方法について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234438"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="4fa18-103">ASP.NET Core :::no-loc(Blazor Server)::: のセキュリティに関するその他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="4fa18-103">ASP.NET Core :::no-loc(Blazor Server)::: additional security scenarios</span></span>

<span data-ttu-id="4fa18-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="4fa18-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="4fa18-105">:::no-loc(Blazor Server)::: アプリにトークンを渡す</span><span class="sxs-lookup"><span data-stu-id="4fa18-105">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="4fa18-106">:::no-loc(Blazor Server)::: アプリの :::no-loc(Razor)::: コンポーネントの外部で使用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-106">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="4fa18-107">通常の :::no-loc(Razor)::: Pages や MVC アプリの場合と同じように、:::no-loc(Blazor Server)::: アプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-107">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="4fa18-108">トークンをプロビジョニングし、認証 :::no-loc(cookie)::: に保存します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-108">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="4fa18-109">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="4fa18-110">必要があれば、`options.Scope.Add("{SCOPE}");` を使用して、さらにスコープを追加します。ここで、プレースホルダー `{SCOPE}` は追加するスコープです。</span><span class="sxs-lookup"><span data-stu-id="4fa18-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="4fa18-111">[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) からトークンを解決するために :::no-loc(Blazor)::: アプリ内で使用できる **スコープを持つ** トークン プロバイダー サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-111">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4fa18-112">`Startup.ConfigureServices` で、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4fa18-113">アクセス トークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4fa18-114">`_Host.cshtml` ファイルで、`InitialApplicationState` のインスタンスを作成し、それをパラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="4fa18-115">`App` コンポーネント (`App.razor`) で、サービスを解決し、パラメーターからのデータを使用してそれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="4fa18-116">[`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet パッケージのパッケージ参照をアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="4fa18-117">セキュリティで保護された API 要求を行うサービスで、トークン プロバイダーを挿入し、API 要求のトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="4fa18-118">認証スキームを設定する</span><span class="sxs-lookup"><span data-stu-id="4fa18-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="4fa18-119">複数の認証ミドルウェアを使用していることにより、複数の認証スキームがあるアプリの場合、:::no-loc(Blazor)::: で使用されるスキームを、`Startup.Configure` のエンドポイント構成で明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="4fa18-120">次の例では、Azure Active Directory スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="4fa18-121">:::no-loc(Blazor Server)::: アプリにトークンを渡す</span><span class="sxs-lookup"><span data-stu-id="4fa18-121">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="4fa18-122">:::no-loc(Blazor Server)::: アプリの :::no-loc(Razor)::: コンポーネントの外部で使用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-122">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="4fa18-123">通常の :::no-loc(Razor)::: Pages や MVC アプリの場合と同じように、:::no-loc(Blazor Server)::: アプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-123">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="4fa18-124">トークンをプロビジョニングし、認証 :::no-loc(cookie)::: に保存します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-124">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="4fa18-125">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="4fa18-126">必要があれば、`options.Scope.Add("{SCOPE}");` を使用して、さらにスコープを追加します。ここで、プレースホルダー `{SCOPE}` は追加するスコープです。</span><span class="sxs-lookup"><span data-stu-id="4fa18-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="4fa18-127">必要があれば、`options.Resource = "{RESOURCE}";` を使用してリソースを指定します。ここで、プレースホルダー `{RESOURCE}` はリソースです。</span><span class="sxs-lookup"><span data-stu-id="4fa18-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="4fa18-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="4fa18-129">アクセス トークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4fa18-130">[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) からトークンを解決するために :::no-loc(Blazor)::: アプリ内で使用できる **スコープを持つ** トークン プロバイダー サービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-130">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="4fa18-131">`Startup.ConfigureServices` で、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="4fa18-132">`_Host.cshtml` ファイルで、`InitialApplicationState` のインスタンスを作成し、それをパラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="4fa18-133">`App` コンポーネント (`App.razor`) で、サービスを解決し、パラメーターからのデータを使用してそれを初期化します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="4fa18-134">[`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet パッケージのパッケージ参照をアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="4fa18-135">セキュリティで保護された API 要求を行うサービスで、トークン プロバイダーを挿入し、API 要求のトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="4fa18-136">認証スキームを設定する</span><span class="sxs-lookup"><span data-stu-id="4fa18-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="4fa18-137">複数の認証ミドルウェアを使用していることにより、複数の認証スキームがあるアプリの場合、:::no-loc(Blazor)::: で使用されるスキームを、`Startup.Configure` のエンドポイント構成で明示的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="4fa18-138">次の例では、Azure Active Directory スキームを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="4fa18-139">OpenID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="4fa18-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="4fa18-140">5\.0 より前のバージョンの ASP.NET Core では、認証ライブラリと :::no-loc(Blazor)::: テンプレートに OpenID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-140">In versions of ASP.NET Core prior to 5.0, the authentication library and :::no-loc(Blazor)::: templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="4fa18-141">5\.0 より前のバージョンの ASP.NET Core で v2.0 エンドポイントを使用するには、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> に <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> オプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="4fa18-142">または、アプリ設定ファイル (`:::no-loc(appsettings.json):::`) で設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-142">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="4fa18-143">証明機関へのセグメントを追跡することがアプリの OIDC プロバイダー (AAD 以外のプロバイダーなど) にとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="4fa18-144"><xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> またはアプリ設定ファイルで <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> キーを使用してプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="4fa18-145">コード変更</span><span class="sxs-lookup"><span data-stu-id="4fa18-145">Code changes</span></span>

* <span data-ttu-id="4fa18-146">ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。</span><span class="sxs-lookup"><span data-stu-id="4fa18-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="4fa18-147">詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を</span><span class="sxs-lookup"><span data-stu-id="4fa18-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="4fa18-148">Azure のドキュメントで参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa18-148">in the Azure documentation.</span></span>
* <span data-ttu-id="4fa18-149">リソースは v2.0 エンドポイントのスコープ URI で指定されているため、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> の <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> プロパティ設定を削除します。</span><span class="sxs-lookup"><span data-stu-id="4fa18-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="4fa18-150">詳細については、Azure のドキュメントの「[リソースではなくスコープ](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4fa18-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="4fa18-151">アプリ ID URI</span><span class="sxs-lookup"><span data-stu-id="4fa18-151">App ID URI</span></span>

* <span data-ttu-id="4fa18-152">v2.0 エンドポイントを使用するとき、API により *`App ID URI`* が定義されます。これは API の一意の識別子を表すものです。</span><span class="sxs-lookup"><span data-stu-id="4fa18-152">When using v2.0 endpoints, APIs define an *`App ID URI`* , which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="4fa18-153">すべてのスコープにはプレフィックスとしてアプリ ID URI が含まれています。v2.0 エンドポイントからはアプリ ID URI を対象ユーザーとするアクセス トークンが発行されます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="4fa18-154">v2.0 エンドポイントを使用するとき、Server API で構成されたクライアント ID は API アプリケーション ID (クライアント ID) からアプリ ID URI に変更されます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="4fa18-155">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="4fa18-155">`:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="4fa18-156">使用するアプリ ID URI は、OIDC プロバイダーのアプリ登録の説明で見つけることができます。</span><span class="sxs-lookup"><span data-stu-id="4fa18-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
