コンポーネント`FetchData`は、次の方法を示しています。

* アクセス トークンをプロビジョニングします。
* アクセス トークンを使用して、*サーバー*アプリで保護されたリソース API を呼び出します。

この`@attribute [Authorize]`ディレクティブは、Blazor WebAssembly 承認システムに対して、このコンポーネントにアクセスするためにユーザーが承認されていることを示します。 *クライアント*アプリに属性が存在しても、サーバー上の API が適切な資格情報なしで呼び出されることを防ぐわけではありません。 *また、サーバー*アプリケーションは`[Authorize]`、適切なエンドポイントで適切なエンドポイントを使用して、適切に保護する必要があります。

`AuthenticationService.RequestAccessToken();`は、API を呼び出す要求に追加できるアクセス トークンの要求を行います。 トークンがキャッシュされている場合、またはサービスがユーザーの操作なしに新しいアクセス トークンをプロビジョニングできる場合、トークン要求は成功します。 それ以外の場合、トークン要求は失敗します。

要求に含める実際のトークンを取得するには、アプリは要求が成功したことを確認する必要があります`tokenResult.TryGetToken(out var token)`。 

要求が成功した場合、トークン変数にはアクセス トークンが設定されます。 トークン`Value`のプロパティは、要求ヘッダーに含めるリテラル文字列を`Authorization`公開します。

ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。 この URL に移動すると、ユーザーはログイン ページに移動し、認証が成功した後に現在のページに戻ります。

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

詳細については、「[認証操作の前にアプリの状態を保存する」を](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)参照してください。
