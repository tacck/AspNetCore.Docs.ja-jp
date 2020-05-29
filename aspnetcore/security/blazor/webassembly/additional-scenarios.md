---
<span data-ttu-id="0168c-101">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-103">'Blazor'</span></span>
- <span data-ttu-id="0168c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-104">'Identity'</span></span>
- <span data-ttu-id="0168c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-106">'Razor'</span></span>
- <span data-ttu-id="0168c-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="0168c-108">Blazor追加のセキュリティシナリオを ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0168c-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="0168c-109">作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0168c-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="0168c-110">送信要求にトークンを添付する</span><span class="sxs-lookup"><span data-stu-id="0168c-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="0168c-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>サービスをと共に使用して、 <xref:System.Net.Http.HttpClient> アクセストークンを送信要求に接続できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="0168c-112">トークンは、既存のサービスを使用して取得され <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="0168c-113">トークンを取得できない場合は、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="0168c-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="0168c-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>には、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0168c-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="0168c-115">は、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成でき <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="0168c-116">次の例では、は <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*) を構成します。</span><span class="sxs-lookup"><span data-stu-id="0168c-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0168c-117">便宜上、アプリの <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ベースアドレスを承認された URL として事前に構成したが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168c-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="0168c-118">認証が有効になったテンプレートでは、 Blazor サーバー API プロジェクトでを使用してを <xref:System.Net.Http.IHttpClientFactory> 設定し <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="0168c-119">前の例でクライアントが作成された場所に <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> <xref:System.Net.Http.HttpClient> は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="0168c-120">構成されたは、 <xref:System.Net.Http.HttpClient> 単純な[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)パターンを使用して承認された要求を行うために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="0168c-121">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="0168c-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="0168c-122">型指定された HttpClient</span><span class="sxs-lookup"><span data-stu-id="0168c-122">Typed HttpClient</span></span>

<span data-ttu-id="0168c-123">1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="0168c-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="0168c-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="0168c-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0168c-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="0168c-126">`FetchData` コンポーネント (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="0168c-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="0168c-127">HttpClient ハンドラーを構成する</span><span class="sxs-lookup"><span data-stu-id="0168c-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="0168c-128">ハンドラーは、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> 送信 HTTP 要求のためにを使用してさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="0168c-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0168c-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="0168c-130">セキュリティで保護された既定のクライアントを使用するアプリ内の認証されていない web API 要求</span><span class="sxs-lookup"><span data-stu-id="0168c-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="0168c-131">通常、 Blazor セキュリティで保護された既定値を使用するアプリの場合、 <xref:System.Net.Http.HttpClient> アプリケーションは、という名前のを構成することによって、認証されていない、または承認されていない web API 要求を作成する <xref:System.Net.Http.HttpClient></span><span class="sxs-lookup"><span data-stu-id="0168c-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="0168c-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0168c-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="0168c-133">前述の登録は、セキュリティで保護された既存の既定の登録に追加され <xref:System.Net.Http.HttpClient> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="0168c-134">コンポーネントは、認証されて <xref:System.Net.Http.HttpClient> <xref:System.Net.Http.IHttpClientFactory> いない要求または未承認の要求を行うために、からを作成します。</span><span class="sxs-lookup"><span data-stu-id="0168c-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="0168c-135">前の例では、サーバー API のコントローラーが `WeatherForecastNoAuthenticationController` 属性でマークされていません [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 。</span><span class="sxs-lookup"><span data-stu-id="0168c-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="0168c-136">追加のアクセス トークンを要求する</span><span class="sxs-lookup"><span data-stu-id="0168c-136">Request additional access tokens</span></span>

<span data-ttu-id="0168c-137">アクセストークンは、を呼び出すことによって手動で取得でき `IAccessTokenProvider.RequestAccessToken` ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="0168c-138">次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。</span><span class="sxs-lookup"><span data-stu-id="0168c-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="0168c-139">Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリで追加のスコープが構成されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="0168c-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0168c-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0168c-141">メソッドは、 `IAccessTokenProvider.RequestToken` アプリが特定のスコープセットを使用してアクセストークンをプロビジョニングできるようにするオーバーロードを提供します。</span><span class="sxs-lookup"><span data-stu-id="0168c-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="0168c-142">コンポーネント内 Razor :</span><span class="sxs-lookup"><span data-stu-id="0168c-142">In a Razor component:</span></span>

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

<span data-ttu-id="0168c-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>型</span><span class="sxs-lookup"><span data-stu-id="0168c-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="0168c-144">`true`を `token` 使用します。</span><span class="sxs-lookup"><span data-stu-id="0168c-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="0168c-145">`false`トークンが取得されない場合は。</span><span class="sxs-lookup"><span data-stu-id="0168c-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="0168c-146">Fetch API 要求オプションを使用する HttpClient と HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="0168c-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="0168c-147">Webassembly で Blazor [httpclient](xref:fundamentals/http-requests)およびを使用して、 <xref:System.Net.Http.HttpRequestMessage> 要求をカスタマイズすることができます。</span><span class="sxs-lookup"><span data-stu-id="0168c-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="0168c-148">たとえば、HTTP メソッドと要求ヘッダーを指定できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="0168c-149">次のコンポーネントは、 `POST` サーバー上の To Do LIST API エンドポイントに対して要求を行い、応答本文を表示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="0168c-150">.NET WebAssembly の <xref:System.Net.Http.HttpClient> の実装には、[WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="0168c-151">フェッチを使用すると、いくつかの[要求固有のオプション](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)を構成できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="0168c-152">HTTP フェッチ要求オプションは、次の表に示す <xref:System.Net.Http.HttpRequestMessage> 拡張メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="0168c-153">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="0168c-153">Extension method</span></span> | <span data-ttu-id="0168c-154">フェッチ要求プロパティ</span><span class="sxs-lookup"><span data-stu-id="0168c-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="0168c-155">credentials</span><span class="sxs-lookup"><span data-stu-id="0168c-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="0168c-156">cache</span><span class="sxs-lookup"><span data-stu-id="0168c-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="0168c-157">モード</span><span class="sxs-lookup"><span data-stu-id="0168c-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="0168c-158">integrity</span><span class="sxs-lookup"><span data-stu-id="0168c-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="0168c-159">より汎用的な <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> 拡張メソッドを使用してその他のオプションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="0168c-160">通常、HTTP 応答は、応答コンテンツに対する同期読み取りのサポートを有効にするために Blazor WebAssembly アプリでバッファリングされます。</span><span class="sxs-lookup"><span data-stu-id="0168c-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="0168c-161">応答ストリーミングのサポートを有効にするには、要求に対して <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="0168c-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="0168c-162">クロスオリジン要求に資格情報を含めるには、<xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="0168c-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="0168c-163">Fetch API オプションの詳細については、[MDN の Web ドキュメント、WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0168c-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="0168c-164">CORS 要求で資格情報 (承認 cookie/ヘッダー) を送信するときには、CORS ポリシーで `Authorization` ヘッダーが許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="0168c-165">次のポリシーには、以下についての構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168c-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="0168c-166">要求元 (`http://localhost:5000`、`https://localhost:5001`)。</span><span class="sxs-lookup"><span data-stu-id="0168c-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="0168c-167">任意のメソッド (動詞)。</span><span class="sxs-lookup"><span data-stu-id="0168c-167">Any method (verb).</span></span>
* <span data-ttu-id="0168c-168">`Content-Type` ヘッダーと `Authorization` ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="0168c-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="0168c-169">カスタム ヘッダー (`x-custom-header`など) を許可するには、<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> の呼び出し時にヘッダーを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="0168c-170">クライアント側の JavaScript コードによって設定された資格情報 (`credentials` プロパティが `include`に設定されています)。</span><span class="sxs-lookup"><span data-stu-id="0168c-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="0168c-171">詳細については、「<xref:security/cors>」と、サンプル アプリの HTTP 要求テスター コンポーネント (*Components/HTTPRequestTester.razor*) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0168c-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="0168c-172">トークン要求エラーを処理する</span><span class="sxs-lookup"><span data-stu-id="0168c-172">Handle token request errors</span></span>

<span data-ttu-id="0168c-173">シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証する場合、認証状態は SPA 内およびプロバイダー (IP) 内でローカルに保持され、 Identity ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。</span><span class="sxs-lookup"><span data-stu-id="0168c-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="0168c-174">通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="0168c-175">そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。</span><span class="sxs-lookup"><span data-stu-id="0168c-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="0168c-176">ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。</span><span class="sxs-lookup"><span data-stu-id="0168c-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="0168c-177">場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。</span><span class="sxs-lookup"><span data-stu-id="0168c-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="0168c-178">このシナリオは、ユーザーがアクセスしてログアウトした場合に発生し `https://login.microsoftonline.com` ます。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="0168c-179">また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0168c-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="0168c-180">これらのシナリオは、トークンベースの認証に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="0168c-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="0168c-181">これらは、SPAs の性質の一部です。</span><span class="sxs-lookup"><span data-stu-id="0168c-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="0168c-182">認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="0168c-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="0168c-183">アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="0168c-184">API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="0168c-185">クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="0168c-186">アプリがトークンを要求すると、次の2つの結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="0168c-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="0168c-187">要求が成功し、アプリに有効なトークンがあります。</span><span class="sxs-lookup"><span data-stu-id="0168c-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="0168c-188">要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="0168c-189">トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="0168c-190">次のようないくつかの方法があり、複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="0168c-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="0168c-191">現在のページの状態をセッションストレージに格納します。</span><span class="sxs-lookup"><span data-stu-id="0168c-191">Store the current page state in session storage.</span></span> <span data-ttu-id="0168c-192">[Oninitializer Edasync ライフサイクルイベント](xref:blazor/lifecycle#component-initialization-methods)() 中に <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 、続行する前に状態を復元できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0168c-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="0168c-193">クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。</span><span class="sxs-lookup"><span data-stu-id="0168c-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="0168c-194">セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。</span><span class="sxs-lookup"><span data-stu-id="0168c-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="0168c-195">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="0168c-195">The following example shows how to:</span></span>

* <span data-ttu-id="0168c-196">ログインページにリダイレクトする前に状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="0168c-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="0168c-197">クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。</span><span class="sxs-lookup"><span data-stu-id="0168c-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="0168c-198">認証操作の前にアプリの状態を保存する</span><span class="sxs-lookup"><span data-stu-id="0168c-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="0168c-199">認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="0168c-200">これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="0168c-201">カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="0168c-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="0168c-202">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="0168c-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="0168c-203">アプリルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168c-203">Customize app routes</span></span>

<span data-ttu-id="0168c-204">既定では、 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="0168c-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="0168c-205">ルート</span><span class="sxs-lookup"><span data-stu-id="0168c-205">Route</span></span>                            | <span data-ttu-id="0168c-206">目的</span><span class="sxs-lookup"><span data-stu-id="0168c-206">Purpose</span></span> |
| ---
<span data-ttu-id="0168c-207">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-209">'Blazor'</span></span>
- <span data-ttu-id="0168c-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-210">'Identity'</span></span>
- <span data-ttu-id="0168c-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-212">'Razor'</span></span>
- <span data-ttu-id="0168c-213">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-214">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-216">'Blazor'</span></span>
- <span data-ttu-id="0168c-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-217">'Identity'</span></span>
- <span data-ttu-id="0168c-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-219">'Razor'</span></span>
- <span data-ttu-id="0168c-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-221">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-223">'Blazor'</span></span>
- <span data-ttu-id="0168c-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-224">'Identity'</span></span>
- <span data-ttu-id="0168c-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-226">'Razor'</span></span>
- <span data-ttu-id="0168c-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-228">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-230">'Blazor'</span></span>
- <span data-ttu-id="0168c-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-231">'Identity'</span></span>
- <span data-ttu-id="0168c-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-233">'Razor'</span></span>
- <span data-ttu-id="0168c-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-235">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-237">'Blazor'</span></span>
- <span data-ttu-id="0168c-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-238">'Identity'</span></span>
- <span data-ttu-id="0168c-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-240">'Razor'</span></span>
- <span data-ttu-id="0168c-241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-242">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-244">'Blazor'</span></span>
- <span data-ttu-id="0168c-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-245">'Identity'</span></span>
- <span data-ttu-id="0168c-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-247">'Razor'</span></span>
- <span data-ttu-id="0168c-248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-249">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-251">'Blazor'</span></span>
- <span data-ttu-id="0168c-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-252">'Identity'</span></span>
- <span data-ttu-id="0168c-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-254">'Razor'</span></span>
- <span data-ttu-id="0168c-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-256">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-258">'Blazor'</span></span>
- <span data-ttu-id="0168c-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-259">'Identity'</span></span>
- <span data-ttu-id="0168c-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-261">'Razor'</span></span>
- <span data-ttu-id="0168c-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-263">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-265">'Blazor'</span></span>
- <span data-ttu-id="0168c-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-266">'Identity'</span></span>
- <span data-ttu-id="0168c-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-268">'Razor'</span></span>
- <span data-ttu-id="0168c-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-270">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-272">'Blazor'</span></span>
- <span data-ttu-id="0168c-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-273">'Identity'</span></span>
- <span data-ttu-id="0168c-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-275">'Razor'</span></span>
- <span data-ttu-id="0168c-276">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-277">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-279">'Blazor'</span></span>
- <span data-ttu-id="0168c-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-280">'Identity'</span></span>
- <span data-ttu-id="0168c-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-282">'Razor'</span></span>
- <span data-ttu-id="0168c-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-284">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-286">'Blazor'</span></span>
- <span data-ttu-id="0168c-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-287">'Identity'</span></span>
- <span data-ttu-id="0168c-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-289">'Razor'</span></span>
- <span data-ttu-id="0168c-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-291">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-293">'Blazor'</span></span>
- <span data-ttu-id="0168c-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-294">'Identity'</span></span>
- <span data-ttu-id="0168c-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-296">'Razor'</span></span>
- <span data-ttu-id="0168c-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-298">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-300">'Blazor'</span></span>
- <span data-ttu-id="0168c-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-301">'Identity'</span></span>
- <span data-ttu-id="0168c-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-303">'Razor'</span></span>
- <span data-ttu-id="0168c-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-304">'SignalR' uid:</span></span> 

<span data-ttu-id="0168c-305">---------------- |---タイトル: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-307">'Blazor'</span></span>
- <span data-ttu-id="0168c-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-308">'Identity'</span></span>
- <span data-ttu-id="0168c-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-310">'Razor'</span></span>
- <span data-ttu-id="0168c-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-311">'SignalR' uid:</span></span> 

<span data-ttu-id="0168c-312">---- | |`authentication/login`           |サインイン操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0168c-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="0168c-313">| |`authentication/login-callback`  |サインイン操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="0168c-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="0168c-314">| |`authentication/login-failed`    |何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="0168c-315">| |`authentication/logout`          |サインアウト操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0168c-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="0168c-316">| |`authentication/logout-callback` |サインアウト操作の結果を処理します。</span><span class="sxs-lookup"><span data-stu-id="0168c-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="0168c-317">| |`authentication/logout-failed`   |何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="0168c-318">| |`authentication/logged-out`      |ユーザーが正常にログアウトしたことを示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="0168c-319">| |`authentication/profile`         |ユーザープロファイルを編集する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0168c-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="0168c-320">| |`authentication/register`        |新しいユーザーを登録する操作をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="0168c-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="0168c-321">上の表に示されているルートは、を使用して構成 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0168c-322">カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0168c-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="0168c-323">次の例では、すべてのパスの先頭にが付き `/security` ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="0168c-324">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="0168c-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="0168c-325">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0168c-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0168c-326">完全に異なるパスを必要とする場合は、前述のようにルートを設定し、 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> 明示的なアクションパラメーターを使用してを表示します。</span><span class="sxs-lookup"><span data-stu-id="0168c-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="0168c-327">UI を別のページに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="0168c-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="0168c-328">認証ユーザーインターフェイスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168c-328">Customize the authentication user interface</span></span>

<span data-ttu-id="0168c-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>には、各認証状態の UI 部分の既定のセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0168c-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="0168c-330">各状態は、カスタムを渡すことによってカスタマイズでき <xref:Microsoft.AspNetCore.Components.RenderFragment> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="0168c-331">初期ログインプロセス中に表示されるテキストをカスタマイズするには、次のようにを変更し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="0168c-332">`Authentication`コンポーネント (*Pages/Authentication. razor*):</span><span class="sxs-lookup"><span data-stu-id="0168c-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="0168c-333">に <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。</span><span class="sxs-lookup"><span data-stu-id="0168c-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="0168c-334">ルート</span><span class="sxs-lookup"><span data-stu-id="0168c-334">Route</span></span>                            | <span data-ttu-id="0168c-335">フラグメント</span><span class="sxs-lookup"><span data-stu-id="0168c-335">Fragment</span></span>                |
| ---
<span data-ttu-id="0168c-336">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-338">'Blazor'</span></span>
- <span data-ttu-id="0168c-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-339">'Identity'</span></span>
- <span data-ttu-id="0168c-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-341">'Razor'</span></span>
- <span data-ttu-id="0168c-342">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-343">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-345">'Blazor'</span></span>
- <span data-ttu-id="0168c-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-346">'Identity'</span></span>
- <span data-ttu-id="0168c-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-348">'Razor'</span></span>
- <span data-ttu-id="0168c-349">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-350">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-352">'Blazor'</span></span>
- <span data-ttu-id="0168c-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-353">'Identity'</span></span>
- <span data-ttu-id="0168c-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-355">'Razor'</span></span>
- <span data-ttu-id="0168c-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-357">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-359">'Blazor'</span></span>
- <span data-ttu-id="0168c-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-360">'Identity'</span></span>
- <span data-ttu-id="0168c-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-362">'Razor'</span></span>
- <span data-ttu-id="0168c-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-364">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-366">'Blazor'</span></span>
- <span data-ttu-id="0168c-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-367">'Identity'</span></span>
- <span data-ttu-id="0168c-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-369">'Razor'</span></span>
- <span data-ttu-id="0168c-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-371">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-373">'Blazor'</span></span>
- <span data-ttu-id="0168c-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-374">'Identity'</span></span>
- <span data-ttu-id="0168c-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-376">'Razor'</span></span>
- <span data-ttu-id="0168c-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-378">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-380">'Blazor'</span></span>
- <span data-ttu-id="0168c-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-381">'Identity'</span></span>
- <span data-ttu-id="0168c-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-383">'Razor'</span></span>
- <span data-ttu-id="0168c-384">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-385">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-387">'Blazor'</span></span>
- <span data-ttu-id="0168c-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-388">'Identity'</span></span>
- <span data-ttu-id="0168c-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-390">'Razor'</span></span>
- <span data-ttu-id="0168c-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-392">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-394">'Blazor'</span></span>
- <span data-ttu-id="0168c-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-395">'Identity'</span></span>
- <span data-ttu-id="0168c-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-397">'Razor'</span></span>
- <span data-ttu-id="0168c-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-399">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-401">'Blazor'</span></span>
- <span data-ttu-id="0168c-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-402">'Identity'</span></span>
- <span data-ttu-id="0168c-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-404">'Razor'</span></span>
- <span data-ttu-id="0168c-405">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-406">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-408">'Blazor'</span></span>
- <span data-ttu-id="0168c-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-409">'Identity'</span></span>
- <span data-ttu-id="0168c-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-411">'Razor'</span></span>
- <span data-ttu-id="0168c-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-413">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-415">'Blazor'</span></span>
- <span data-ttu-id="0168c-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-416">'Identity'</span></span>
- <span data-ttu-id="0168c-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-418">'Razor'</span></span>
- <span data-ttu-id="0168c-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-420">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-422">'Blazor'</span></span>
- <span data-ttu-id="0168c-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-423">'Identity'</span></span>
- <span data-ttu-id="0168c-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-425">'Razor'</span></span>
- <span data-ttu-id="0168c-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-427">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-429">'Blazor'</span></span>
- <span data-ttu-id="0168c-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-430">'Identity'</span></span>
- <span data-ttu-id="0168c-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-432">'Razor'</span></span>
- <span data-ttu-id="0168c-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-433">'SignalR' uid:</span></span> 

<span data-ttu-id="0168c-434">---------------- |---タイトル: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-436">'Blazor'</span></span>
- <span data-ttu-id="0168c-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-437">'Identity'</span></span>
- <span data-ttu-id="0168c-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-439">'Razor'</span></span>
- <span data-ttu-id="0168c-440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-441">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-443">'Blazor'</span></span>
- <span data-ttu-id="0168c-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-444">'Identity'</span></span>
- <span data-ttu-id="0168c-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-446">'Razor'</span></span>
- <span data-ttu-id="0168c-447">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-448">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-450">'Blazor'</span></span>
- <span data-ttu-id="0168c-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-451">'Identity'</span></span>
- <span data-ttu-id="0168c-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-453">'Razor'</span></span>
- <span data-ttu-id="0168c-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-455">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-457">'Blazor'</span></span>
- <span data-ttu-id="0168c-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-458">'Identity'</span></span>
- <span data-ttu-id="0168c-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-460">'Razor'</span></span>
- <span data-ttu-id="0168c-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-462">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-464">'Blazor'</span></span>
- <span data-ttu-id="0168c-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-465">'Identity'</span></span>
- <span data-ttu-id="0168c-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-467">'Razor'</span></span>
- <span data-ttu-id="0168c-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-469">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-471">'Blazor'</span></span>
- <span data-ttu-id="0168c-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-472">'Identity'</span></span>
- <span data-ttu-id="0168c-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-474">'Razor'</span></span>
- <span data-ttu-id="0168c-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-476">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-478">'Blazor'</span></span>
- <span data-ttu-id="0168c-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-479">'Identity'</span></span>
- <span data-ttu-id="0168c-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-481">'Razor'</span></span>
- <span data-ttu-id="0168c-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-483">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-485">'Blazor'</span></span>
- <span data-ttu-id="0168c-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-486">'Identity'</span></span>
- <span data-ttu-id="0168c-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-488">'Razor'</span></span>
- <span data-ttu-id="0168c-489">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0168c-490">title: ' ASP.NET Core の Blazor 追加のセキュリティシナリオの作成者: 説明: ' Blazor セキュリティシナリオを追加するために webassembly 構成する方法について説明します。 "</span><span class="sxs-lookup"><span data-stu-id="0168c-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="0168c-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0168c-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0168c-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0168c-492">'Blazor'</span></span>
- <span data-ttu-id="0168c-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0168c-493">'Identity'</span></span>
- <span data-ttu-id="0168c-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0168c-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="0168c-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0168c-495">'Razor'</span></span>
- <span data-ttu-id="0168c-496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0168c-496">'SignalR' uid:</span></span> 

<span data-ttu-id="0168c-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="0168c-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="0168c-498">ユーザーをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="0168c-498">Customize the user</span></span>

<span data-ttu-id="0168c-499">アプリにバインドされているユーザーをカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="0168c-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="0168c-500">次の例では、すべての認証されたユーザーが、 `amr` ユーザーの認証方法ごとに要求を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="0168c-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="0168c-501">クラスを拡張するクラスを作成し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="0168c-502">以下を拡張するファクトリを作成し <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="0168c-503">`CustomAccountFactory`使用中の認証プロバイダーのを登録します。</span><span class="sxs-lookup"><span data-stu-id="0168c-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="0168c-504">次の登録のいずれかが有効です。</span><span class="sxs-lookup"><span data-stu-id="0168c-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="0168c-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="0168c-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="0168c-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="0168c-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="0168c-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="0168c-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="0168c-508">認証を使用したプリレンダリングのサポート</span><span class="sxs-lookup"><span data-stu-id="0168c-508">Support prerendering with authentication</span></span>

<span data-ttu-id="0168c-509">ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="0168c-510">承認が不要なパスをプリレンダリングする。</span><span class="sxs-lookup"><span data-stu-id="0168c-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="0168c-511">承認が必要なパスをプリレンダリングしない。</span><span class="sxs-lookup"><span data-stu-id="0168c-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="0168c-512">クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。</span><span class="sxs-lookup"><span data-stu-id="0168c-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="0168c-513">サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="0168c-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="0168c-514">サーバーアプリのメソッドで `Startup.Configure` 、[エンドポイントを置き換えます。エンドポイントを含む MapFallbackToFile ("index .html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) [。MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="0168c-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="0168c-515">サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。</span><span class="sxs-lookup"><span data-stu-id="0168c-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="0168c-516">サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="0168c-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="0168c-517">クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0168c-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="0168c-518">ファイルの内容を更新します。</span><span class="sxs-lookup"><span data-stu-id="0168c-518">Update the file's contents:</span></span>

* <span data-ttu-id="0168c-519">ファイルの先頭に、`@page "_Host"` を追加します。</span><span class="sxs-lookup"><span data-stu-id="0168c-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="0168c-520">`<app>Loading...</app>` タグを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0168c-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="0168c-521">ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション</span><span class="sxs-lookup"><span data-stu-id="0168c-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="0168c-522">ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0168c-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="0168c-523">どれを選択するかは、シナリオによって異なります。</span><span class="sxs-lookup"><span data-stu-id="0168c-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="0168c-524">詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0168c-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="0168c-525">ユーザーを認証して保護されたサードパーティ API のみを呼び出す</span><span class="sxs-lookup"><span data-stu-id="0168c-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="0168c-526">サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。</span><span class="sxs-lookup"><span data-stu-id="0168c-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="0168c-527">このシナリオでは:</span><span class="sxs-lookup"><span data-stu-id="0168c-527">In this scenario:</span></span>

* <span data-ttu-id="0168c-528">アプリをホストしているサーバーは関与しません。</span><span class="sxs-lookup"><span data-stu-id="0168c-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="0168c-529">サーバー上の API を保護することはできません。</span><span class="sxs-lookup"><span data-stu-id="0168c-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="0168c-530">アプリでは、保護されたサードパーティ API のみを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="0168c-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="0168c-531">サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す</span><span class="sxs-lookup"><span data-stu-id="0168c-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="0168c-532">Identityサードパーティのログインプロバイダーを使用してを構成します。</span><span class="sxs-lookup"><span data-stu-id="0168c-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="0168c-533">サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。</span><span class="sxs-lookup"><span data-stu-id="0168c-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="0168c-534">ユーザーがログインすると、は Identity 認証プロセスの一環としてアクセストークンと更新トークンを収集します。</span><span class="sxs-lookup"><span data-stu-id="0168c-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="0168c-535">その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="0168c-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="0168c-536">サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する</span><span class="sxs-lookup"><span data-stu-id="0168c-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="0168c-537">サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。</span><span class="sxs-lookup"><span data-stu-id="0168c-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="0168c-538">そこから、サードパーティのアクセストークンを使用して、クライアント側から直接サードパーティの API リソースを呼び出し Identity ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="0168c-539">この方法はお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="0168c-539">We don't recommend this approach.</span></span> <span data-ttu-id="0168c-540">この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="0168c-541">OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="0168c-542">機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。</span><span class="sxs-lookup"><span data-stu-id="0168c-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="0168c-543">サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。</span><span class="sxs-lookup"><span data-stu-id="0168c-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="0168c-544">同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="0168c-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="0168c-545">サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="0168c-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="0168c-546">クライアントからサーバー API への API 呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="0168c-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="0168c-547">サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。</span><span class="sxs-lookup"><span data-stu-id="0168c-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="0168c-548">この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="0168c-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="0168c-549">サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0168c-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="0168c-550">アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。</span><span class="sxs-lookup"><span data-stu-id="0168c-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="0168c-551">Open ID Connect (OIDC) v2.0 エンドポイントを使用する</span><span class="sxs-lookup"><span data-stu-id="0168c-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="0168c-552">認証ライブラリとテンプレートでは、 Blazor OPEN ID Connect (OIDC) v1.0 エンドポイントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0168c-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="0168c-553">V2.0 エンドポイントを使用するには、JWT ベアラーオプションを構成し <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="0168c-554">次の例では、プロパティにセグメントを追加することによって、バージョン2.0 用に AAD が構成されてい `v2.0` <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="0168c-555">または、アプリ設定 (*appsettings. json*) ファイルで設定を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="0168c-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="0168c-556">証明機関へのトラッキングがアプリの OIDC プロバイダー (AAD 以外のプロバイダーを含む) に適していない場合は、プロパティを直接設定し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="0168c-557"><xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>キーを使用して、またはアプリ設定ファイル (*appsettings. json*) のプロパティを設定し `Authority` ます。</span><span class="sxs-lookup"><span data-stu-id="0168c-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="0168c-558">V2.0 エンドポイントの ID トークンの変更要求の一覧。</span><span class="sxs-lookup"><span data-stu-id="0168c-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="0168c-559">詳細については、「 [Microsoft identity platform (v2.0) を更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0168c-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
