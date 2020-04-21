<span data-ttu-id="f92e3-101">コンポーネント`FetchData`は、次の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="f92e3-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="f92e3-102">アクセス トークンをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="f92e3-102">Provision an access token.</span></span>
* <span data-ttu-id="f92e3-103">アクセス トークンを使用して、*サーバー*アプリで保護されたリソース API を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f92e3-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="f92e3-104">この`@attribute [Authorize]`ディレクティブは、Blazor WebAssembly 承認システムに対して、このコンポーネントにアクセスするためにユーザーが承認されていることを示します。</span><span class="sxs-lookup"><span data-stu-id="f92e3-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="f92e3-105">*クライアント*アプリに属性が存在しても、サーバー上の API が適切な資格情報なしで呼び出されることを防ぐわけではありません。</span><span class="sxs-lookup"><span data-stu-id="f92e3-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="f92e3-106">*また、サーバー*アプリケーションは`[Authorize]`、適切なエンドポイントで適切なエンドポイントを使用して、適切に保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f92e3-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="f92e3-107">`AuthenticationService.RequestAccessToken();`は、API を呼び出す要求に追加できるアクセス トークンの要求を行います。</span><span class="sxs-lookup"><span data-stu-id="f92e3-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="f92e3-108">トークンがキャッシュされている場合、またはサービスがユーザーの操作なしに新しいアクセス トークンをプロビジョニングできる場合、トークン要求は成功します。</span><span class="sxs-lookup"><span data-stu-id="f92e3-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="f92e3-109">それ以外の場合、トークン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="f92e3-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="f92e3-110">要求に含める実際のトークンを取得するには、アプリは要求が成功したことを確認する必要があります`tokenResult.TryGetToken(out var token)`。</span><span class="sxs-lookup"><span data-stu-id="f92e3-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="f92e3-111">要求が成功した場合、トークン変数にはアクセス トークンが設定されます。</span><span class="sxs-lookup"><span data-stu-id="f92e3-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="f92e3-112">トークン`Value`のプロパティは、要求ヘッダーに含めるリテラル文字列を`Authorization`公開します。</span><span class="sxs-lookup"><span data-stu-id="f92e3-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="f92e3-113">ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f92e3-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="f92e3-114">この URL に移動すると、ユーザーはログイン ページに移動し、認証が成功した後に現在のページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="f92e3-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="f92e3-115">詳細については、「[認証操作の前にアプリの状態を保存する」を](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)参照してください。</span><span class="sxs-lookup"><span data-stu-id="f92e3-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
