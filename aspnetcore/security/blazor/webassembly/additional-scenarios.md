---
title: 追加Blazorのセキュリティシナリオを ASP.NET Core
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111176"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="2776c-102">ASP.NET Core Blazor Webasの追加のセキュリティシナリオ</span><span class="sxs-lookup"><span data-stu-id="2776c-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="2776c-103">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="2776c-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="2776c-104">この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="2776c-105">このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="2776c-106">追加のアクセストークンを要求する</span><span class="sxs-lookup"><span data-stu-id="2776c-106">Request additional access tokens</span></span>

<span data-ttu-id="2776c-107">ほとんどのアプリでは、使用する保護されたリソースと対話するためのアクセストークンのみが必要です。</span><span class="sxs-lookup"><span data-stu-id="2776c-107">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="2776c-108">場合によっては、アプリで2つ以上のリソースを操作するために複数のトークンが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="2776c-108">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="2776c-109">次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="2776c-109">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="2776c-110">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリ (`Program.Main`、 *Program.cs*) で追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-110">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="2776c-111">メソッド`IAccessTokenProvider.RequestToken`は、次の例に示すように、アプリが特定のスコープセットを使用してトークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="2776c-111">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
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

<span data-ttu-id="2776c-112">`TryGetToken`型</span><span class="sxs-lookup"><span data-stu-id="2776c-112">`TryGetToken` returns:</span></span>

* <span data-ttu-id="2776c-113">`true``token`を使用します。</span><span class="sxs-lookup"><span data-stu-id="2776c-113">`true` with the `token` for use.</span></span>
* <span data-ttu-id="2776c-114">`false`トークンが取得されない場合は。</span><span class="sxs-lookup"><span data-stu-id="2776c-114">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="2776c-115">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="2776c-115">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="2776c-116">サービス`AuthorizationMessageHandler`をと共に使用`HttpClient`して、アクセストークンを送信要求に接続できます。</span><span class="sxs-lookup"><span data-stu-id="2776c-116">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="2776c-117">トークンは、既存`IAccessTokenProvider`のサービスを使用して取得されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-117">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="2776c-118">トークンを取得できない場合`AccessTokenNotAvailableException`は、がスローされます。</span><span class="sxs-lookup"><span data-stu-id="2776c-118">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="2776c-119">`AccessTokenNotAvailableException`には`Redirect` 、ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2776c-119">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="2776c-120">は`AuthorizationMessageHandler` 、 `ConfigureHandler`メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="2776c-120">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="2776c-121">次の例では`AuthorizationMessageHandler` 、は`HttpClient` in `Program.Main` (*Program.cs*) を構成します。</span><span class="sxs-lookup"><span data-stu-id="2776c-121">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
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

<span data-ttu-id="2776c-122">便宜上、アプリの`BaseAddressAuthorizationMessageHandler`ベースアドレスを承認された URL として事前に構成したが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2776c-122">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="2776c-123">認証が有効な Blazor WebAssembly テンプレートでは、 `BaseAddressAuthorizationMessageHandler`次のように`HttpClient` [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests)を使用してを設定するようになりました。</span><span class="sxs-lookup"><span data-stu-id="2776c-123">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="2776c-124">前の例`CreateClient`でクライアントが作成された場所に`HttpClient`は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-124">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="2776c-125">構成`HttpClient`されたは、単純な`try-catch`パターンを使用して承認された要求を行うために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-125">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="2776c-126">次`FetchData`のコンポーネントは、天気予報データを要求します。</span><span class="sxs-lookup"><span data-stu-id="2776c-126">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="2776c-127">または、1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="2776c-127">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="2776c-128">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="2776c-128">*WeatherClient.cs*:</span></span>

```csharp
public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
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

<span data-ttu-id="2776c-129">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2776c-129">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="2776c-130">*Fetchdata。 razor*:</span><span class="sxs-lookup"><span data-stu-id="2776c-130">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="2776c-131">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="2776c-131">Handle token request errors</span></span>

<span data-ttu-id="2776c-132">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証すると、認証状態は SPA 内および Id プロバイダー (IP) 内でローカルに保持され、ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。</span><span class="sxs-lookup"><span data-stu-id="2776c-132">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="2776c-133">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-133">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="2776c-134">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="2776c-134">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="2776c-135">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="2776c-135">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="2776c-136">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="2776c-136">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="2776c-137">このシナリオは、ユーザーがアクセス`https://login.microsoftonline.com`してログアウトした場合に発生します。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-137">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="2776c-138">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="2776c-138">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="2776c-139">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="2776c-139">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="2776c-140">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="2776c-140">They are part of the nature of SPAs.</span></span> <span data-ttu-id="2776c-141">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="2776c-141">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="2776c-142">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-142">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="2776c-143">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-143">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="2776c-144">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-144">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="2776c-145">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="2776c-145">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="2776c-146">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="2776c-146">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="2776c-147">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-147">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="2776c-148">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-148">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="2776c-149">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="2776c-149">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="2776c-150">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="2776c-150">Store the current page state in session storage.</span></span> <span data-ttu-id="2776c-151">の`OnInitializeAsync`間、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2776c-151">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="2776c-152">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="2776c-152">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="2776c-153">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="2776c-153">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="2776c-154">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="2776c-154">The following example shows how to:</span></span>

* <span data-ttu-id="2776c-155">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="2776c-155">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="2776c-156">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="2776c-156">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="2776c-157">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="2776c-157">Save app state before an authentication operation</span></span>

<span data-ttu-id="2776c-158">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-158">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="2776c-159">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-159">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="2776c-160">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="2776c-160">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="2776c-161">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="2776c-161">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="2776c-162">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="2776c-162">Customize app routes</span></span>

<span data-ttu-id="2776c-163">既定では、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="2776c-163">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="2776c-164">ルート</span><span class="sxs-lookup"><span data-stu-id="2776c-164">Route</span></span>                            | <span data-ttu-id="2776c-165">目的</span><span class="sxs-lookup"><span data-stu-id="2776c-165">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="2776c-166">サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="2776c-166">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="2776c-167">サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="2776c-167">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="2776c-168">何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2776c-168">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="2776c-169">サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="2776c-169">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="2776c-170">サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="2776c-170">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="2776c-171">何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2776c-171">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="2776c-172">ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="2776c-172">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="2776c-173">ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="2776c-173">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="2776c-174">新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="2776c-174">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="2776c-175">上の表に示されているルートは`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`、を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="2776c-175">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="2776c-176">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2776c-176">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="2776c-177">次の例では、すべてのパスの先頭`/security`にが付きます。</span><span class="sxs-lookup"><span data-stu-id="2776c-177">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="2776c-178">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="2776c-178">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="2776c-179">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="2776c-179">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="2776c-180">完全に異なるパスを必要とする場合は、前述のようにルートを設定`RemoteAuthenticatorView`し、明示的なアクションパラメーターを使用してを表示します。</span><span class="sxs-lookup"><span data-stu-id="2776c-180">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="2776c-181">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="2776c-181">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="2776c-182">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="2776c-182">Customize the authentication user interface</span></span>

<span data-ttu-id="2776c-183">`RemoteAuthenticatorView`には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2776c-183">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="2776c-184">各状態は、カスタム`RenderFragment`を渡すことによってカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="2776c-184">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="2776c-185">初期ログインプロセス中に表示されるテキストをカスタマイズするには`RemoteAuthenticatorView` 、次のようにを変更します。</span><span class="sxs-lookup"><span data-stu-id="2776c-185">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="2776c-186">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="2776c-186">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="2776c-187">に`RemoteAuthenticatorView`は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="2776c-187">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="2776c-188">ルート</span><span class="sxs-lookup"><span data-stu-id="2776c-188">Route</span></span>                            | <span data-ttu-id="2776c-189">フラグメント</span><span class="sxs-lookup"><span data-stu-id="2776c-189">Fragment</span></span>                |
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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="2776c-190">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="2776c-190">Support prerendering with authentication</span></span>

<span data-ttu-id="2776c-191">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2776c-191">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="2776c-192">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="2776c-192">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="2776c-193">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="2776c-193">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="2776c-194">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="2776c-194">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
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

<span data-ttu-id="2776c-195">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="2776c-195">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="2776c-196">サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2776c-196">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="2776c-197">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="2776c-197">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="2776c-198">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="2776c-198">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="2776c-199">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2776c-199">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="2776c-200">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="2776c-200">Update the file's contents:</span></span>

* <span data-ttu-id="2776c-201">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="2776c-201">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="2776c-202">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2776c-202">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="2776c-203">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="2776c-203">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="2776c-204">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2776c-204">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="2776c-205">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="2776c-205">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="2776c-206">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2776c-206">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="2776c-207">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="2776c-207">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="2776c-208">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="2776c-208">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="2776c-209">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="2776c-209">In this scenario:</span></span>

* <span data-ttu-id="2776c-210">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="2776c-210">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="2776c-211">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="2776c-211">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="2776c-212">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2776c-212">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="2776c-213">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="2776c-213">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="2776c-214">サードパーティ ログイン プロバイダーで ID を構成します。</span><span class="sxs-lookup"><span data-stu-id="2776c-214">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="2776c-215">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="2776c-215">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="2776c-216">ユーザーがログインすると、認証プロセスの一環としてアクセス トークンと更新トークンが ID によって収集されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-216">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="2776c-217">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="2776c-217">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="2776c-218">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="2776c-218">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="2776c-219">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="2776c-219">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="2776c-220">そこから、サードパーティのアクセス トークンを使用して、クライアント上の ID から直接、サードパーティ API リソースを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2776c-220">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="2776c-221">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="2776c-221">We don't recommend this approach.</span></span> <span data-ttu-id="2776c-222">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-222">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="2776c-223">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="2776c-223">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="2776c-224">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="2776c-224">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="2776c-225">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="2776c-225">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="2776c-226">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="2776c-226">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="2776c-227">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="2776c-227">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="2776c-228">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="2776c-228">Make an API call from the client to the server API.</span></span> <span data-ttu-id="2776c-229">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="2776c-229">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="2776c-230">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="2776c-230">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="2776c-231">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="2776c-231">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="2776c-232">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="2776c-232">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
