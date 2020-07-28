---
title: ASP.NET Core Blazor WebAssembly のセキュリティに関するその他のシナリオ
author: guardrex
description: セキュリティに関するその他のシナリオ用に Blazor WebAssembly を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 79f7b2177d6d07101c73cde841c062b0e1468593
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445152"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="17138-103">ASP.NET Core Blazor WebAssembly のセキュリティに関するその他のシナリオ</span><span class="sxs-lookup"><span data-stu-id="17138-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="17138-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17138-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="17138-105">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="17138-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="17138-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> サービスを <xref:System.Net.Http.HttpClient> と共に使用して、アクセス トークンを送信要求に添付することができます。</span><span class="sxs-lookup"><span data-stu-id="17138-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="17138-107">トークンは、既存の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> サービスを使用して取得します。</span><span class="sxs-lookup"><span data-stu-id="17138-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="17138-108">トークンを取得できない場合は、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="17138-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="17138-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> には、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> メソッドがあります。このメソッドを使用すると、ユーザーを ID プロバイダーに移動して、新しいトークンを取得できます。</span><span class="sxs-lookup"><span data-stu-id="17138-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="17138-110"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> は、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> メソッドを使用して、承認された URL、スコープ、および戻り先 URL で構成できます。</span><span class="sxs-lookup"><span data-stu-id="17138-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="17138-111">送信要求のメッセージ ハンドラーの構成を行うには、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="17138-112">[カスタム `AuthorizationMessageHandler` クラス](#custom-authorizationmessagehandler-class) (*推奨*)</span><span class="sxs-lookup"><span data-stu-id="17138-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="17138-113">`AuthorizationMessageHandler` を構成する</span><span class="sxs-lookup"><span data-stu-id="17138-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="17138-114">カスタム AuthorizationMessageHandler クラス</span><span class="sxs-lookup"><span data-stu-id="17138-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="17138-115">次の例では、<xref:System.Net.Http.HttpClient> の構成を行うために使用できる <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> が、カスタム クラスによって拡張されます。</span><span class="sxs-lookup"><span data-stu-id="17138-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="17138-116">`Program.Main` (`Program.cs`) では、カスタム承認メッセージ ハンドラーを使用して、<xref:System.Net.Http.HttpClient> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="17138-117">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) を <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="17138-118">構成が行われた <xref:System.Net.Http.HttpClient> を使用し、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="17138-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="17138-119"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) パッケージ) を使用してクライアントが作成される場合にサーバー API への要求を行うと、アクセス トークンが含まれるインスタンスが <xref:System.Net.Http.HttpClient> に提供されます。</span><span class="sxs-lookup"><span data-stu-id="17138-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="17138-120">AuthorizationMessageHandler を構成する</span><span class="sxs-lookup"><span data-stu-id="17138-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="17138-121">次の例では、`Program.Main` (`Program.cs`) で、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> によって <xref:System.Net.Http.HttpClient> が構成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="17138-122">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> を以下に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="17138-123"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="17138-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="17138-124">`authorizedUrls` 配列の URL。</span><span class="sxs-lookup"><span data-stu-id="17138-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="17138-125">便宜上、アプリのベース アドレスを承認された URL として使用して事前構成が行われた <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> が含まれています。</span><span class="sxs-lookup"><span data-stu-id="17138-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="17138-126">認証が有効な Blazor WebAssembly テンプレートでは、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> を使用して <xref:System.Net.Http.HttpClient> を設定するためのサーバー API プロジェクトで、<xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) パッケージ) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="17138-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="17138-127">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) を <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="17138-128">前の例では、<xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> を使用してクライアントが作成される場合にサーバー プロジェクトへの要求を行うと、アクセス トークンが含まれるインスタンスが <xref:System.Net.Http.HttpClient> に提供されています。</span><span class="sxs-lookup"><span data-stu-id="17138-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="17138-129">構成された <xref:System.Net.Http.HttpClient> を使用し、[`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) パターンを使用して、承認された要求を行います。</span><span class="sxs-lookup"><span data-stu-id="17138-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="17138-130">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="17138-130">Typed HttpClient</span></span>

<span data-ttu-id="17138-131">単一クラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="17138-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="17138-132">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="17138-132">`WeatherForecastClient.cs`:</span></span>

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

<span data-ttu-id="17138-133">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `using static BlazorSample.Data;`)。</span><span class="sxs-lookup"><span data-stu-id="17138-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="17138-134">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="17138-135">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) を <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="17138-136">`FetchData` コンポーネント (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="17138-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="17138-137">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="17138-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="17138-138">ハンドラーは、送信 HTTP 要求の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> を使用して、さらに構成を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="17138-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="17138-139">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="17138-140">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> を以下に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="17138-141"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`)。</span><span class="sxs-lookup"><span data-stu-id="17138-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="17138-142">`authorizedUrls` 配列の URL。</span><span class="sxs-lookup"><span data-stu-id="17138-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="17138-143">セキュリティで保護された既定のクライアントを使用する、アプリ内の認証または承認されていない Web API 要求</span><span class="sxs-lookup"><span data-stu-id="17138-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="17138-144">通常、Blazor WebAssembly アプリがセキュリティで保護された既定の <xref:System.Net.Http.HttpClient> を使用する場合、アプリでは、名前付きの <xref:System.Net.Http.HttpClient> の構成を行うことで、認証または承認されていない Web API 要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="17138-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="17138-145">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="17138-146">Blazor WebAssembly のホステッド テンプレートに基づく Blazor アプリの場合、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) を <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="17138-147">前述の登録は、セキュリティで保護された既定の <xref:System.Net.Http.HttpClient> 登録に追加されます。</span><span class="sxs-lookup"><span data-stu-id="17138-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="17138-148">コンポーネントでは、<xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) パッケージ) から <xref:System.Net.Http.HttpClient> が作成され、認証または承認されていない要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="17138-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="17138-149">サーバー API のコントローラー (前の例では `WeatherForecastNoAuthenticationController`) では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性を使用してマークされることはありません。</span><span class="sxs-lookup"><span data-stu-id="17138-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="17138-150">既定の <xref:System.Net.Http.HttpClient> インスタンスとしてセキュリティで保護されたクライアントを使用するか、セキュリティで保護されていないクライアントを使用するかは、開発者が決定します。</span><span class="sxs-lookup"><span data-stu-id="17138-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="17138-151">この決定を行う方法の 1 つは、アプリが通信する認証済みのエンドポイントと認証されていないエンドポイントの数を考慮することです。</span><span class="sxs-lookup"><span data-stu-id="17138-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="17138-152">アプリの要求の大部分が API エンドポイントをセキュリティで保護する場合は、認証された <xref:System.Net.Http.HttpClient> インスタンスを既定として使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="17138-153">それ以外の場合は、認証されていない <xref:System.Net.Http.HttpClient> インスタンスを既定として登録します。</span><span class="sxs-lookup"><span data-stu-id="17138-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="17138-154"><xref:System.Net.Http.IHttpClientFactory> を使用する別の方法として、匿名エンドポイントへの認証されていないアクセス用に、[型指定されたクライアント](#typed-httpclient)を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="17138-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="17138-155">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="17138-155">Request additional access tokens</span></span>

<span data-ttu-id="17138-156">アクセス トークンは、`IAccessTokenProvider.RequestAccessToken` を呼び出して手動で取得できます。</span><span class="sxs-lookup"><span data-stu-id="17138-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="17138-157">次の例では、アプリでユーザー データの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="17138-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="17138-158">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアント アプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="17138-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-159">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="17138-160">`IAccessTokenProvider.RequestToken` メソッドには、指定されたスコープ セットを使用して、アプリでアクセス トークンをプロビジョニングできるようにするオーバーロードが用意されています。</span><span class="sxs-lookup"><span data-stu-id="17138-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="17138-161">Razor コンポーネントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="17138-161">In a Razor component:</span></span>

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

<span data-ttu-id="17138-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> が次のように返します。</span><span class="sxs-lookup"><span data-stu-id="17138-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="17138-163">`token` を使用する場合は `true`。</span><span class="sxs-lookup"><span data-stu-id="17138-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="17138-164">トークンが取得されない場合は `false`。</span><span class="sxs-lookup"><span data-stu-id="17138-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="17138-165">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="17138-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="17138-166">Blazor WebAssembly アプリで WebAssembly を実行するときに、[`HttpClient`](xref:fundamentals/http-requests) ([API ドキュメント](xref:System.Net.Http.HttpClient)) および <xref:System.Net.Http.HttpRequestMessage> を使用して要求をカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="17138-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="17138-167">たとえば、HTTP メソッドや要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="17138-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="17138-168">次のコンポーネントでは、サーバー上の To Do List API エンドポイントに `POST` 要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="17138-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@responseBody</p>

@code {
    private string responseBody;

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

            responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="17138-169">.NET WebAssembly の <xref:System.Net.Http.HttpClient> の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="17138-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="17138-170">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="17138-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="17138-171">HTTP フェッチ要求オプションは、次の表に示す <xref:System.Net.Http.HttpRequestMessage> 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="17138-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="17138-172">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="17138-172">Extension method</span></span> | <span data-ttu-id="17138-173">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="17138-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="17138-174">より汎用的な <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="17138-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="17138-175">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="17138-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="17138-176">応答ストリーミングのサポートを有効にするには、要求に対して <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="17138-177">クロスオリジン要求に資格情報を含めるには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="17138-178">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="17138-179">クロスオリジン リソース共有 (CORS)</span><span class="sxs-lookup"><span data-stu-id="17138-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="17138-180">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="17138-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="17138-181">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="17138-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="17138-182">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="17138-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="17138-183">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="17138-183">Any method (verb).</span></span>
* <span data-ttu-id="17138-184">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="17138-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="17138-185">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="17138-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="17138-186">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="17138-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="17138-187">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (`Components/HTTPRequestTester.razor`) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="17138-188">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="17138-188">Handle token request errors</span></span>

<span data-ttu-id="17138-189">シングル ページ アプリケーション (SPA) で Open ID Connect (OIDC) を使用してユーザーが認証されると、ユーザーが資格情報を入力したときに設定されるセッション Cookie の形式で、SPA 内および IdentityID プロバイダー (IP) 内で、認証状態がローカルに維持されます。</span><span class="sxs-lookup"><span data-stu-id="17138-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="17138-190">通常、IP によってユーザーに出力されるトークンが有効なのは短時間のため (通常は約 1 時間)、クライアント アプリでは定期的に新しいトークンをフェッチする必要があります。</span><span class="sxs-lookup"><span data-stu-id="17138-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="17138-191">それ以外の場合は、許可されたトークンの有効期限が切れると、ユーザーがログアウトします。</span><span class="sxs-lookup"><span data-stu-id="17138-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="17138-192">ほとんどの場合、OIDC クライアントでは、ユーザーに対して認証の再要求を行うことなく、新しいトークンをプロビジョニングできます。これは、認証状態または IP 内に保持される "セッション" によるものです。</span><span class="sxs-lookup"><span data-stu-id="17138-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="17138-193">場合によっては、ユーザーの介入なしに、クライアントでトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="17138-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="17138-194">このシナリオは、ユーザーが `https://login.microsoftonline.com` にアクセスしてログアウトした場合に発生します。これらのシナリオでは、ユーザーがログアウトしたことを、アプリはすぐに認識しません。クライアントで保持されるトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="17138-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="17138-195">また、クライアントでは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="17138-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="17138-196">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="17138-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="17138-197">これらは、SPA の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="17138-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="17138-198">また、認証 Cookie が削除されると、Cookie を使用する SPA でサーバー API を呼び出すこともできません。</span><span class="sxs-lookup"><span data-stu-id="17138-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="17138-199">保護されたリソースに対する API 呼び出しをアプリで実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17138-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="17138-200">API を呼び出すための新しいアクセス トークンをプロビジョニングするには、ユーザーの再認証が必要です。</span><span class="sxs-lookup"><span data-stu-id="17138-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="17138-201">有効かもしれないトークンがクライアントにある場合でも、そのトークンはユーザーによって取り消されているため、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="17138-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="17138-202">アプリでトークンが要求されている場合は、次の 2 つの結果が考えられます。</span><span class="sxs-lookup"><span data-stu-id="17138-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="17138-203">要求が成功します。アプリには有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="17138-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="17138-204">要求が失敗します。新しいトークンを取得するために、ユーザーの再認証が必要となります。</span><span class="sxs-lookup"><span data-stu-id="17138-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="17138-205">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17138-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="17138-206">次のようないくつかの方法がありますが、さらに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="17138-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="17138-207">現在のページの状態をセッション ストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="17138-207">Store the current page state in session storage.</span></span> <span data-ttu-id="17138-208">[`OnInitializedAsync` ライフサイクル イベント](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) 中に、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="17138-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="17138-209">クエリ文字列パラメーターを追加して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="17138-210">他の項目と競合するリスクなしにセッション ストレージにデータを格納するための一意識別子を持つクエリ文字列パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="17138-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="17138-211">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="17138-211">The following example shows how to:</span></span>

* <span data-ttu-id="17138-212">ログイン ページにリダイレクトする前に、状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="17138-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="17138-213">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="17138-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="17138-214">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="17138-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="17138-215">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="17138-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="17138-216">状態コンテナーを使用していて、認証が成功した後に状態を復元する場合には、このようなことが起こる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="17138-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="17138-217">カスタム認証状態オブジェクトを使用して、アプリ固有の状態、またはその参照を保持し、認証操作が正常に完了した後で、その状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="17138-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="17138-218">このアプローチの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="17138-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="17138-219">状態コンテナー クラスは、アプリの状態値を保持するプロパティを使用して、アプリ内に作成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="17138-220">次の例では、コンテナーを使用して、既定のテンプレートの `Counter` コンポーネント (`Pages/Counter.razor`) のカウンター値を維持します。</span><span class="sxs-lookup"><span data-stu-id="17138-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="17138-221">コンテナーをシリアル化および逆シリアル化するためのメソッドは、<xref:System.Text.Json> に基づいています。</span><span class="sxs-lookup"><span data-stu-id="17138-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

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

<span data-ttu-id="17138-222">`Counter` コンポーネントでは、状態コンテナーを使用して、コンポーネントの外部に `currentCount` 値を維持します。</span><span class="sxs-lookup"><span data-stu-id="17138-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

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

<span data-ttu-id="17138-223"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> から `ApplicationAuthenticationState` を作成します。</span><span class="sxs-lookup"><span data-stu-id="17138-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="17138-224">ローカルに格納されている状態の識別子として機能する `Id` プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="17138-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="17138-225">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="17138-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="17138-226">`Authentication` コンポーネント (`Pages/Authentication.razor`) では、`StateContainer` のシリアル化と逆シリアル化の方法である `GetStateForLocalStorage` および `SetStateFromLocalStorage` で、ローカル セッション ストレージを使用してアプリの状態を保存および復元します。</span><span class="sxs-lookup"><span data-stu-id="17138-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

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

<span data-ttu-id="17138-227">この例では、Azure Active Directory (AAD) を使用して認証を行います。</span><span class="sxs-lookup"><span data-stu-id="17138-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="17138-228">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="17138-229">`ApplicationAuthenticationState` は、Microsoft Authentication Library (MSAL) の `RemoteAuthenticationState` 型として、構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="17138-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="17138-230">状態コンテナーがサービス コンテナーに登録されます。</span><span class="sxs-lookup"><span data-stu-id="17138-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="17138-231">アプリ ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17138-231">Customize app routes</span></span>

<span data-ttu-id="17138-232">[`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) ライブラリでは既定で、次の表に示すルートを使用して、さまざまな認証状態が表されます。</span><span class="sxs-lookup"><span data-stu-id="17138-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="17138-233">ルート</span><span class="sxs-lookup"><span data-stu-id="17138-233">Route</span></span>                            | <span data-ttu-id="17138-234">目的</span><span class="sxs-lookup"><span data-stu-id="17138-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="17138-235">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="17138-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="17138-236">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="17138-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="17138-237">何らかの理由でサインイン操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="17138-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="17138-238">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="17138-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="17138-239">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="17138-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="17138-240">何らかの理由でサインアウト操作が失敗した場合に、エラー メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="17138-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="17138-241">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="17138-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="17138-242">ユーザー プロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="17138-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="17138-243">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="17138-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="17138-244">上の表に示すルートは、<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="17138-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="17138-245">カスタム ルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="17138-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="17138-246">次の例では、すべてのパスが `/security` で始まります。</span><span class="sxs-lookup"><span data-stu-id="17138-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="17138-247">`Authentication` コンポーネント (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="17138-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="17138-248">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="17138-248">`Program.Main` (`Program.cs`):</span></span>

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

<span data-ttu-id="17138-249">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、明示的なアクション パラメーターを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="17138-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="17138-250">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="17138-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="17138-251">認証ユーザー インターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17138-251">Customize the authentication user interface</span></span>

<span data-ttu-id="17138-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、各認証状態の UI 部分の既定のセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="17138-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="17138-253">各状態は、カスタム <xref:Microsoft.AspNetCore.Components.RenderFragment> を渡すことでカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="17138-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="17138-254">最初のログイン プロセス中に表示されるテキストをカスタマイズするには、次のように <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> を変更します。</span><span class="sxs-lookup"><span data-stu-id="17138-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="17138-255">`Authentication` コンポーネント (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="17138-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

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

<span data-ttu-id="17138-256"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> には、次の表に示す認証ルートごとに使用できるフラグメントが 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="17138-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="17138-257">ルート</span><span class="sxs-lookup"><span data-stu-id="17138-257">Route</span></span>                            | <span data-ttu-id="17138-258">Fragment</span><span class="sxs-lookup"><span data-stu-id="17138-258">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="17138-259">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="17138-259">Customize the user</span></span>

<span data-ttu-id="17138-260">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="17138-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="17138-261">次の例では、すべての認証されたユーザーが、ユーザーの認証方法ごとに `amr` 要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="17138-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="17138-262"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> クラスを拡張するクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="17138-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="17138-263"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> を拡張するファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="17138-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="17138-264">使用中の認証プロバイダーの `CustomAccountFactory` を登録します。</span><span class="sxs-lookup"><span data-stu-id="17138-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="17138-265">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="17138-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="17138-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="17138-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="17138-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="17138-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="17138-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="17138-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="17138-269">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="17138-269">Support prerendering with authentication</span></span>

<span data-ttu-id="17138-270">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="17138-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="17138-271">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="17138-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="17138-272">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="17138-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="17138-273">クライアント アプリの `Program` クラス (`Program.cs`) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="17138-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
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

<span data-ttu-id="17138-274">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="17138-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="17138-275">サーバー アプリの `Startup.Configure` メソッドで、[`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) を [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="17138-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="17138-276">サーバー アプリで、`Pages` フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="17138-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="17138-277">サーバー アプリの `Pages` フォルダー内に `_Host.cshtml` ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="17138-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="17138-278">クライアント アプリの `wwwroot/index.html` ファイルの内容を `Pages/_Host.cshtml` ファイルに貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="17138-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="17138-279">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="17138-279">Update the file's contents:</span></span>

* <span data-ttu-id="17138-280">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="17138-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="17138-281">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="17138-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="17138-282">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="17138-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="17138-283">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="17138-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="17138-284">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="17138-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="17138-285">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="17138-286">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="17138-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="17138-287">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="17138-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="17138-288">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="17138-288">In this scenario:</span></span>

* <span data-ttu-id="17138-289">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="17138-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="17138-290">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="17138-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="17138-291">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="17138-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="17138-292">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="17138-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="17138-293">サードパーティのログイン プロバイダーを使用して、Identity の構成を行います。</span><span class="sxs-lookup"><span data-stu-id="17138-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="17138-294">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="17138-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="17138-295">ユーザーがログインすると、認証プロセスの一環として、アクセス トークンと更新トークンが IdentityID によって収集されます。</span><span class="sxs-lookup"><span data-stu-id="17138-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="17138-296">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="17138-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="17138-297">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="17138-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="17138-298">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="17138-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="17138-299">そこから、サードパーティのアクセス トークンを使用して、クライアント上の IdentityID からサードパーティ API リソースを直接呼び出します。</span><span class="sxs-lookup"><span data-stu-id="17138-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="17138-300">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="17138-300">We don't recommend this approach.</span></span> <span data-ttu-id="17138-301">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="17138-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="17138-302">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="17138-303">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="17138-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="17138-304">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="17138-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="17138-305">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="17138-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="17138-306">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="17138-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="17138-307">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="17138-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="17138-308">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="17138-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="17138-309">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="17138-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="17138-310">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="17138-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="17138-311">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="17138-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="17138-312">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="17138-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="17138-313">認証ライブラリと Blazor テンプレートでは、Open ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="17138-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="17138-314">v2.0 エンドポイントを使用するには、JWT ベアラー <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> オプションの構成を行います。</span><span class="sxs-lookup"><span data-stu-id="17138-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="17138-315">次の例では、<xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> プロパティに `v2.0` セグメントを追加することで、v2.0 に対して AAD の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="17138-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="17138-316">または、アプリ設定ファイル (`appsettings.json`) で設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="17138-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="17138-317">証明機関へのセグメントを追跡することがアプリの OIDC プロバイダー (AAD 以外のプロバイダーなど) にとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。</span><span class="sxs-lookup"><span data-stu-id="17138-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="17138-318"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> またはアプリ設定ファイル (`appsettings.json`) で `Authority` キーを使用してプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="17138-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="17138-319">ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。</span><span class="sxs-lookup"><span data-stu-id="17138-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="17138-320">詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="17138-321">コンポーネントで gRPC を構成し、使用する</span><span class="sxs-lookup"><span data-stu-id="17138-321">Configure and use gRPC in components</span></span>

<span data-ttu-id="17138-322">[ASP.NET Core gRPC フレームワーク](xref:grpc/index)を使用するように Blazor WebAssembly アプリを構成するには:</span><span class="sxs-lookup"><span data-stu-id="17138-322">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="17138-323">サーバーで gRPC-Web を有効にします。</span><span class="sxs-lookup"><span data-stu-id="17138-323">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="17138-324">詳細については、「<xref:grpc/browser>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-324">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="17138-325">アプリのメッセージ ハンドラーに gRPC サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="17138-325">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="17138-326">次の例では、gRPC チュートリアル[ (`Program.Main`) から ](xref:tutorials/grpc/grpc-start#create-a-grpc-service)`GreeterClient` サービスを使用するように、アプリの認可メッセージ ハンドラーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="17138-326">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="17138-327">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="17138-327">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="17138-328">ホストされている Blazor ソリューションの `Shared` プロジェクトに `.proto` ファイルを置きます。</span><span class="sxs-lookup"><span data-stu-id="17138-328">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="17138-329">クライアント アプリのコンポーネントでは、gRPC クライアント (`Pages/Grpc.razor`) を使用し、gRPC 呼び出しを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="17138-329">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="17138-330">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="17138-330">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="17138-331">`Status.DebugException` プロパティを使用するには、[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) バージョン 2.30.0 以降を使用します。</span><span class="sxs-lookup"><span data-stu-id="17138-331">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="17138-332">詳細については、「<xref:grpc/browser>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17138-332">For more information, see <xref:grpc/browser>.</span></span>
