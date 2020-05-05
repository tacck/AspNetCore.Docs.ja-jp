コンポーネント`FetchData`では、次の方法を示しています。

* アクセストークンをプロビジョニングします。
* *サーバー*アプリで保護されたリソース API を呼び出すには、アクセストークンを使用します。

ディレクティブ`@attribute [Authorize]`は、このコンポーネントにアクセスするためにユーザーを承認する必要があることを、Blazor WebAssembly 認証システムに示します。 *クライアント*アプリに属性が存在しても、適切な資格情報を使用せずにサーバー上の API が呼び出されるのを防ぐことはできません。 また、*サーバー*アプリは、 `[Authorize]`適切なエンドポイントでを使用して適切に保護する必要があります。

`IAccessTokenProvider.RequestAccessToken();`は、API を呼び出すために要求に追加できるアクセストークンを要求します。 トークンがキャッシュされている場合、またはサービスがユーザーの操作なしで新しいアクセストークンをプロビジョニングできる場合、トークン要求は成功します。 それ以外の場合、トークン要求は`AccessTokenNotAvailableException`失敗し、エラーが発生し`try-catch`ます。これは、ステートメントでキャッチされます。

要求に含める実際のトークンを取得するために、アプリはを呼び出し`tokenResult.TryGetToken(out var token)`て要求が成功したことを確認する必要があります。 

要求が成功すると、トークン変数にアクセストークンが設定されます。 トークン`Value`のプロパティは、 `Authorization`要求ヘッダーに含めるリテラル文字列を公開します。

ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。 この URL に移動すると、認証が成功した後、ユーザーがログインページに移動し、現在のページに戻ります。

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
