---
title: Blazor追加のセキュリティシナリオを ASP.NET Core
author: guardrex
description: Blazor追加のセキュリティシナリオを実現するために webassembly 構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: de752eb180767bbb269107ebc478a4422448f968
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84272035"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="15cd5-103">Blazor追加のセキュリティシナリオを ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15cd5-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="15cd5-104">[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="15cd5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="15cd5-105">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="15cd5-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="15cd5-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>サービスをと共に使用して、 <xref:System.Net.Http.HttpClient> アクセストークンを送信要求に接続できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="15cd5-107">トークンは、既存のサービスを使用して取得され <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="15cd5-108">トークンを取得できない場合は、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="15cd5-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>には、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="15cd5-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="15cd5-110">は、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成でき <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="15cd5-111">送信要求のメッセージハンドラーを構成するには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="15cd5-112">[カスタム AuthorizationMessageHandler クラス](#custom-authorizationmessagehandler-class)(*推奨*)</span><span class="sxs-lookup"><span data-stu-id="15cd5-112">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="15cd5-113">AuthorizationMessageHandler の構成</span><span class="sxs-lookup"><span data-stu-id="15cd5-113">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="15cd5-114">カスタム AuthorizationMessageHandler クラス</span><span class="sxs-lookup"><span data-stu-id="15cd5-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="15cd5-115">次の例では、を <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> 構成するために使用できるカスタムクラスを拡張してい <xref:System.Net.Http.HttpClient> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="15cd5-116">`Program.Main`(*Program.cs*) では、 <xref:System.Net.Http.HttpClient> はカスタム承認メッセージハンドラーで構成されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-116">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="15cd5-117">構成されたは、try-catch <xref:System.Net.Http.HttpClient> パターンを使用[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)して承認された要求を行うために使用されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-117">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="15cd5-118">クライアントがによって作成される場所 <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft の拡張子は Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)パッケージ) では、 <xref:System.Net.Http.HttpClient> サーバー API に要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-118">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="15cd5-119">AuthorizationMessageHandler の構成</span><span class="sxs-lookup"><span data-stu-id="15cd5-119">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="15cd5-120">次の例では、は <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*) を構成します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-120">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="15cd5-121">便宜上、アプリの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ベースアドレスを承認された URL として事前に構成したが含まれています。</span><span class="sxs-lookup"><span data-stu-id="15cd5-121">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="15cd5-122">認証が有効になっているテンプレートでは、 Blazor <xref:System.Net.Http.IHttpClientFactory> サーバー API プロジェクトで ([Microsoft. Extensions. Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)パッケージ) を使用してを設定し <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-122">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="15cd5-123">前の例でクライアントが作成された場所に <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="15cd5-124">構成されたは、try-catch <xref:System.Net.Http.HttpClient> パターンを使用[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)して承認された要求を行うために使用されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="15cd5-125">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="15cd5-125">Typed HttpClient</span></span>

<span data-ttu-id="15cd5-126">1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-126">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="15cd5-127">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="15cd5-127">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="15cd5-128">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-128">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="15cd5-129">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-129">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="15cd5-130">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="15cd5-130">Configure the HttpClient handler</span></span>

<span data-ttu-id="15cd5-131">ハンドラーは、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 送信 HTTP 要求のためにを使用してさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-131">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="15cd5-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="15cd5-133">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="15cd5-133">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="15cd5-134">通常、 Blazor セキュリティで保護された既定値を使用するアプリの場合、 <xref:System.Net.Http.HttpClient> アプリケーションは、という名前のを構成することによって、認証されていない、または承認されていない web API 要求を作成する <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="15cd5-134">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="15cd5-135">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-135">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="15cd5-136">前述の登録は、セキュリティで保護された既存の既定の登録に追加され <xref:System.Net.Http.HttpClient> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-136">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="15cd5-137">コンポーネントは、認証されて <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> いない要求または承認されていない要求を行うために、([Microsoft 拡張子 Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/)パッケージ) からを作成します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-137">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="15cd5-138">前の例では、サーバー API のコントローラーが `WeatherForecastNoAuthenticationController` 属性でマークされていません [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="15cd5-138">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="15cd5-139">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="15cd5-139">Request additional access tokens</span></span>

<span data-ttu-id="15cd5-140">アクセストークンは、を呼び出すことによって手動で取得でき `IAccessTokenProvider.RequestAccessToken` ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-140">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="15cd5-141">次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="15cd5-141">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="15cd5-142">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-142">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="15cd5-143">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-143">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="15cd5-144">メソッドは、 `IAccessTokenProvider.RequestToken` アプリが特定のスコープセットを使用してアクセストークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-144">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="15cd5-145">コンポーネント内 Razor :</span><span class="sxs-lookup"><span data-stu-id="15cd5-145">In a Razor component:</span></span>

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

<span data-ttu-id="15cd5-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>型</span><span class="sxs-lookup"><span data-stu-id="15cd5-146"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="15cd5-147">`true`を `token` 使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-147">`true` with the `token` for use.</span></span>
* <span data-ttu-id="15cd5-148">`false`トークンが取得されない場合は。</span><span class="sxs-lookup"><span data-stu-id="15cd5-148">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="15cd5-149">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="15cd5-149">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="15cd5-150">Webassembly で Blazor [httpclient](xref:fundamentals/http-requests)およびを使用して、 <xref:System.Net.Http.HttpRequestMessage> 要求をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-150">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="15cd5-151">たとえば、HTTP メソッドと要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-151">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="15cd5-152">次のコンポーネントは、 `POST` サーバー上の To Do LIST API エンドポイントに対して要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-152">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="15cd5-153">.NET WebAssembly の <xref:System.Net.Http.HttpClient> の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-153">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="15cd5-154">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-154">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="15cd5-155">HTTP フェッチ要求オプションは、次の表に示す <xref:System.Net.Http.HttpRequestMessage> 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-155">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="15cd5-156">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="15cd5-156">Extension method</span></span> | <span data-ttu-id="15cd5-157">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="15cd5-157">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="15cd5-158">credentials</span><span class="sxs-lookup"><span data-stu-id="15cd5-158">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="15cd5-159">cache</span><span class="sxs-lookup"><span data-stu-id="15cd5-159">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="15cd5-160">モード</span><span class="sxs-lookup"><span data-stu-id="15cd5-160">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="15cd5-161">integrity</span><span class="sxs-lookup"><span data-stu-id="15cd5-161">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="15cd5-162">より汎用的な <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-162">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="15cd5-163">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-163">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="15cd5-164">応答ストリーミングのサポートを有効にするには、要求に対して <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-164">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="15cd5-165">クロスオリジン要求に資格情報を含めるには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-165">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="15cd5-166">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15cd5-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="15cd5-167">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="15cd5-168">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="15cd5-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="15cd5-169">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="15cd5-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="15cd5-170">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="15cd5-170">Any method (verb).</span></span>
* <span data-ttu-id="15cd5-171">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="15cd5-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="15cd5-172">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="15cd5-173">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="15cd5-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="15cd5-174">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15cd5-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="15cd5-175">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="15cd5-175">Handle token request errors</span></span>

<span data-ttu-id="15cd5-176">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証する場合、認証状態は SPA 内およびプロバイダー (IP) 内でローカルに保持され、 Identity ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-176">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="15cd5-177">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-177">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="15cd5-178">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-178">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="15cd5-179">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-179">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="15cd5-180">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="15cd5-180">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="15cd5-181">このシナリオは、ユーザーがアクセスしてログアウトした場合に発生し `https://login.microsoftonline.com` ます。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-181">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="15cd5-182">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-182">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="15cd5-183">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-183">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="15cd5-184">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="15cd5-184">They are part of the nature of SPAs.</span></span> <span data-ttu-id="15cd5-185">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-185">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="15cd5-186">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-186">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="15cd5-187">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-187">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="15cd5-188">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-188">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="15cd5-189">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-189">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="15cd5-190">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-190">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="15cd5-191">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-191">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="15cd5-192">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-192">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="15cd5-193">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-193">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="15cd5-194">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-194">Store the current page state in session storage.</span></span> <span data-ttu-id="15cd5-195">[Oninitializer Edasync ライフサイクルイベント](xref:blazor/lifecycle#component-initialization-methods)() 中に <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-195">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="15cd5-196">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-196">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="15cd5-197">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-197">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="15cd5-198">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="15cd5-198">The following example shows how to:</span></span>

* <span data-ttu-id="15cd5-199">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-199">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="15cd5-200">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-200">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="15cd5-201">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="15cd5-201">Save app state before an authentication operation</span></span>

<span data-ttu-id="15cd5-202">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-202">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="15cd5-203">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-203">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="15cd5-204">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-204">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="15cd5-205">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-205">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="15cd5-206">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="15cd5-206">Customize app routes</span></span>

<span data-ttu-id="15cd5-207">既定では、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-207">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="15cd5-208">ルート</span><span class="sxs-lookup"><span data-stu-id="15cd5-208">Route</span></span>                            | <span data-ttu-id="15cd5-209">目的</span><span class="sxs-lookup"><span data-stu-id="15cd5-209">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="15cd5-210">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="15cd5-210">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="15cd5-211">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-211">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="15cd5-212">何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-212">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="15cd5-213">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="15cd5-213">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="15cd5-214">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-214">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="15cd5-215">何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-215">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="15cd5-216">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-216">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="15cd5-217">ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="15cd5-217">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="15cd5-218">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="15cd5-218">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="15cd5-219">上の表に示されているルートは、を使用して構成 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-219">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="15cd5-220">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-220">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="15cd5-221">次の例では、すべてのパスの先頭にが付き `/security` ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-221">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="15cd5-222">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="15cd5-223">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-223">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="15cd5-224">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 明示的なアクションパラメーターを使用してを表示します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-224">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="15cd5-225">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-225">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="15cd5-226">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="15cd5-226">Customize the authentication user interface</span></span>

<span data-ttu-id="15cd5-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="15cd5-227"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="15cd5-228">各状態は、カスタムを渡すことによってカスタマイズでき <xref:Microsoft.AspNetCore.Components.RenderFragment> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-228">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="15cd5-229">初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のようにを変更し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-229">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="15cd5-230">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="15cd5-230">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="15cd5-231">に <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-231">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="15cd5-232">ルート</span><span class="sxs-lookup"><span data-stu-id="15cd5-232">Route</span></span>                            | <span data-ttu-id="15cd5-233">フラグメント</span><span class="sxs-lookup"><span data-stu-id="15cd5-233">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="15cd5-234">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="15cd5-234">Customize the user</span></span>

<span data-ttu-id="15cd5-235">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-235">Users bound to the app can be customized.</span></span> <span data-ttu-id="15cd5-236">次の例では、すべての認証されたユーザーが、 `amr` ユーザーの認証方法ごとに要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-236">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="15cd5-237">クラスを拡張するクラスを作成し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-237">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="15cd5-238">以下を拡張するファクトリを作成し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-238">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="15cd5-239">`CustomAccountFactory`使用中の認証プロバイダーのを登録します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-239">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="15cd5-240">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="15cd5-240">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="15cd5-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="15cd5-241"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="15cd5-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="15cd5-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="15cd5-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="15cd5-243"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="15cd5-244">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="15cd5-244">Support prerendering with authentication</span></span>

<span data-ttu-id="15cd5-245">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-245">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="15cd5-246">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="15cd5-246">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="15cd5-247">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="15cd5-247">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="15cd5-248">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-248">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="15cd5-249">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-249">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="15cd5-250">サーバーアプリのメソッドで `Startup.Configure` 、[エンドポイントを置き換えます。エンドポイントを含む MapFallbackToFile ("index .html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [。MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="15cd5-250">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="15cd5-251">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-251">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="15cd5-252">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-252">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="15cd5-253">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-253">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="15cd5-254">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-254">Update the file's contents:</span></span>

* <span data-ttu-id="15cd5-255">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-255">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="15cd5-256">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-256">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="15cd5-257">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="15cd5-257">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="15cd5-258">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-258">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="15cd5-259">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-259">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="15cd5-260">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15cd5-260">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="15cd5-261">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="15cd5-261">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="15cd5-262">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-262">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="15cd5-263">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="15cd5-263">In this scenario:</span></span>

* <span data-ttu-id="15cd5-264">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-264">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="15cd5-265">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-265">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="15cd5-266">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-266">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="15cd5-267">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="15cd5-267">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="15cd5-268">Identityサードパーティのログインプロバイダーを使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-268">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="15cd5-269">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-269">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="15cd5-270">ユーザーがログインすると、は Identity 認証プロセスの一環としてアクセストークンと更新トークンを収集します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-270">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="15cd5-271">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-271">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="15cd5-272">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="15cd5-272">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="15cd5-273">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-273">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="15cd5-274">そこから、サードパーティのアクセストークンを使用して、クライアント側から直接サードパーティの API リソースを呼び出し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-274">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="15cd5-275">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-275">We don't recommend this approach.</span></span> <span data-ttu-id="15cd5-276">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-276">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="15cd5-277">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-277">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="15cd5-278">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="15cd5-278">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="15cd5-279">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="15cd5-279">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="15cd5-280">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-280">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="15cd5-281">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="15cd5-281">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="15cd5-282">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="15cd5-282">Make an API call from the client to the server API.</span></span> <span data-ttu-id="15cd5-283">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="15cd5-283">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="15cd5-284">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-284">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="15cd5-285">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-285">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="15cd5-286">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="15cd5-286">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="15cd5-287">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="15cd5-287">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="15cd5-288">認証ライブラリとテンプレートでは、 Blazor OPEN ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-288">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="15cd5-289">V2.0 エンドポイントを使用するには、JWT ベアラーオプションを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-289">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="15cd5-290">次の例では、プロパティにセグメントを追加することによって、バージョン2.0 用に AAD が構成されてい `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-290">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="15cd5-291">または、アプリ設定 (*appsettings. json*) ファイルで設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-291">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="15cd5-292">証明機関へのトラッキングがアプリの OIDC プロバイダー (AAD 以外のプロバイダーを含む) に適していない場合は、プロパティを直接設定し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-292">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="15cd5-293"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>キーを使用して、またはアプリ設定ファイル (*appsettings. json*) のプロパティを設定し `Authority` ます。</span><span class="sxs-lookup"><span data-stu-id="15cd5-293">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="15cd5-294">V2.0 エンドポイントの ID トークンの変更要求の一覧。</span><span class="sxs-lookup"><span data-stu-id="15cd5-294">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="15cd5-295">詳細については、「 [Microsoft identity platform (v2.0) を更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15cd5-295">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
