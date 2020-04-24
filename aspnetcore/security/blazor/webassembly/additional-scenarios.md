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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor Webasの追加のセキュリティシナリオ

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> この記事のガイダンスは、ASP.NET Core 3.2 Preview 4 に適用されます。 このトピックは、4月24日金曜日の Preview 5 に対応するよう更新されます。

## <a name="request-additional-access-tokens"></a>追加のアクセストークンを要求する

ほとんどのアプリでは、使用する保護されたリソースと対話するためのアクセストークンのみが必要です。 場合によっては、アプリで2つ以上のリソースを操作するために複数のトークンが必要になることがあります。

次の例では、アプリがユーザーデータの読み取りとメールの送信を行うために、追加の Azure Active Directory (AAD) Microsoft Graph API スコープが必要です。 Azure AAD ポータルで Microsoft Graph API のアクセス許可を追加すると、クライアントアプリ (`Program.Main`、 *Program.cs*) で追加のスコープが構成されます。

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

メソッド`IAccessTokenProvider.RequestToken`は、次の例に示すように、アプリが特定のスコープセットを使用してトークンをプロビジョニングできるようにするオーバーロードを提供します。

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

`TryGetToken`型

* `true``token`を使用します。
* `false`トークンが取得されない場合は。

## <a name="attach-tokens-to-outgoing-requests"></a>送信要求にトークンを添付する

サービス`AuthorizationMessageHandler`をと共に使用`HttpClient`して、アクセストークンを送信要求に接続できます。 トークンは、既存`IAccessTokenProvider`のサービスを使用して取得されます。 トークンを取得できない場合`AccessTokenNotAvailableException`は、がスローされます。 `AccessTokenNotAvailableException`には`Redirect` 、ユーザーを id プロバイダーに移動して新しいトークンを取得するために使用できるメソッドが用意されています。 は`AuthorizationMessageHandler` 、 `ConfigureHandler`メソッドを使用して、承認された url、スコープ、およびリターン url を使用して構成できます。

次の例では`AuthorizationMessageHandler` 、は`HttpClient` in `Program.Main` (*Program.cs*) を構成します。

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

便宜上、アプリの`BaseAddressAuthorizationMessageHandler`ベースアドレスを承認された URL として事前に構成したが含まれています。 認証が有効な Blazor WebAssembly テンプレートでは、 `BaseAddressAuthorizationMessageHandler`次のように`HttpClient` [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests)を使用してを設定するようになりました。

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

前の例`CreateClient`でクライアントが作成された場所に`HttpClient`は、サーバープロジェクトへの要求を行うときにアクセストークンを含むインスタンスが提供されます。

構成`HttpClient`されたは、単純な`try-catch`パターンを使用して承認された要求を行うために使用されます。 次`FetchData`のコンポーネントは、天気予報データを要求します。

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

または、1つのクラス内のすべての HTTP およびトークンの取得に関する問題を処理する、型指定されたクライアントを定義することもできます。

*WeatherClient.cs*:

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

*Program.cs*:

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*Fetchdata。 razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a>トークン要求エラーを処理する

シングルページアプリケーション (SPA) が Open ID Connect (OIDC) を使用してユーザーを認証すると、認証状態は SPA 内および Id プロバイダー (IP) 内でローカルに保持され、ユーザーが資格情報を入力したときに設定されるセッション cookie の形式になります。

通常、ユーザーに対して IP が生成するトークンは短時間、通常は1時間にわたって有効であるため、クライアントアプリは定期的に新しいトークンを取得する必要があります。 そうしないと、許可されたトークンの有効期限が切れると、ユーザーはログアウトされます。 ほとんどの場合、OIDC クライアントは、認証状態または IP 内に保持される "セッション" によってユーザーの認証を再度要求することなく、新しいトークンをプロビジョニングできます。

場合によっては、ユーザーの介入なしにクライアントがトークンを取得できないことがあります。たとえば、何らかの理由でユーザーが明示的に IP からログアウトした場合などです。 このシナリオは、ユーザーがアクセス`https://login.microsoftonline.com`してログアウトした場合に発生します。これらのシナリオでは、アプリはユーザーがログアウトしたことをすぐに認識できません。クライアントが保持するトークンは、有効でなくなった可能性があります。 また、クライアントは、現在のトークンの有効期限が切れた後に、ユーザーの介入なしに新しいトークンをプロビジョニングすることはできません。

これらのシナリオは、トークンベースの認証に固有のものではありません。 これらは、SPAs の性質の一部です。 認証クッキーが削除されると、cookie を使用する SPA もサーバー API を呼び出すことができません。

アプリが保護されたリソースに対する API 呼び出しを実行するときは、次の点に注意する必要があります。

* API を呼び出すための新しいアクセストークンをプロビジョニングするには、ユーザーが再度認証される必要があります。
* クライアントに有効なトークンがある場合でも、トークンがユーザーによって取り消されたために、サーバーへの呼び出しが失敗する可能性があります。

アプリがトークンを要求すると、次の2つの結果が得られます。

* 要求が成功し、アプリに有効なトークンがあります。
* 要求は失敗します。アプリは、新しいトークンを取得するために、ユーザーを再度認証する必要があります。

トークン要求が失敗した場合は、リダイレクトを実行する前に、現在の状態を保存するかどうかを決定する必要があります。 次のようないくつかの方法があり、複雑さが増します。

* 現在のページの状態をセッションストレージに格納します。 の`OnInitializeAsync`間、続行する前に状態を復元できるかどうかを確認します。
* クエリ文字列パラメーターを追加し、それを使用して、以前に保存した状態を再ハイドレートする必要があることをアプリに通知する方法として使用します。
* セッションストレージにデータを格納するための一意の識別子を持つクエリ文字列パラメーターを追加します。他の項目と競合するリスクはありません。

以下の例では、次のことを行っています。

* ログインページにリダイレクトする前に状態を保持します。
* クエリ文字列パラメーターを使用して、認証後に以前の状態を回復します。

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

## <a name="save-app-state-before-an-authentication-operation"></a>認証操作の前にアプリの状態を保存する

認証操作中に、ブラウザーが IP にリダイレクトされる前に、アプリの状態を保存することが必要になる場合があります。 これは、状態コンテナーのようなものを使用していて、認証が成功した後に状態を復元する場合に発生する可能性があります。 カスタム認証状態オブジェクトを使用して、アプリ固有の状態またはその参照を保持し、認証操作が正常に完了したらその状態を復元することができます。

`Authentication`コンポーネント (*Pages/Authentication. razor*):

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

## <a name="customize-app-routes"></a>アプリルートをカスタマイズする

既定では、 `Microsoft.AspNetCore.Components.WebAssembly.Authentication`ライブラリは、さまざまな認証状態を表すために、次の表に示すルートを使用します。

| ルート                            | 目的 |
| -------------------------------- | ------- |
| `authentication/login`           | サインイン操作をトリガーします。 |
| `authentication/login-callback`  | サインイン操作の結果を処理します。 |
| `authentication/login-failed`    | 何らかの理由でサインイン操作が失敗した場合に、エラーメッセージを表示します。 |
| `authentication/logout`          | サインアウト操作をトリガーします。 |
| `authentication/logout-callback` | サインアウト操作の結果を処理します。 |
| `authentication/logout-failed`   | 何らかの理由でサインアウト操作が失敗した場合に、エラーメッセージを表示します。 |
| `authentication/logged-out`      | ユーザーが正常にログアウトしたことを示します。 |
| `authentication/profile`         | ユーザープロファイルを編集する操作をトリガーします。 |
| `authentication/register`        | 新しいユーザーを登録する操作をトリガーします。 |

上の表に示されているルートは`RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`、を使用して構成できます。 カスタムルートを提供するオプションを設定する場合は、アプリに各パスを処理するルートがあることを確認します。

次の例では、すべてのパスの先頭`/security`にが付きます。

`Authentication`コンポーネント (*Pages/Authentication. razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

完全に異なるパスを必要とする場合は、前述のようにルートを設定`RemoteAuthenticatorView`し、明示的なアクションパラメーターを使用してを表示します。

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

UI を別のページに分割することもできます。

## <a name="customize-the-authentication-user-interface"></a>認証ユーザーインターフェイスをカスタマイズする

`RemoteAuthenticatorView`には、各認証状態の UI 部分の既定のセットが含まれています。 各状態は、カスタム`RenderFragment`を渡すことによってカスタマイズできます。 初期ログインプロセス中に表示されるテキストをカスタマイズするには`RemoteAuthenticatorView` 、次のようにを変更します。

`Authentication`コンポーネント (*Pages/Authentication. razor*):

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

に`RemoteAuthenticatorView`は、次の表に示す認証ルートごとに使用できる1つのフラグメントがあります。

| ルート                            | フラグメント                |
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

## <a name="support-prerendering-with-authentication"></a>認証を使用したプリレンダリングのサポート

ホストされている Blazor WebAssembly アプリのトピックのいずれかのガイダンスを実行した後は、この後の手順に従って次のようなアプリを作成できます。

* 承認が不要なパスをプリレンダリングする。
* 承認が必要なパスをプリレンダリングしない。

クライアント アプリの `Program` クラス (*Program.cs*) で、共通のサービスの登録を別のメソッド (たとえば、`ConfigureCommonServices`) に組み入れます。

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

サーバー アプリの `Startup.ConfigureServices` で、次の追加サービスを登録します。

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

サーバー アプリの `Startup.Configure` メソッドで、`endpoints.MapFallbackToFile("index.html")` を `endpoints.MapFallbackToPage("/_Host")` に置き換えます。

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

サーバー アプリで、*Pages* フォルダーが存在しない場合は作成します。 サーバー アプリの *Pages* フォルダー内に *_Host.cshtml* ページを作成します。 クライアント アプリの *wwwroot/index.html* ファイルの内容を *Pages/_Host.cshtml* ファイル内に貼り付けます。 ファイルの内容を更新します。

* ファイルの先頭に、`@page "_Host"` を追加します。
* `<app>Loading...</app>` タグを次のように置き換えます。

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>ホストされているアプリおよびサードパーティ ログイン プロバイダーに関するオプション

ホストされている Blazor WebAssembly アプリをサードパーティ プロバイダーで認証および承認する場合、ユーザーの認証にはいくつかのオプションを使用できます。 どれを選択するかは、シナリオによって異なります。

詳細については、「<xref:security/authentication/social/additional-claims>」を参照してください。

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>ユーザーを認証して保護されたサードパーティ API のみを呼び出す

サードパーティ API プロバイダーに対してクライアント側の OAuth フローを使用してユーザーを認証します。

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 このシナリオでは:

* アプリをホストしているサーバーは関与しません。
* サーバー上の API を保護することはできません。
* アプリでは、保護されたサードパーティ API のみを呼び出すことができます。

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>サードパーティ プロバイダーでユーザーを認証し、ホスト サーバーおよびサード パーティ上で保護された API を呼び出す

サードパーティ ログイン プロバイダーで ID を構成します。 サードパーティ API へのアクセスに必要なトークンを取得し、それを格納します。

ユーザーがログインすると、認証プロセスの一環としてアクセス トークンと更新トークンが ID によって収集されます。 その時点で、サードパーティ API の API 呼び出しを行うために使用できる方法はいくつかあります。

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>サーバー アクセス トークンを使用してサードパーティのアクセス トークンを取得する

サーバー上で生成されたアクセス トークンを使用して、サーバー API エンドポイントからサードパーティのアクセストークンを取得します。 そこから、サードパーティのアクセス トークンを使用して、クライアント上の ID から直接、サードパーティ API リソースを呼び出します。

この方法はお勧めしません。 この方法では、サードパーティのアクセス トークンをパブリック クライアント用に生成されたものとして扱う必要があります。 OAuth 規約では、パブリック アプリにはクライアント シークレットがありません。これはシークレットを安全に格納することが信頼できないためです。アクセス トークンは機密クライアントに対して生成されます。 機密クライアントとは、クライアント シークレットを持っていてシークレットを安全に格納できると想定されるクライアントです。

* サードパーティのアクセス トークンには、サードパーティがより信頼できるクライアントのトークンを生成したという事実に基づいて機密性の高い操作を実行するための追加のスコープが付与される場合があります。
* 同様に、信頼されていないクライアントに更新トークンを発行してはなりません。それを行ってしまうと、他の制限が適用されない限り、クライアントは無制限にアクセスできます。

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>サードパーティ API を呼び出すために、クライアントからサーバー API への API 呼び出しを行う

クライアントからサーバー API への API 呼び出しを行います。 サーバーから、サードパーティ API リソースのアクセス トークンを取得し、必要な呼び出しはすべて発行します。

この方法では、サードパーティ API を呼び出すためにサーバー経由で追加のネットワーク ホップが必要になりますが、それによって最終的にはより安全なエクスペリエンスが得られます。

* サーバーでは、更新トークンを格納し、アプリからサードパーティ リソースへのアクセスが決して失われないようにすることができます。
* アプリでは、より機密性の高いアクセス許可を含む可能性のあるサーバーからのアクセス トークンをリークさせることはできません。
