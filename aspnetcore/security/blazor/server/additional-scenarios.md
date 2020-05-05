---
title: ASP.NET Core Blazorサーバーの追加のセキュリティシナリオ
author: guardrex
description: 追加のセキュリティシナリオBlazor用にサーバーを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772805"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="da360-103">ASP.NET Core Blazorサーバーの追加のセキュリティシナリオ</span><span class="sxs-lookup"><span data-stu-id="da360-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="da360-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="da360-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="da360-105">Blazorサーバーアプリにトークンを渡す</span><span class="sxs-lookup"><span data-stu-id="da360-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="da360-106">BlazorサーバーアプリのRazorコンポーネントの外部で利用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="da360-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="da360-107">完全`Startup.ConfigureServices`な例を含むサンプルコードについては、「[サーバー側Blazorアプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="da360-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="da360-108">通常RazorのBlazorページや MVC アプリの場合と同様に、サーバーアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="da360-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="da360-109">トークンをプロビジョニングし、認証 cookie に保存します。</span><span class="sxs-lookup"><span data-stu-id="da360-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="da360-110">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da360-110">For example:</span></span>

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

<span data-ttu-id="da360-111">アクセストークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="da360-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="da360-112">[依存関係の挿入 (DI)](xref:blazor/dependency-injection)からトークンを解決するBlazorためにアプリ内で使用できる**スコープ**付きトークンプロバイダーサービスを定義します。</span><span class="sxs-lookup"><span data-stu-id="da360-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="da360-113">で`Startup.ConfigureServices`、次のサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="da360-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="da360-114">*_Host*ファイルで、の`InitialApplicationState`インスタンスを作成し、それをパラメーターとしてアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="da360-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="da360-115">`App`コンポーネント (*app.xaml*) で、サービスを解決し、パラメーターのデータで初期化します。</span><span class="sxs-lookup"><span data-stu-id="da360-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="da360-116">セキュリティで保護された API 要求を行うサービスで、トークンプロバイダーを挿入し、API を呼び出すトークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="da360-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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
