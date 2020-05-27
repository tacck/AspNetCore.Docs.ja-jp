<span data-ttu-id="240d8-101">コンポーネントでは、 `FetchData` 次の方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="240d8-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="240d8-102">アクセストークンをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="240d8-102">Provision an access token.</span></span>
* <span data-ttu-id="240d8-103">*サーバー*アプリで保護されたリソース API を呼び出すには、アクセストークンを使用します。</span><span class="sxs-lookup"><span data-stu-id="240d8-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="240d8-104">ディレクティブは、 [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) このコンポーネントにアクセスするためにユーザーを承認する必要があることを、Blazor WebAssembly 認証システムに示します。</span><span class="sxs-lookup"><span data-stu-id="240d8-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="240d8-105">*クライアント*アプリに属性が存在しても、適切な資格情報を使用せずにサーバー上の API が呼び出されるのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="240d8-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="240d8-106">また、*サーバー*アプリは、 `[Authorize]` 適切なエンドポイントでを使用して適切に保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240d8-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="240d8-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>は、API を呼び出すために要求に追加できるアクセストークンを要求します。</span><span class="sxs-lookup"><span data-stu-id="240d8-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="240d8-108">トークンがキャッシュされている場合、またはサービスがユーザーの操作なしで新しいアクセストークンをプロビジョニングできる場合、トークン要求は成功します。</span><span class="sxs-lookup"><span data-stu-id="240d8-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="240d8-109">それ以外の場合、トークン要求は、try-catch <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ステートメントでキャッチさ[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)れたを使用して失敗します。</span><span class="sxs-lookup"><span data-stu-id="240d8-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="240d8-110">要求に含める実際のトークンを取得するには、アプリは[Tokenresult. TryGetToken (out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A)を呼び出すことによって、要求が成功したことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="240d8-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [tokenResult.TryGetToken(out var token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="240d8-111">要求が成功すると、トークン変数にアクセストークンが設定されます。</span><span class="sxs-lookup"><span data-stu-id="240d8-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="240d8-112"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>トークンのプロパティは、要求ヘッダーに含めるリテラル文字列を公開し `Authorization` ます。</span><span class="sxs-lookup"><span data-stu-id="240d8-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="240d8-113">ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。</span><span class="sxs-lookup"><span data-stu-id="240d8-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="240d8-114">この URL に移動すると、認証が成功した後、ユーザーがログインページに移動し、現在のページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="240d8-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
