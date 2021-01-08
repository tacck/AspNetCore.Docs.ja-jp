---
title: ASP.NET Core Blazor WebAssembly で Graph API を使用する
author: guardrex
description: Blazor WebAssemlby アプリで Graph API を使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 58c201d6d1172c1ff82521589f988e33d5c984ae
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854497"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="8750c-103">ASP.NET Core Blazor WebAssembly で Graph API を使用する</span><span class="sxs-lookup"><span data-stu-id="8750c-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="8750c-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8750c-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8750c-105">[Microsoft Graph API](/graph/use-the-api) は、Blazor および他の .NET Framework アプリが Microsoft Cloud サービス リソースにアクセスできるようにする RESTful Web API です。</span><span class="sxs-lookup"><span data-stu-id="8750c-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="8750c-106">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="8750c-106">Graph SDK</span></span>

<span data-ttu-id="8750c-107">[Microsoft Graph SDK](/graph/sdks/sdks-overview) は、Microsoft Graph にアクセスする高品質で効率的であり、回復性があるアプリケーションのビルドを簡素化するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="8750c-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="8750c-108">このセクションの例では、スタンドアロンのプロジェクト ファイルまたは *`Client`* アプリのプロジェクト ファイルに次のパッケージのパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="8750c-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="8750c-109">次のユーティリティ クラスおよび構成は、この記事の次の各サブセクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8750c-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="8750c-110">Graph SDK を使用してコンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="8750c-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="8750c-111">Graph SDK を使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8750c-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="8750c-112">Azure portal の AAD 領域で Microsoft Graph API スコープを追加した後:</span><span class="sxs-lookup"><span data-stu-id="8750c-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="8750c-113">ホストされている Blazor ソリューションのスタンドアロン アプリまたは *`Client`* アプリに次の `GraphClientExtensions.cs` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="8750c-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="8750c-114">`AuthenticateRequestAsync` メソッド内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> プロパティに必要なスコープを提供します。</span><span class="sxs-lookup"><span data-stu-id="8750c-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="8750c-115">次の例では、この記事の後述のセクションにある例と一致するように `User.Read` スコープを指定しています。</span><span class="sxs-lookup"><span data-stu-id="8750c-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="8750c-116">前のコードのスコープのプレースホルダー `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` は、1 つ以上の許可されたスコープを表します。</span><span class="sxs-lookup"><span data-stu-id="8750c-116">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="8750c-117">たとえば、`Scopes` を、この記事の後のセクションにある例の `User.Read` の 1 つのスコープの文字列配列に設定します。</span><span class="sxs-lookup"><span data-stu-id="8750c-117">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="8750c-118">`Program.Main` (`Program.cs`) で、`AddGraphClient` 拡張メソッドを使用して Graph クライアント サービスと構成を追加します。</span><span class="sxs-lookup"><span data-stu-id="8750c-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="8750c-119">前のコードのスコープのプレースホルダー `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` は、1 つ以上の許可されたスコープを表します。</span><span class="sxs-lookup"><span data-stu-id="8750c-119">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="8750c-120">たとえば、`User.Read` を、この記事の次のセクションにある例の `AddGraphClient` スコープに渡します。</span><span class="sxs-lookup"><span data-stu-id="8750c-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="8750c-121">Graph SDK を使用してコンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="8750c-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="8750c-122">このセクションでは、この記事で既に説明した [ユーティリティ クラス (`GraphClientExtensions.cs`)](#graph-sdk) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8750c-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="8750c-123">次の `GraphExample` コンポーネントでは、挿入された `GraphServiceClient` を使用してユーザーの AAD プロファイル データを取得し、その携帯電話番号を表示します。</span><span class="sxs-lookup"><span data-stu-id="8750c-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="8750c-124">Graph SDK を使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8750c-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="8750c-125">このセクションでは、この記事で既に説明した [ユーティリティ クラス (`GraphClientExtensions.cs`)](#graph-sdk) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8750c-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="8750c-126">次の例のアプリでは、AAD ユーザー プロファイルの携帯電話番号からユーザーの携帯電話番号要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="8750c-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="8750c-127">このアプリには、AAD で構成された `User.Read` Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="8750c-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="8750c-128">次のカスタム ユーザー アカウント ファクトリでは、フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> はユーザーのアカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="8750c-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="8750c-129"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで必要な場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="8750c-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="8750c-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="8750c-130">`CustomAccountFactory.cs`:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="8750c-131">`Program.Main` (`Program.cs`) で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで使用されている場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="8750c-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a><span data-ttu-id="8750c-132">名前付きクライアントと Graph API</span><span class="sxs-lookup"><span data-stu-id="8750c-132">Named client with Graph API</span></span>

<span data-ttu-id="8750c-133">このセクションの例では、Graph API の名前付き <xref:System.Net.Http.HttpClient> を使用して、通話を処理するユーザーの携帯電話番号を取得します。</span><span class="sxs-lookup"><span data-stu-id="8750c-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="8750c-134">このセクションの例では、スタンドアロンのプロジェクト ファイルまたは *`Client`* アプリのプロジェクト ファイルに [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) のパッケージ参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="8750c-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="8750c-135">Graph API を使用するには、次のクラスとプロジェクト構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="8750c-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="8750c-136">次のクラスおよび構成は、この記事の次の各サブセクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8750c-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="8750c-137">コンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="8750c-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="8750c-138">Graph API と名前付きクライアントを使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8750c-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="8750c-139">Azure portal の AAD 領域で Microsoft Graph API スコープを追加した後、アプリの構成済み Graph API 用ハンドラーに必要なスコープを提供します。</span><span class="sxs-lookup"><span data-stu-id="8750c-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="8750c-140">次の例では、`User.Read` スコープのハンドラーを構成します。</span><span class="sxs-lookup"><span data-stu-id="8750c-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="8750c-141">さらにスコープを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8750c-141">Additional scopes can be added.</span></span>

<span data-ttu-id="8750c-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="8750c-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

<span data-ttu-id="8750c-143">`Program.Main` (`Program.cs`) では、Graph API の名前付き <xref:System.Net.Http.HttpClient> を構成します。</span><span class="sxs-lookup"><span data-stu-id="8750c-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="8750c-144">コンポーネントから Graph API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="8750c-144">Call Graph API from a component</span></span>

<span data-ttu-id="8750c-145">このセクションでは、この記事で既に説明した [Graph 認可メッセージ ハンドラー (`GraphAuthorizationMessageHandler.cs`) とアプリへの `Program.Main` の追加](#named-client-with-graph-api)を使用します。これにより、Graph API の名前付き <xref:System.Net.Http.HttpClient> が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8750c-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="8750c-146">Razor コンポーネントでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8750c-146">In a Razor component:</span></span>

* <span data-ttu-id="8750c-147">Graph API の <xref:System.Net.Http.HttpClient> を作成し、ユーザーのプロファイル データの要求を発行します。</span><span class="sxs-lookup"><span data-stu-id="8750c-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="8750c-148">`UserInfo.cs` クラスにより、必要なユーザー プロファイル プロパティを、<xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 属性と、AAD によってそれらのプロパティ用に使用される JSON 名を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="8750c-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="8750c-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="8750c-149">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                // Use userInfo.MobilePhone and callInfo.Message to make a call

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="8750c-150">Graph API と名前付きクライアントを使用してユーザー要求をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8750c-150">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="8750c-151">このセクションでは、この記事で既に説明した [Graph 認可メッセージ ハンドラー (`GraphAuthorizationMessageHandler.cs`) とアプリへの `Program.Main` の追加](#named-client-with-graph-api)を使用します。これにより、Graph API の名前付き <xref:System.Net.Http.HttpClient> が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8750c-151">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="8750c-152">次の例のアプリでは、AAD ユーザー プロファイルの携帯電話番号からユーザーの携帯電話番号要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="8750c-152">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="8750c-153">このアプリには、AAD で構成された `User.Read` Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="8750c-153">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="8750c-154">`UserInfo.cs` クラスをアプリに追加し、必要なユーザー プロファイル プロパティを、<xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> 属性と、AAD によってそれらのプロパティ用に使用される JSON 名を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="8750c-154">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="8750c-155">次のカスタム ユーザー アカウント ファクトリでは、フレームワークの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> はユーザーのアカウントを表します。</span><span class="sxs-lookup"><span data-stu-id="8750c-155">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="8750c-156"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで必要な場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="8750c-156">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="8750c-157">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="8750c-157">`CustomAccountFactory.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="8750c-158">`Program.Main` (`Program.cs`) で、カスタム ユーザー アカウント ファクトリを使用するように MSAL 認証を構成します。<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> を拡張するカスタム ユーザー アカウント クラスがアプリで使用されている場合は、そのカスタム ユーザー アカウント クラスを次のコード内の <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> と入れ替えます。</span><span class="sxs-lookup"><span data-stu-id="8750c-158">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

<span data-ttu-id="8750c-159">前の例は、MSAL で AAD 認証が使用されるアプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="8750c-159">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="8750c-160">OIDC と API 認証にも、同様のパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="8750c-160">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="8750c-161">詳細については、「[ペイロード要求を使用してユーザーをカスタマイズする](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim)」セクションの例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8750c-161">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
