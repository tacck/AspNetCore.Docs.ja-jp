---
<span data-ttu-id="68312-101">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-102">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-103">'Blazor'</span></span>
- <span data-ttu-id="68312-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-104">'Identity'</span></span>
- <span data-ttu-id="68312-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-106">'Razor'</span></span>
- <span data-ttu-id="68312-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="68312-108">Blazor追加のセキュリティシナリオを ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="68312-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="68312-109">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="68312-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="68312-110">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="68312-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="68312-111">`AuthorizationMessageHandler`サービスをと共に使用して、 `HttpClient` アクセストークンを送信要求に接続できます。</span><span class="sxs-lookup"><span data-stu-id="68312-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="68312-112">トークンは、既存のサービスを使用して取得され `IAccessTokenProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="68312-113">トークンを取得できない場合は、 `AccessTokenNotAvailableException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="68312-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="68312-114">`AccessTokenNotAvailableException`には、 `Redirect` ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="68312-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="68312-115">は、 `AuthorizationMessageHandler` メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成でき `ConfigureHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="68312-116">次の例では、は `AuthorizationMessageHandler` `HttpClient` in `Program.Main` (*Program.cs*) を構成します。</span><span class="sxs-lookup"><span data-stu-id="68312-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="68312-117">便宜上、アプリの `BaseAddressAuthorizationMessageHandler` ベースアドレスを承認された URL として事前に構成したが含まれています。</span><span class="sxs-lookup"><span data-stu-id="68312-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="68312-118">認証が有効になったテンプレートでは、 Blazor サーバー API プロジェクトでを使用してを <xref:System.Net.Http.IHttpClientFactory> 設定し <xref:System.Net.Http.HttpClient> `BaseAddressAuthorizationMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="68312-119">前の例でクライアントが作成された場所に `CreateClient` <xref:System.Net.Http.HttpClient> は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="68312-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="68312-120">構成されたは、 <xref:System.Net.Http.HttpClient> 単純なパターンを使用して承認された要求を行うために使用され `try-catch` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="68312-121">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="68312-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="68312-122">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="68312-122">Typed HttpClient</span></span>

<span data-ttu-id="68312-123">1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="68312-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="68312-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="68312-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="68312-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="68312-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="68312-126">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="68312-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="68312-127">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="68312-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="68312-128">ハンドラーは、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 送信 HTTP 要求のためにを使用してさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="68312-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="68312-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="68312-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="68312-130">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="68312-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="68312-131">通常、 Blazor セキュリティで保護された既定値を使用するアプリの場合、 <xref:System.Net.Http.HttpClient> アプリケーションは、という名前のを構成することによって、認証されていない、または承認されていない web API 要求を作成する <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="68312-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="68312-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="68312-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="68312-133">前述の登録は、セキュリティで保護された既存の既定の登録に追加され <xref:System.Net.Http.HttpClient> ます。</span><span class="sxs-lookup"><span data-stu-id="68312-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="68312-134">コンポーネントは、認証されて <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> いない要求または未承認の要求を行うために、からを作成します。</span><span class="sxs-lookup"><span data-stu-id="68312-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="68312-135">前の例では、サーバー API のコントローラーが `WeatherForecastNoAuthenticationController` 属性でマークされていません [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="68312-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="68312-136">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="68312-136">Request additional access tokens</span></span>

<span data-ttu-id="68312-137">アクセストークンは、を呼び出すことによって手動で取得でき `IAccessTokenProvider.RequestAccessToken` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="68312-138">次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="68312-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="68312-139">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="68312-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="68312-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="68312-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="68312-141">メソッドは、 `IAccessTokenProvider.RequestToken` アプリが特定のスコープセットを使用してアクセストークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="68312-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="68312-142">コンポーネント内 Razor :</span><span class="sxs-lookup"><span data-stu-id="68312-142">In a Razor component:</span></span>

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

<span data-ttu-id="68312-143">`TryGetToken`型</span><span class="sxs-lookup"><span data-stu-id="68312-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="68312-144">`true`を `token` 使用します。</span><span class="sxs-lookup"><span data-stu-id="68312-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="68312-145">`false`トークンが取得されない場合は。</span><span class="sxs-lookup"><span data-stu-id="68312-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="68312-146">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="68312-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="68312-147">Webassembly で Blazor [httpclient](xref:fundamentals/http-requests)およびを使用して、 <xref:System.Net.Http.HttpRequestMessage> 要求をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="68312-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="68312-148">たとえば、HTTP メソッドと要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="68312-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="68312-149">次のコンポーネントは、 `POST` サーバー上の To Do LIST API エンドポイントに対して要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="68312-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="68312-150">.NET WebAssembly の `HttpClient` の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="68312-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="68312-151">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="68312-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="68312-152">HTTP フェッチ要求オプションは、次の表に示す `HttpRequestMessage` 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="68312-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="68312-153">`HttpRequestMessage` 拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="68312-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="68312-154">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="68312-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="68312-155">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-156">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-157">'Blazor'</span></span>
- <span data-ttu-id="68312-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-158">'Identity'</span></span>
- <span data-ttu-id="68312-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-160">'Razor'</span></span>
- <span data-ttu-id="68312-161">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-162">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-163">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-164">'Blazor'</span></span>
- <span data-ttu-id="68312-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-165">'Identity'</span></span>
- <span data-ttu-id="68312-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-167">'Razor'</span></span>
- <span data-ttu-id="68312-168">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-169">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-170">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-171">'Blazor'</span></span>
- <span data-ttu-id="68312-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-172">'Identity'</span></span>
- <span data-ttu-id="68312-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-174">'Razor'</span></span>
- <span data-ttu-id="68312-175">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-176">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-177">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-178">'Blazor'</span></span>
- <span data-ttu-id="68312-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-179">'Identity'</span></span>
- <span data-ttu-id="68312-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-181">'Razor'</span></span>
- <span data-ttu-id="68312-182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-183">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-184">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-185">'Blazor'</span></span>
- <span data-ttu-id="68312-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-186">'Identity'</span></span>
- <span data-ttu-id="68312-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-188">'Razor'</span></span>
- <span data-ttu-id="68312-189">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-190">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-191">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-192">'Blazor'</span></span>
- <span data-ttu-id="68312-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-193">'Identity'</span></span>
- <span data-ttu-id="68312-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-195">'Razor'</span></span>
- <span data-ttu-id="68312-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-197">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-198">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-199">'Blazor'</span></span>
- <span data-ttu-id="68312-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-200">'Identity'</span></span>
- <span data-ttu-id="68312-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-202">'Razor'</span></span>
- <span data-ttu-id="68312-203">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-204">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-205">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-206">'Blazor'</span></span>
- <span data-ttu-id="68312-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-207">'Identity'</span></span>
- <span data-ttu-id="68312-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-209">'Razor'</span></span>
- <span data-ttu-id="68312-210">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-211">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-212">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-213">'Blazor'</span></span>
- <span data-ttu-id="68312-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-214">'Identity'</span></span>
- <span data-ttu-id="68312-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-216">'Razor'</span></span>
- <span data-ttu-id="68312-217">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-218">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-219">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-220">'Blazor'</span></span>
- <span data-ttu-id="68312-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-221">'Identity'</span></span>
- <span data-ttu-id="68312-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-223">'Razor'</span></span>
- <span data-ttu-id="68312-224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-225">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-226">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-227">'Blazor'</span></span>
- <span data-ttu-id="68312-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-228">'Identity'</span></span>
- <span data-ttu-id="68312-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-230">'Razor'</span></span>
- <span data-ttu-id="68312-231">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-232">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-233">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-234">'Blazor'</span></span>
- <span data-ttu-id="68312-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-235">'Identity'</span></span>
- <span data-ttu-id="68312-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-237">'Razor'</span></span>
- <span data-ttu-id="68312-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-239">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-240">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-241">'Blazor'</span></span>
- <span data-ttu-id="68312-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-242">'Identity'</span></span>
- <span data-ttu-id="68312-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-244">'Razor'</span></span>
- <span data-ttu-id="68312-245">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-246">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-247">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-248">'Blazor'</span></span>
- <span data-ttu-id="68312-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-249">'Identity'</span></span>
- <span data-ttu-id="68312-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-251">'Razor'</span></span>
- <span data-ttu-id="68312-252">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-253">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-254">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-255">'Blazor'</span></span>
- <span data-ttu-id="68312-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-256">'Identity'</span></span>
- <span data-ttu-id="68312-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-258">'Razor'</span></span>
- <span data-ttu-id="68312-259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-260">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-261">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-262">'Blazor'</span></span>
- <span data-ttu-id="68312-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-263">'Identity'</span></span>
- <span data-ttu-id="68312-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-265">'Razor'</span></span>
- <span data-ttu-id="68312-266">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-266">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-267">------------------- |---タイトル: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-268">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-269">'Blazor'</span></span>
- <span data-ttu-id="68312-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-270">'Identity'</span></span>
- <span data-ttu-id="68312-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-272">'Razor'</span></span>
- <span data-ttu-id="68312-273">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-274">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-275">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-276">'Blazor'</span></span>
- <span data-ttu-id="68312-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-277">'Identity'</span></span>
- <span data-ttu-id="68312-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-279">'Razor'</span></span>
- <span data-ttu-id="68312-280">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-281">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-282">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-283">'Blazor'</span></span>
- <span data-ttu-id="68312-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-284">'Identity'</span></span>
- <span data-ttu-id="68312-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-286">'Razor'</span></span>
- <span data-ttu-id="68312-287">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-288">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-289">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-290">'Blazor'</span></span>
- <span data-ttu-id="68312-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-291">'Identity'</span></span>
- <span data-ttu-id="68312-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-293">'Razor'</span></span>
- <span data-ttu-id="68312-294">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-295">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-296">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-297">'Blazor'</span></span>
- <span data-ttu-id="68312-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-298">'Identity'</span></span>
- <span data-ttu-id="68312-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-300">'Razor'</span></span>
- <span data-ttu-id="68312-301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-302">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-303">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-304">'Blazor'</span></span>
- <span data-ttu-id="68312-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-305">'Identity'</span></span>
- <span data-ttu-id="68312-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-307">'Razor'</span></span>
- <span data-ttu-id="68312-308">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-309">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-310">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-311">'Blazor'</span></span>
- <span data-ttu-id="68312-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-312">'Identity'</span></span>
- <span data-ttu-id="68312-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-314">'Razor'</span></span>
- <span data-ttu-id="68312-315">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-316">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-317">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-318">'Blazor'</span></span>
- <span data-ttu-id="68312-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-319">'Identity'</span></span>
- <span data-ttu-id="68312-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-321">'Razor'</span></span>
- <span data-ttu-id="68312-322">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-323">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-324">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-325">'Blazor'</span></span>
- <span data-ttu-id="68312-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-326">'Identity'</span></span>
- <span data-ttu-id="68312-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-328">'Razor'</span></span>
- <span data-ttu-id="68312-329">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-329">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-330">----------- | |`SetBrowserRequestCredentials`         |  [資格情報](https://developer.mozilla.org/docs/Web/API/Request/credentials)| | `SetBrowserRequestCache`               | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | |`SetBrowserRequestMode`                |  [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           | [整合性](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span><span class="sxs-lookup"><span data-stu-id="68312-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="68312-331">より汎用的な `SetBrowserRequestOption` 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="68312-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="68312-332">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="68312-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="68312-333">応答ストリーミングのサポートを有効にするには、要求に対して `SetBrowserResponseStreamingEnabled` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="68312-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="68312-334">クロスオリジン要求に資格情報を含めるには、`SetBrowserRequestCredentials` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="68312-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="68312-335">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68312-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="68312-336">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="68312-337">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="68312-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="68312-338">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="68312-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="68312-339">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="68312-339">Any method (verb).</span></span>
* <span data-ttu-id="68312-340">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="68312-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="68312-341">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="68312-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="68312-342">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="68312-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="68312-343">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68312-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="68312-344">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="68312-344">Handle token request errors</span></span>

<span data-ttu-id="68312-345">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証する場合、認証状態は SPA 内およびプロバイダー (IP) 内でローカルに保持され、 Identity ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。</span><span class="sxs-lookup"><span data-stu-id="68312-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="68312-346">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="68312-347">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="68312-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="68312-348">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="68312-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="68312-349">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="68312-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="68312-350">このシナリオは、ユーザーがアクセスしてログアウトした場合に発生し `https://login.microsoftonline.com` ます。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68312-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="68312-351">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="68312-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="68312-352">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="68312-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="68312-353">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="68312-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="68312-354">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="68312-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="68312-355">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="68312-356">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="68312-357">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68312-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="68312-358">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="68312-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="68312-359">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="68312-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="68312-360">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="68312-361">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="68312-362">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="68312-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="68312-363">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="68312-363">Store the current page state in session storage.</span></span> <span data-ttu-id="68312-364">`OnInitializeAsync`の間、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="68312-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="68312-365">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="68312-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="68312-366">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="68312-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="68312-367">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="68312-367">The following example shows how to:</span></span>

* <span data-ttu-id="68312-368">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="68312-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="68312-369">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="68312-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="68312-370">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="68312-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="68312-371">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="68312-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="68312-372">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68312-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="68312-373">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="68312-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="68312-374">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="68312-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="68312-375">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="68312-375">Customize app routes</span></span>

<span data-ttu-id="68312-376">既定では、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="68312-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="68312-377">ルート</span><span class="sxs-lookup"><span data-stu-id="68312-377">Route</span></span>                            | <span data-ttu-id="68312-378">目的</span><span class="sxs-lookup"><span data-stu-id="68312-378">Purpose</span></span> |
| ---
<span data-ttu-id="68312-379">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-380">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-381">'Blazor'</span></span>
- <span data-ttu-id="68312-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-382">'Identity'</span></span>
- <span data-ttu-id="68312-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-384">'Razor'</span></span>
- <span data-ttu-id="68312-385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-386">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-387">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-388">'Blazor'</span></span>
- <span data-ttu-id="68312-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-389">'Identity'</span></span>
- <span data-ttu-id="68312-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-391">'Razor'</span></span>
- <span data-ttu-id="68312-392">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-393">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-394">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-395">'Blazor'</span></span>
- <span data-ttu-id="68312-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-396">'Identity'</span></span>
- <span data-ttu-id="68312-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-398">'Razor'</span></span>
- <span data-ttu-id="68312-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-400">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-401">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-402">'Blazor'</span></span>
- <span data-ttu-id="68312-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-403">'Identity'</span></span>
- <span data-ttu-id="68312-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-405">'Razor'</span></span>
- <span data-ttu-id="68312-406">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-407">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-408">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-409">'Blazor'</span></span>
- <span data-ttu-id="68312-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-410">'Identity'</span></span>
- <span data-ttu-id="68312-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-412">'Razor'</span></span>
- <span data-ttu-id="68312-413">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-414">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-415">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-416">'Blazor'</span></span>
- <span data-ttu-id="68312-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-417">'Identity'</span></span>
- <span data-ttu-id="68312-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-419">'Razor'</span></span>
- <span data-ttu-id="68312-420">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-421">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-422">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-423">'Blazor'</span></span>
- <span data-ttu-id="68312-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-424">'Identity'</span></span>
- <span data-ttu-id="68312-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-426">'Razor'</span></span>
- <span data-ttu-id="68312-427">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-428">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-429">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-430">'Blazor'</span></span>
- <span data-ttu-id="68312-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-431">'Identity'</span></span>
- <span data-ttu-id="68312-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-433">'Razor'</span></span>
- <span data-ttu-id="68312-434">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-435">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-436">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-437">'Blazor'</span></span>
- <span data-ttu-id="68312-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-438">'Identity'</span></span>
- <span data-ttu-id="68312-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-440">'Razor'</span></span>
- <span data-ttu-id="68312-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-442">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-443">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-444">'Blazor'</span></span>
- <span data-ttu-id="68312-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-445">'Identity'</span></span>
- <span data-ttu-id="68312-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-447">'Razor'</span></span>
- <span data-ttu-id="68312-448">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-449">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-450">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-451">'Blazor'</span></span>
- <span data-ttu-id="68312-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-452">'Identity'</span></span>
- <span data-ttu-id="68312-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-454">'Razor'</span></span>
- <span data-ttu-id="68312-455">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-456">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-457">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-458">'Blazor'</span></span>
- <span data-ttu-id="68312-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-459">'Identity'</span></span>
- <span data-ttu-id="68312-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-461">'Razor'</span></span>
- <span data-ttu-id="68312-462">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-463">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-464">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-465">'Blazor'</span></span>
- <span data-ttu-id="68312-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-466">'Identity'</span></span>
- <span data-ttu-id="68312-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-468">'Razor'</span></span>
- <span data-ttu-id="68312-469">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-470">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-471">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-472">'Blazor'</span></span>
- <span data-ttu-id="68312-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-473">'Identity'</span></span>
- <span data-ttu-id="68312-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-475">'Razor'</span></span>
- <span data-ttu-id="68312-476">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-476">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-477">---------------- |---タイトル: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-478">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-479">'Blazor'</span></span>
- <span data-ttu-id="68312-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-480">'Identity'</span></span>
- <span data-ttu-id="68312-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-482">'Razor'</span></span>
- <span data-ttu-id="68312-483">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-483">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-484">---- | |`authentication/login`           |サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="68312-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="68312-485">| |`authentication/login-callback`  |サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="68312-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="68312-486">| |`authentication/login-failed`    |何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="68312-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="68312-487">| |`authentication/logout`          |サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="68312-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="68312-488">| |`authentication/logout-callback` |サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="68312-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="68312-489">| |`authentication/logout-failed`   |何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="68312-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="68312-490">| |`authentication/logged-out`      |ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="68312-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="68312-491">| |`authentication/profile`         |ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="68312-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="68312-492">| |`authentication/register`        |新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="68312-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="68312-493">上の表に示されているルートは、を使用して構成 `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` できます。</span><span class="sxs-lookup"><span data-stu-id="68312-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="68312-494">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="68312-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="68312-495">次の例では、すべてのパスの先頭にが付き `/security` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="68312-496">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="68312-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="68312-497">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="68312-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="68312-498">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、 `RemoteAuthenticatorView` 明示的なアクションパラメーターを使用してを表示します。</span><span class="sxs-lookup"><span data-stu-id="68312-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="68312-499">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="68312-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="68312-500">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="68312-500">Customize the authentication user interface</span></span>

<span data-ttu-id="68312-501">`RemoteAuthenticatorView`には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="68312-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="68312-502">各状態は、カスタムを渡すことによってカスタマイズでき `RenderFragment` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="68312-503">初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のようにを変更し `RemoteAuthenticatorView` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="68312-504">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="68312-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="68312-505">に `RemoteAuthenticatorView` は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="68312-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="68312-506">ルート</span><span class="sxs-lookup"><span data-stu-id="68312-506">Route</span></span>                            | <span data-ttu-id="68312-507">フラグメント</span><span class="sxs-lookup"><span data-stu-id="68312-507">Fragment</span></span>                |
| ---
<span data-ttu-id="68312-508">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-509">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-510">'Blazor'</span></span>
- <span data-ttu-id="68312-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-511">'Identity'</span></span>
- <span data-ttu-id="68312-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-513">'Razor'</span></span>
- <span data-ttu-id="68312-514">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-515">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-516">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-517">'Blazor'</span></span>
- <span data-ttu-id="68312-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-518">'Identity'</span></span>
- <span data-ttu-id="68312-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-520">'Razor'</span></span>
- <span data-ttu-id="68312-521">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-522">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-523">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-524">'Blazor'</span></span>
- <span data-ttu-id="68312-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-525">'Identity'</span></span>
- <span data-ttu-id="68312-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-527">'Razor'</span></span>
- <span data-ttu-id="68312-528">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-529">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-530">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-531">'Blazor'</span></span>
- <span data-ttu-id="68312-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-532">'Identity'</span></span>
- <span data-ttu-id="68312-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-534">'Razor'</span></span>
- <span data-ttu-id="68312-535">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-536">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-537">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-538">'Blazor'</span></span>
- <span data-ttu-id="68312-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-539">'Identity'</span></span>
- <span data-ttu-id="68312-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-541">'Razor'</span></span>
- <span data-ttu-id="68312-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-543">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-544">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-545">'Blazor'</span></span>
- <span data-ttu-id="68312-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-546">'Identity'</span></span>
- <span data-ttu-id="68312-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-548">'Razor'</span></span>
- <span data-ttu-id="68312-549">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-550">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-551">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-552">'Blazor'</span></span>
- <span data-ttu-id="68312-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-553">'Identity'</span></span>
- <span data-ttu-id="68312-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-555">'Razor'</span></span>
- <span data-ttu-id="68312-556">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-557">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-558">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-559">'Blazor'</span></span>
- <span data-ttu-id="68312-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-560">'Identity'</span></span>
- <span data-ttu-id="68312-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-562">'Razor'</span></span>
- <span data-ttu-id="68312-563">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-564">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-565">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-566">'Blazor'</span></span>
- <span data-ttu-id="68312-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-567">'Identity'</span></span>
- <span data-ttu-id="68312-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-569">'Razor'</span></span>
- <span data-ttu-id="68312-570">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-571">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-572">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-573">'Blazor'</span></span>
- <span data-ttu-id="68312-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-574">'Identity'</span></span>
- <span data-ttu-id="68312-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-576">'Razor'</span></span>
- <span data-ttu-id="68312-577">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-578">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-579">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-580">'Blazor'</span></span>
- <span data-ttu-id="68312-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-581">'Identity'</span></span>
- <span data-ttu-id="68312-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-583">'Razor'</span></span>
- <span data-ttu-id="68312-584">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-585">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-586">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-587">'Blazor'</span></span>
- <span data-ttu-id="68312-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-588">'Identity'</span></span>
- <span data-ttu-id="68312-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-590">'Razor'</span></span>
- <span data-ttu-id="68312-591">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-592">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-593">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-594">'Blazor'</span></span>
- <span data-ttu-id="68312-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-595">'Identity'</span></span>
- <span data-ttu-id="68312-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-597">'Razor'</span></span>
- <span data-ttu-id="68312-598">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-599">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-600">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-601">'Blazor'</span></span>
- <span data-ttu-id="68312-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-602">'Identity'</span></span>
- <span data-ttu-id="68312-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-604">'Razor'</span></span>
- <span data-ttu-id="68312-605">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-605">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-606">---------------- |---タイトル: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-607">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-608">'Blazor'</span></span>
- <span data-ttu-id="68312-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-609">'Identity'</span></span>
- <span data-ttu-id="68312-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-611">'Razor'</span></span>
- <span data-ttu-id="68312-612">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-613">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-614">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-615">'Blazor'</span></span>
- <span data-ttu-id="68312-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-616">'Identity'</span></span>
- <span data-ttu-id="68312-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-618">'Razor'</span></span>
- <span data-ttu-id="68312-619">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-620">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-621">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-622">'Blazor'</span></span>
- <span data-ttu-id="68312-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-623">'Identity'</span></span>
- <span data-ttu-id="68312-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-625">'Razor'</span></span>
- <span data-ttu-id="68312-626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-627">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-628">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-629">'Blazor'</span></span>
- <span data-ttu-id="68312-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-630">'Identity'</span></span>
- <span data-ttu-id="68312-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-632">'Razor'</span></span>
- <span data-ttu-id="68312-633">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-634">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-635">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-636">'Blazor'</span></span>
- <span data-ttu-id="68312-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-637">'Identity'</span></span>
- <span data-ttu-id="68312-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-639">'Razor'</span></span>
- <span data-ttu-id="68312-640">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-641">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-642">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-643">'Blazor'</span></span>
- <span data-ttu-id="68312-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-644">'Identity'</span></span>
- <span data-ttu-id="68312-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-646">'Razor'</span></span>
- <span data-ttu-id="68312-647">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-648">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-649">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-650">'Blazor'</span></span>
- <span data-ttu-id="68312-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-651">'Identity'</span></span>
- <span data-ttu-id="68312-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-653">'Razor'</span></span>
- <span data-ttu-id="68312-654">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-655">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-656">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-657">'Blazor'</span></span>
- <span data-ttu-id="68312-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-658">'Identity'</span></span>
- <span data-ttu-id="68312-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-660">'Razor'</span></span>
- <span data-ttu-id="68312-661">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="68312-662">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="68312-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="68312-663">monikerRange: ms. author: ms. カスタム: ms. date: no loc:</span><span class="sxs-lookup"><span data-stu-id="68312-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="68312-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="68312-664">'Blazor'</span></span>
- <span data-ttu-id="68312-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="68312-665">'Identity'</span></span>
- <span data-ttu-id="68312-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="68312-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="68312-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="68312-667">'Razor'</span></span>
- <span data-ttu-id="68312-668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="68312-668">'SignalR' uid:</span></span> 

<span data-ttu-id="68312-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="68312-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="68312-670">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="68312-670">Customize the user</span></span>

<span data-ttu-id="68312-671">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="68312-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="68312-672">次の例では、すべての認証されたユーザーが、 `amr` ユーザーの認証方法ごとに要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="68312-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="68312-673">クラスを拡張するクラスを作成し `RemoteUserAccount` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="68312-674">以下を拡張するファクトリを作成し `AccountClaimsPrincipalFactory<TAccount>` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="68312-675">`CustomAccountFactory`使用中の認証プロバイダーのを登録します。</span><span class="sxs-lookup"><span data-stu-id="68312-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="68312-676">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="68312-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="68312-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="68312-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="68312-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="68312-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="68312-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="68312-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="68312-680">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="68312-680">Support prerendering with authentication</span></span>

<span data-ttu-id="68312-681">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="68312-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="68312-682">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="68312-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="68312-683">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="68312-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="68312-684">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="68312-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="68312-685">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="68312-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="68312-686">サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="68312-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="68312-687">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="68312-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="68312-688">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="68312-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="68312-689">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="68312-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="68312-690">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="68312-690">Update the file's contents:</span></span>

* <span data-ttu-id="68312-691">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="68312-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="68312-692">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="68312-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="68312-693">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="68312-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="68312-694">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="68312-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="68312-695">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="68312-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="68312-696">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68312-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="68312-697">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="68312-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="68312-698">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="68312-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="68312-699">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="68312-699">In this scenario:</span></span>

* <span data-ttu-id="68312-700">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="68312-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="68312-701">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="68312-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="68312-702">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="68312-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="68312-703">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="68312-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="68312-704">Identityサードパーティのログインプロバイダーを使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="68312-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="68312-705">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="68312-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="68312-706">ユーザーがログインすると、は Identity 認証プロセスの一環としてアクセストークンと更新トークンを収集します。</span><span class="sxs-lookup"><span data-stu-id="68312-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="68312-707">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="68312-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="68312-708">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="68312-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="68312-709">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="68312-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="68312-710">そこから、サードパーティのアクセストークンを使用して、クライアント側から直接サードパーティの API リソースを呼び出し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="68312-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="68312-711">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="68312-711">We don't recommend this approach.</span></span> <span data-ttu-id="68312-712">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="68312-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="68312-713">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="68312-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="68312-714">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="68312-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="68312-715">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="68312-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="68312-716">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="68312-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="68312-717">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="68312-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="68312-718">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="68312-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="68312-719">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="68312-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="68312-720">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="68312-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="68312-721">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="68312-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="68312-722">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="68312-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="68312-723">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="68312-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="68312-724">認証ライブラリとテンプレートでは、 Blazor OPEN ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="68312-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="68312-725">V2.0 エンドポイントを使用するには、JWT ベアラーオプションを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="68312-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="68312-726">次の例では、プロパティにセグメントを追加することによって、バージョン2.0 用に AAD が構成されてい `v2.0` `Authority` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="68312-727">または、アプリ設定 (*appsettings. json*) ファイルで設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="68312-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="68312-728">証明機関へのトラッキングがアプリの OIDC プロバイダー (AAD 以外のプロバイダーを含む) に適していない場合は、プロパティを直接設定し `Authority` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="68312-729">`JwtBearerOptions`またはアプリ設定ファイルのプロパティを、キーを使用して設定し `Authority` ます。</span><span class="sxs-lookup"><span data-stu-id="68312-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="68312-730">V2.0 エンドポイントの ID トークンの変更要求の一覧。</span><span class="sxs-lookup"><span data-stu-id="68312-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="68312-731">詳細については、「 [Microsoft identity platform (v2.0) を更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68312-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
