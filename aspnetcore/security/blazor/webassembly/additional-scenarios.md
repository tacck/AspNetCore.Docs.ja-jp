---
title: Blazor追加のセキュリティシナリオを ASP.NET Core
author: guardrex
description: Blazor追加のセキュリティシナリオを実現するために webassembly 構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153473"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="18cd6-103">ASP.NET Core Blazor Webasの追加のセキュリティシナリオ</span><span class="sxs-lookup"><span data-stu-id="18cd6-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="18cd6-104">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="18cd6-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="18cd6-105">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="18cd6-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="18cd6-106">`AuthorizationMessageHandler`サービスをと共に使用して、 `HttpClient` アクセストークンを送信要求に接続できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="18cd6-107">トークンは、既存のサービスを使用して取得され `IAccessTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="18cd6-108">トークンを取得できない場合は、 `AccessTokenNotAvailableException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="18cd6-109">`AccessTokenNotAvailableException`には、 `Redirect` ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="18cd6-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="18cd6-110">は、 `AuthorizationMessageHandler` メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成でき `ConfigureHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="18cd6-111">次の例では、は `AuthorizationMessageHandler` `HttpClient` in `Program.Main` (*Program.cs*) を構成します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="18cd6-112">便宜上、アプリの `BaseAddressAuthorizationMessageHandler` ベースアドレスを承認された URL として事前に構成したが含まれています。</span><span class="sxs-lookup"><span data-stu-id="18cd6-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="18cd6-113">認証が有効な Blazor WebAssembly テンプレートが、サーバー API プロジェクトでを使用してを設定するようになりました <xref:System.Net.Http.IHttpClientFactory> <xref:System.Net.Http.HttpClient> `BaseAddressAuthorizationMessageHandler` 。</span><span class="sxs-lookup"><span data-stu-id="18cd6-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="18cd6-114">前の例でクライアントが作成された場所に `CreateClient` <xref:System.Net.Http.HttpClient> は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="18cd6-115">構成されたは、 <xref:System.Net.Http.HttpClient> 単純なパターンを使用して承認された要求を行うために使用され `try-catch` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="18cd6-116">`FetchData`コンポーネント (*Pages/FetchData. razor*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-116">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="18cd6-117">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="18cd6-117">Typed HttpClient</span></span>

<span data-ttu-id="18cd6-118">1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-118">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="18cd6-119">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="18cd6-119">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="18cd6-120">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-120">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="18cd6-121">`FetchData`コンポーネント (*Pages/FetchData. razor*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="18cd6-122">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="18cd6-122">Configure the HttpClient handler</span></span>

<span data-ttu-id="18cd6-123">ハンドラーは、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 送信 HTTP 要求のためにを使用してさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-123">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="18cd6-124">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-124">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="18cd6-125">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="18cd6-125">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="18cd6-126">通常、Blazor WebAssembly がセキュリティで保護された既定値を使用する場合、 <xref:System.Net.Http.HttpClient> アプリは、という名前のを構成することによって、認証されていない、または承認されていない WEB API 要求 <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="18cd6-126">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="18cd6-127">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-127">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="18cd6-128">前述の登録は、セキュリティで保護された既存の既定の登録に追加され <xref:System.Net.Http.HttpClient> ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-128">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="18cd6-129">コンポーネントは、認証されて <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> いない要求または未承認の要求を行うために、からを作成します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-129">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="18cd6-130">前の例では、サーバー API のコントローラーが `WeatherForecastNoAuthenticationController` 属性でマークされていません [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="18cd6-130">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="18cd6-131">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="18cd6-131">Request additional access tokens</span></span>

<span data-ttu-id="18cd6-132">アクセストークンは、を呼び出すことによって手動で取得でき `IAccessTokenProvider.RequestAccessToken` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-132">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="18cd6-133">次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="18cd6-133">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="18cd6-134">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-134">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="18cd6-135">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-135">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="18cd6-136">メソッドは、 `IAccessTokenProvider.RequestToken` アプリが特定のスコープセットを使用してアクセストークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-136">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="18cd6-137">Razor コンポーネントの場合:</span><span class="sxs-lookup"><span data-stu-id="18cd6-137">In a Razor component:</span></span>

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

<span data-ttu-id="18cd6-138">`TryGetToken`型</span><span class="sxs-lookup"><span data-stu-id="18cd6-138">`TryGetToken` returns:</span></span>

* <span data-ttu-id="18cd6-139">`true`を `token` 使用します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-139">`true` with the `token` for use.</span></span>
* <span data-ttu-id="18cd6-140">`false`トークンが取得されない場合は。</span><span class="sxs-lookup"><span data-stu-id="18cd6-140">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="18cd6-141">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="18cd6-141">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="18cd6-142">Blazor WebAssembly アプリで[Httpclient](xref:fundamentals/http-requests)とを使用して、 <xref:System.Net.Http.HttpRequestMessage> 要求をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-142">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="18cd6-143">たとえば、HTTP メソッドと要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-143">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="18cd6-144">次のコンポーネントは、 `POST` サーバー上の To Do LIST API エンドポイントに対して要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-144">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="18cd6-145">.NET WebAssembly の `HttpClient` の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-145">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="18cd6-146">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-146">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="18cd6-147">HTTP フェッチ要求オプションは、次の表に示す `HttpRequestMessage` 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-147">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="18cd6-148">`HttpRequestMessage` 拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="18cd6-148">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="18cd6-149">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="18cd6-149">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="18cd6-150">credentials</span><span class="sxs-lookup"><span data-stu-id="18cd6-150">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="18cd6-151">cache</span><span class="sxs-lookup"><span data-stu-id="18cd6-151">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="18cd6-152">モード</span><span class="sxs-lookup"><span data-stu-id="18cd6-152">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="18cd6-153">integrity</span><span class="sxs-lookup"><span data-stu-id="18cd6-153">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="18cd6-154">より汎用的な `SetBrowserRequestOption` 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-154">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="18cd6-155">通常、HTTP 応答は Blazor WebAssembly でバッファリングされ、応答コンテンツに対する同期読み取りのサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="18cd6-155">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="18cd6-156">応答ストリーミングのサポートを有効にするには、要求に対して `SetBrowserResponseStreamingEnabled` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-156">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="18cd6-157">クロスオリジン要求に資格情報を含めるには、`SetBrowserRequestCredentials` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-157">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="18cd6-158">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18cd6-158">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="18cd6-159">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-159">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="18cd6-160">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="18cd6-160">The following policy includes configuration for:</span></span>

* <span data-ttu-id="18cd6-161">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="18cd6-161">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="18cd6-162">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="18cd6-162">Any method (verb).</span></span>
* <span data-ttu-id="18cd6-163">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="18cd6-163">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="18cd6-164">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-164">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="18cd6-165">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="18cd6-165">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="18cd6-166">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18cd6-166">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="18cd6-167">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="18cd6-167">Handle token request errors</span></span>

<span data-ttu-id="18cd6-168">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証すると、認証状態は SPA 内および Id プロバイダー (IP) 内でローカルに保持され、ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-168">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="18cd6-169">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-169">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="18cd6-170">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-170">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="18cd6-171">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-171">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="18cd6-172">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="18cd6-172">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="18cd6-173">このシナリオは、ユーザーがアクセスしてログアウトした場合に発生し `https://login.microsoftonline.com` ます。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-173">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="18cd6-174">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-174">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="18cd6-175">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-175">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="18cd6-176">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="18cd6-176">They are part of the nature of SPAs.</span></span> <span data-ttu-id="18cd6-177">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-177">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="18cd6-178">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-178">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="18cd6-179">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-179">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="18cd6-180">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-180">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="18cd6-181">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-181">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="18cd6-182">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-182">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="18cd6-183">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-183">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="18cd6-184">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-184">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="18cd6-185">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-185">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="18cd6-186">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-186">Store the current page state in session storage.</span></span> <span data-ttu-id="18cd6-187">`OnInitializeAsync`の間、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-187">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="18cd6-188">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-188">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="18cd6-189">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-189">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="18cd6-190">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="18cd6-190">The following example shows how to:</span></span>

* <span data-ttu-id="18cd6-191">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-191">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="18cd6-192">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-192">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="18cd6-193">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="18cd6-193">Save app state before an authentication operation</span></span>

<span data-ttu-id="18cd6-194">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-194">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="18cd6-195">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-195">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="18cd6-196">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-196">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="18cd6-197">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-197">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="18cd6-198">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="18cd6-198">Customize app routes</span></span>

<span data-ttu-id="18cd6-199">既定では、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-199">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="18cd6-200">ルート</span><span class="sxs-lookup"><span data-stu-id="18cd6-200">Route</span></span>                            | <span data-ttu-id="18cd6-201">目的</span><span class="sxs-lookup"><span data-stu-id="18cd6-201">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="18cd6-202">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18cd6-202">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="18cd6-203">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-203">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="18cd6-204">何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-204">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="18cd6-205">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18cd6-205">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="18cd6-206">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-206">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="18cd6-207">何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-207">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="18cd6-208">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-208">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="18cd6-209">ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18cd6-209">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="18cd6-210">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="18cd6-210">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="18cd6-211">上の表に示されているルートは、を使用して構成 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-211">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="18cd6-212">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-212">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="18cd6-213">次の例では、すべてのパスの先頭にが付き `/security` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-213">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="18cd6-214">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-214">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="18cd6-215">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-215">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="18cd6-216">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、 `RemoteAuthenticatorView` 明示的なアクションパラメーターを使用してを表示します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-216">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="18cd6-217">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-217">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="18cd6-218">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="18cd6-218">Customize the authentication user interface</span></span>

<span data-ttu-id="18cd6-219">`RemoteAuthenticatorView`には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="18cd6-219">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="18cd6-220">各状態は、カスタムを渡すことによってカスタマイズでき `RenderFragment` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-220">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="18cd6-221">初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のようにを変更し `RemoteAuthenticatorView` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-221">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="18cd6-222">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="18cd6-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="18cd6-223">に `RemoteAuthenticatorView` は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-223">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="18cd6-224">ルート</span><span class="sxs-lookup"><span data-stu-id="18cd6-224">Route</span></span>                            | <span data-ttu-id="18cd6-225">フラグメント</span><span class="sxs-lookup"><span data-stu-id="18cd6-225">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="18cd6-226">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="18cd6-226">Customize the user</span></span>

<span data-ttu-id="18cd6-227">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-227">Users bound to the app can be customized.</span></span> <span data-ttu-id="18cd6-228">次の例では、すべての認証されたユーザーが、 `amr` ユーザーの認証方法ごとに要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-228">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="18cd6-229">クラスを拡張するクラスを作成し `RemoteUserAccount` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-229">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="18cd6-230">以下を拡張するファクトリを作成し `AccountClaimsPrincipalFactory<TAccount>` ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-230">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="18cd6-231">`CustomAccountFactory`使用中の認証プロバイダーのを登録します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-231">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="18cd6-232">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="18cd6-232">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="18cd6-233">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="18cd6-233">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="18cd6-234">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="18cd6-234">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="18cd6-235">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="18cd6-235">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="18cd6-236">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="18cd6-236">Support prerendering with authentication</span></span>

<span data-ttu-id="18cd6-237">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-237">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="18cd6-238">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="18cd6-238">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="18cd6-239">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="18cd6-239">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="18cd6-240">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-240">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="18cd6-241">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-241">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="18cd6-242">サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-242">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="18cd6-243">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-243">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="18cd6-244">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-244">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="18cd6-245">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-245">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="18cd6-246">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-246">Update the file's contents:</span></span>

* <span data-ttu-id="18cd6-247">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-247">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="18cd6-248">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-248">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="18cd6-249">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="18cd6-249">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="18cd6-250">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-250">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="18cd6-251">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-251">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="18cd6-252">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="18cd6-252">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="18cd6-253">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="18cd6-253">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="18cd6-254">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-254">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="18cd6-255">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="18cd6-255">In this scenario:</span></span>

* <span data-ttu-id="18cd6-256">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-256">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="18cd6-257">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-257">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="18cd6-258">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-258">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="18cd6-259">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="18cd6-259">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="18cd6-260">Identityサードパーティのログインプロバイダーを使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-260">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="18cd6-261">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-261">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="18cd6-262">ユーザーがログインすると、は Identity 認証プロセスの一環としてアクセストークンと更新トークンを収集します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-262">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="18cd6-263">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-263">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="18cd6-264">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="18cd6-264">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="18cd6-265">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-265">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="18cd6-266">そこから、サードパーティのアクセストークンを使用して、クライアント側から直接サードパーティの API リソースを呼び出し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-266">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="18cd6-267">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-267">We don't recommend this approach.</span></span> <span data-ttu-id="18cd6-268">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-268">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="18cd6-269">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-269">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="18cd6-270">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="18cd6-270">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="18cd6-271">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="18cd6-271">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="18cd6-272">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-272">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="18cd6-273">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="18cd6-273">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="18cd6-274">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="18cd6-274">Make an API call from the client to the server API.</span></span> <span data-ttu-id="18cd6-275">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="18cd6-275">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="18cd6-276">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-276">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="18cd6-277">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="18cd6-277">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="18cd6-278">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="18cd6-278">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
