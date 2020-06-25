---
title: ASP.NET Core Blazor WebAssembly のその他のセキュリティ シナリオ
author: guardrex
description: その他のセキュリティ シナリオの Blazor WebAssembly の構成方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 2020b422ad48a9c4c52f2670fd3b5054aa4d60c5
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103410"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="52034-103">ASP.NET Core Blazor WebAssembly のその他のセキュリティ シナリオ</span><span class="sxs-lookup"><span data-stu-id="52034-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="52034-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="52034-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="52034-105">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="52034-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="52034-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> サービスを <xref:System.Net.Http.HttpClient> と共に使用して、アクセス トークンを送信要求に添付することができます。</span><span class="sxs-lookup"><span data-stu-id="52034-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="52034-107">トークンは、既存の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> サービスを使用して取得します。</span><span class="sxs-lookup"><span data-stu-id="52034-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="52034-108">トークンを取得できない場合は、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="52034-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="52034-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> には、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> メソッドがあります。このメソッドを使用すると、ユーザーを ID プロバイダーに移動して、新しいトークンを取得できます。</span><span class="sxs-lookup"><span data-stu-id="52034-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="52034-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> は、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> メソッドを使用して、承認された URL、スコープ、および戻り先 URL で構成できます。</span><span class="sxs-lookup"><span data-stu-id="52034-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="52034-111">送信要求のメッセージ ハンドラーの構成を行うには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="52034-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="52034-112">[カスタム AuthorizationMessageHandler クラス](#custom-authorizationmessagehandler-class) ("*推奨*")</span><span class="sxs-lookup"><span data-stu-id="52034-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="52034-113">AuthorizationMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="52034-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="52034-114">カスタム AuthorizationMessageHandler クラス</span><span class="sxs-lookup"><span data-stu-id="52034-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="52034-115">次の例では、<xref:System.Net.Http.HttpClient> の構成を行うために使用できる <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> が、カスタム クラスによって拡張されます。</span><span class="sxs-lookup"><span data-stu-id="52034-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="52034-116">`Program.Main` (*Program.cs*) では、カスタム承認メッセージ ハンドラーを使用して、<xref:System.Net.Http.HttpClient> の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="52034-117">構成が行われた <xref:System.Net.Http.HttpClient> を使用し、[try-catch](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="52034-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="52034-118"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージ) を使用してクライアントが作成される場合にサーバー API への要求を行うと、アクセス トークンが含まれるインスタンスが <xref:System.Net.Http.HttpClient> に提供されます。</span><span class="sxs-lookup"><span data-stu-id="52034-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="52034-119">AuthorizationMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="52034-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="52034-120">次の例では、`Program.Main` (*Program.cs*) で、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> によって <xref:System.Net.Http.HttpClient> の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="52034-121">便宜上、アプリのベース アドレスを承認された URL として使用して事前構成が行われた <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> が含まれています。</span><span class="sxs-lookup"><span data-stu-id="52034-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="52034-122">認証が有効な Blazor WebAssembly テンプレートでは、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> を使用して <xref:System.Net.Http.HttpClient> を設定するためのサーバー API プロジェクトで、<xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージ) を使用するようになりました。</span><span class="sxs-lookup"><span data-stu-id="52034-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="52034-123">前の例では、<xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> を使用してクライアントが作成される場合にサーバー プロジェクトへの要求を行うと、アクセス トークンが含まれるインスタンスが <xref:System.Net.Http.HttpClient> に提供されています。</span><span class="sxs-lookup"><span data-stu-id="52034-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="52034-124">構成が行われた <xref:System.Net.Http.HttpClient> を使用し、[try-catch](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="52034-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="52034-125">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="52034-125">Typed HttpClient</span></span>

<span data-ttu-id="52034-126">単一クラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="52034-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="52034-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="52034-127">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="52034-128">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `using static BlazorSample.Data;`)。</span><span class="sxs-lookup"><span data-stu-id="52034-128">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="52034-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="52034-130">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="52034-130">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="52034-131">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="52034-131">Configure the HttpClient handler</span></span>

<span data-ttu-id="52034-132">ハンドラーは、送信 HTTP 要求の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> を使用して、さらに構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="52034-132">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="52034-133">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="52034-134">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="52034-134">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="52034-135">通常、Blazor WebAssembly がセキュリティで保護された既定の <xref:System.Net.Http.HttpClient> を使用する場合、アプリでは、名前付きの <xref:System.Net.Http.HttpClient> の構成を行うことで、認証または承認されていない Web API 要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-135">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="52034-136">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-136">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="52034-137">前述の登録は、セキュリティで保護された既定の <xref:System.Net.Http.HttpClient> 登録に追加されます。</span><span class="sxs-lookup"><span data-stu-id="52034-137">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="52034-138">コンポーネントでは、<xref:System.Net.Http.IHttpClientFactory> ([Microsoft. Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) パッケージ) から <xref:System.Net.Http.HttpClient> が作成され、認証または承認されていない要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-138">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="52034-139">サーバー API のコントローラー (前の例では `WeatherForecastNoAuthenticationController`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性を使用してマークされることはありません。</span><span class="sxs-lookup"><span data-stu-id="52034-139">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="52034-140">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="52034-140">Request additional access tokens</span></span>

<span data-ttu-id="52034-141">アクセス トークンは、`IAccessTokenProvider.RequestAccessToken` を呼び出して手動で取得できます。</span><span class="sxs-lookup"><span data-stu-id="52034-141">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="52034-142">次の例では、アプリでユーザー データの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="52034-142">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="52034-143">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアント アプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="52034-143">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="52034-144">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-144">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="52034-145">`IAccessTokenProvider.RequestToken` メソッドには、指定されたスコープ セットを使用して、アプリでアクセス トークンをプロビジョニングできるようにするオーバーロードが用意されています。</span><span class="sxs-lookup"><span data-stu-id="52034-145">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="52034-146">Razor コンポーネントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="52034-146">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="52034-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> が次のように返します。</span><span class="sxs-lookup"><span data-stu-id="52034-147"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="52034-148">`token` を使用する場合は `true`。</span><span class="sxs-lookup"><span data-stu-id="52034-148">`true` with the `token` for use.</span></span>
* <span data-ttu-id="52034-149">トークンが取得されない場合は `false`。</span><span class="sxs-lookup"><span data-stu-id="52034-149">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="52034-150">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="52034-150">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="52034-151">Blazor WebAssembly アプリで WebAssembly を実行するときには、[HttpClient](xref:fundamentals/http-requests) および <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="52034-151">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="52034-152">たとえば、HTTP メソッドや要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="52034-152">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="52034-153">次のコンポーネントでは、サーバー上の To Do List API エンドポイントに `POST` 要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="52034-153">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@_responseBody</p>

@code {
    private string _responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            _responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="52034-154">.NET WebAssembly の <xref:System.Net.Http.HttpClient> の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="52034-154">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="52034-155">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="52034-155">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="52034-156">HTTP フェッチ要求オプションは、次の表に示す <xref:System.Net.Http.HttpRequestMessage> 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="52034-156">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="52034-157">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="52034-157">Extension method</span></span> | <span data-ttu-id="52034-158">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="52034-158">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="52034-159">credentials</span><span class="sxs-lookup"><span data-stu-id="52034-159">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="52034-160">cache</span><span class="sxs-lookup"><span data-stu-id="52034-160">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="52034-161">モード</span><span class="sxs-lookup"><span data-stu-id="52034-161">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="52034-162">integrity</span><span class="sxs-lookup"><span data-stu-id="52034-162">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="52034-163">より汎用的な <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="52034-163">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="52034-164">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="52034-164">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="52034-165">応答ストリーミングのサポートを有効にするには、要求に対して <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="52034-165">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="52034-166">クロスオリジン要求に資格情報を含めるには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="52034-166">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="52034-167">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52034-167">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="52034-168">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="52034-168">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="52034-169">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="52034-169">The following policy includes configuration for:</span></span>

* <span data-ttu-id="52034-170">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="52034-170">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="52034-171">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="52034-171">Any method (verb).</span></span>
* <span data-ttu-id="52034-172">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="52034-172">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="52034-173">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="52034-173">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="52034-174">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="52034-174">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="52034-175">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52034-175">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="52034-176">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="52034-176">Handle token request errors</span></span>

<span data-ttu-id="52034-177">シングル ページ アプリケーション (SPA) で Open ID Connect (OIDC) を使用してユーザーが認証されると、ユーザーが資格情報を入力したときに設定されるセッション Cookie の形式で、SPA 内および IdentityID プロバイダー (IP) 内で、認証状態がローカルに維持されます。</span><span class="sxs-lookup"><span data-stu-id="52034-177">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="52034-178">通常、IP によってユーザーに出力されるトークンが有効なのは短時間のため (通常は約 1 時間)、クライアント アプリでは定期的に新しいトークンをフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="52034-178">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="52034-179">それ以外の場合は、許可されたトークンの有効期限が切れると、ユーザーがログアウトします。</span><span class="sxs-lookup"><span data-stu-id="52034-179">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="52034-180">ほとんどの場合、OIDC クライアントでは、ユーザーに対して認証の再要求を行うことなく、新しいトークンをプロビジョニングできます。これは、認証状態または IP 内に保持される "セッション" によるものです。</span><span class="sxs-lookup"><span data-stu-id="52034-180">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="52034-181">場合によっては、ユーザーの介入なしに、クライアントでトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="52034-181">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="52034-182">このシナリオは、ユーザーが `https://login.microsoftonline.com` にアクセスしてログアウトした場合に発生します。これらのシナリオでは、ユーザーがログアウトしたことを、アプリはすぐに認識しません。クライアントで保持されるトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52034-182">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="52034-183">また、クライアントでは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="52034-183">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="52034-184">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="52034-184">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="52034-185">これらは、SPA の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="52034-185">They are part of the nature of SPAs.</span></span> <span data-ttu-id="52034-186">また、認証 Cookie が削除されると、Cookie を使用する SPA でサーバー API を呼び出すこともできません。</span><span class="sxs-lookup"><span data-stu-id="52034-186">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="52034-187">保護されたリソースに対する API 呼び出しをアプリで実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="52034-187">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="52034-188">API を呼び出すための新しいアクセス トークンをプロビジョニングするには、ユーザーの再認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="52034-188">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="52034-189">有効かもしれないトークンがクライアントにある場合でも、そのトークンはユーザーによって取り消されているため、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52034-189">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="52034-190">アプリでトークンが要求されている場合は、次の 2 つの結果が考えられます。</span><span class="sxs-lookup"><span data-stu-id="52034-190">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="52034-191">要求が成功します。アプリには有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="52034-191">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="52034-192">要求が失敗します。新しいトークンを取得するために、ユーザーの再認証が必要となります。</span><span class="sxs-lookup"><span data-stu-id="52034-192">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="52034-193">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="52034-193">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="52034-194">次のようないくつかの方法がありますが、さらに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="52034-194">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="52034-195">現在のページの状態をセッション ストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="52034-195">Store the current page state in session storage.</span></span> <span data-ttu-id="52034-196">[Oninitializer Edasync ライフサイクル イベント](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 中に、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="52034-196">During the [OnInitializedAsync lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="52034-197">クエリ文字列パラメーターを追加して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="52034-197">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="52034-198">他の項目と競合するリスクなしにセッション ストレージにデータを格納するための一意識別子を持つクエリ文字列パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="52034-198">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="52034-199">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="52034-199">The following example shows how to:</span></span>

* <span data-ttu-id="52034-200">ログイン ページにリダイレクトする前に、状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="52034-200">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="52034-201">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="52034-201">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="52034-202">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="52034-202">Save app state before an authentication operation</span></span>

<span data-ttu-id="52034-203">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="52034-203">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="52034-204">状態コンテナーを使用していて、認証が成功した後に状態を復元する場合には、このようなことが起こる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52034-204">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="52034-205">カスタム認証状態オブジェクトを使用して、アプリ固有の状態、またはその参照を保持し、認証操作が正常に完了した後で、その状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="52034-205">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="52034-206">このアプローチの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="52034-206">The following example demonstrates the approach.</span></span>

<span data-ttu-id="52034-207">状態コンテナー クラスは、アプリの状態値を保持するプロパティを使用して、アプリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="52034-207">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="52034-208">次の例では、コンテナーを使用して、既定のテンプレートの `Counter` コンポーネント (*Pages/Counter.razor*) のカウンター値を維持します。</span><span class="sxs-lookup"><span data-stu-id="52034-208">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (*Pages/Counter.razor*).</span></span> <span data-ttu-id="52034-209">コンテナーをシリアル化および逆シリアル化するためのメソッドは、<xref:System.Text.Json> に基づいています。</span><span class="sxs-lookup"><span data-stu-id="52034-209">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="52034-210">`Counter` コンポーネントでは、状態コンテナーを使用して、コンポーネントの外部に `currentCount` 値を維持します。</span><span class="sxs-lookup"><span data-stu-id="52034-210">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="52034-211"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> から `ApplicationAuthenticationState` を作成します。</span><span class="sxs-lookup"><span data-stu-id="52034-211">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="52034-212">ローカルに格納されている状態の識別子として機能する `Id` プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="52034-212">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="52034-213">*ApplicationAuthenticationState.cs*:</span><span class="sxs-lookup"><span data-stu-id="52034-213">*ApplicationAuthenticationState.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="52034-214">`Authentication` コンポーネント (*Pages/Authentication.razor*) では、`StateContainer` のシリアル化と逆シリアル化の方法である `GetStateForLocalStorage` および `SetStateFromLocalStorage` で、ローカル セッション ストレージを使用してアプリの状態を保存および復元します。</span><span class="sxs-lookup"><span data-stu-id="52034-214">The `Authentication` component (*Pages/Authentication.razor*) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="52034-215">この例では、Azure Active Directory (AAD) を使用して認証を行います。</span><span class="sxs-lookup"><span data-stu-id="52034-215">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="52034-216">In `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-216">In `Program.Main` (*Program.cs*):</span></span>

* <span data-ttu-id="52034-217">`ApplicationAuthenticationState` は、Microsoft Authentication Library (MSAL) の `RemoteAuthenticationState` 型として、構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-217">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="52034-218">状態コンテナーがサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="52034-218">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="52034-219">アプリ ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="52034-219">Customize app routes</span></span>

<span data-ttu-id="52034-220">[Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ライブラリでは既定で、次の表に示すルートを使用して、さまざまな認証状態が表されます。</span><span class="sxs-lookup"><span data-stu-id="52034-220">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="52034-221">ルート</span><span class="sxs-lookup"><span data-stu-id="52034-221">Route</span></span>                            | <span data-ttu-id="52034-222">目的</span><span class="sxs-lookup"><span data-stu-id="52034-222">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="52034-223">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="52034-223">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="52034-224">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="52034-224">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="52034-225">何らかの理由でサインイン操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="52034-225">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="52034-226">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="52034-226">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="52034-227">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="52034-227">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="52034-228">何らかの理由でサインアウト操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="52034-228">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="52034-229">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="52034-229">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="52034-230">ユーザー プロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="52034-230">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="52034-231">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="52034-231">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="52034-232">上の表に示すルートは、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="52034-232">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="52034-233">カスタム ルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="52034-233">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="52034-234">次の例では、すべてのパスが `/security` で始まります。</span><span class="sxs-lookup"><span data-stu-id="52034-234">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="52034-235">`Authentication` component (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="52034-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="52034-236">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="52034-236">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="52034-237">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、明示的なアクション パラメーターを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="52034-237">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="52034-238">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="52034-238">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="52034-239">認証ユーザー インターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="52034-239">Customize the authentication user interface</span></span>

<span data-ttu-id="52034-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、各認証状態の UI 部分の既定のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="52034-240"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="52034-241">各状態は、カスタム <xref:Microsoft.AspNetCore.Components.RenderFragment> を渡すことでカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="52034-241">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="52034-242">最初のログイン プロセス中に表示されるテキストをカスタマイズするには、次のように <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> を変更します。</span><span class="sxs-lookup"><span data-stu-id="52034-242">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="52034-243">`Authentication` component (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="52034-243">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="52034-244"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、次の表に示す認証ルートごとに使用できるフラグメントが 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="52034-244">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="52034-245">ルート</span><span class="sxs-lookup"><span data-stu-id="52034-245">Route</span></span>                            | <span data-ttu-id="52034-246">Fragment</span><span class="sxs-lookup"><span data-stu-id="52034-246">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="52034-247">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="52034-247">Customize the user</span></span>

<span data-ttu-id="52034-248">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="52034-248">Users bound to the app can be customized.</span></span> <span data-ttu-id="52034-249">次の例では、すべての認証されたユーザーが、ユーザーの認証方法ごとに `amr` 要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="52034-249">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="52034-250"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> クラスを拡張するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="52034-250">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="52034-251"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> を拡張するファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="52034-251">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="52034-252">使用中の認証プロバイダーの `CustomAccountFactory` を登録します。</span><span class="sxs-lookup"><span data-stu-id="52034-252">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="52034-253">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="52034-253">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="52034-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="52034-254"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="52034-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="52034-255"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="52034-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="52034-256"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="52034-257">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="52034-257">Support prerendering with authentication</span></span>

<span data-ttu-id="52034-258">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="52034-258">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="52034-259">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="52034-259">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="52034-260">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="52034-260">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="52034-261">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="52034-261">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="52034-262">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="52034-262">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="52034-263">サーバー アプリの `Startup.Configure` メソッドで、[endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) を [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="52034-263">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="52034-264">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="52034-264">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="52034-265">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="52034-265">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="52034-266">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="52034-266">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="52034-267">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="52034-267">Update the file's contents:</span></span>

* <span data-ttu-id="52034-268">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="52034-268">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="52034-269">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="52034-269">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="52034-270">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="52034-270">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="52034-271">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="52034-271">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="52034-272">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="52034-272">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="52034-273">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52034-273">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="52034-274">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="52034-274">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="52034-275">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="52034-275">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="52034-276">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="52034-276">In this scenario:</span></span>

* <span data-ttu-id="52034-277">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="52034-277">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="52034-278">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="52034-278">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="52034-279">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="52034-279">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="52034-280">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="52034-280">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="52034-281">サードパーティのログイン プロバイダーを使用して、Identity の構成を行います。</span><span class="sxs-lookup"><span data-stu-id="52034-281">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="52034-282">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="52034-282">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="52034-283">ユーザーがログインすると、認証プロセスの一環として、アクセス トークンと更新トークンが IdentityID によって収集されます。</span><span class="sxs-lookup"><span data-stu-id="52034-283">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="52034-284">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="52034-284">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="52034-285">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="52034-285">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="52034-286">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="52034-286">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="52034-287">そこから、サードパーティのアクセス トークンを使用して、クライアント上の IdentityID からサードパーティ API リソースを直接呼び出します。</span><span class="sxs-lookup"><span data-stu-id="52034-287">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="52034-288">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="52034-288">We don't recommend this approach.</span></span> <span data-ttu-id="52034-289">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="52034-289">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="52034-290">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="52034-290">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="52034-291">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="52034-291">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="52034-292">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="52034-292">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="52034-293">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="52034-293">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="52034-294">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="52034-294">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="52034-295">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="52034-295">Make an API call from the client to the server API.</span></span> <span data-ttu-id="52034-296">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="52034-296">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="52034-297">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="52034-297">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="52034-298">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="52034-298">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="52034-299">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="52034-299">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="52034-300">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="52034-300">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="52034-301">認証ライブラリと Blazor テンプレートでは、Open ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="52034-301">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="52034-302">v2.0 エンドポイントを使用するには、JWT ベアラー <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> オプションの構成を行います。</span><span class="sxs-lookup"><span data-stu-id="52034-302">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="52034-303">次の例では、<xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> プロパティに `v2.0` セグメントを追加することで、v2.0 に対して AAD の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="52034-303">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="52034-304">または、アプリ設定ファイル (*appsettings.json*) で設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="52034-304">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="52034-305">AAD 以外のプロバイダーなど、証明機関へのセグメントの追跡がアプリの OIDC プロバイダーにとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。</span><span class="sxs-lookup"><span data-stu-id="52034-305">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="52034-306">`Authority` キーを使用して、<xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> またはアプリ設定ファイル (*appsettings.json*) でプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="52034-306">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="52034-307">ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。</span><span class="sxs-lookup"><span data-stu-id="52034-307">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="52034-308">詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52034-308">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
