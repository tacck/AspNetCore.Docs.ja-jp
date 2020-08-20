---
title: ASP.NET Core Blazor Server のセキュリティに関するその他のシナリオ
author: guardrex
description: セキュリティに関するその他のシナリオ用に Blazor Server を構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 62cf16e3e495e3e9a015a3be4e7fffd6a3ce08bc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626468"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor Server のセキュリティに関するその他のシナリオ

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a>Blazor Server アプリにトークンを渡す

Blazor Server アプリの Razor コンポーネントの外部で使用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。 `Startup.ConfigureServices` の完全な例を含むサンプル コードについては、「[サーバー側の Blazor アプリケーションにトークンを渡す](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。

通常の Razor Pages や MVC アプリの場合と同じように、Blazor Server アプリを認証します。 トークンをプロビジョニングし、認証 cookie に保存します。 次に例を示します。

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

アクセス トークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

[依存関係の挿入 (DI)](xref:blazor/fundamentals/dependency-injection) からトークンを解決するために Blazor アプリ内で使用できる**スコープを持つ**トークン プロバイダー サービスを定義します。

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

`Startup.ConfigureServices` で、次のサービスを追加します。

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

`_Host.cshtml` ファイルで、`InitialApplicationState` のインスタンスを作成し、それをパラメーターとしてアプリに渡します。

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

`App` コンポーネント (`App.razor`) で、サービスを解決し、パラメーターからのデータを使用してそれを初期化します。

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

セキュリティで保護された API 要求を行うサービスで、トークン プロバイダーを挿入し、API を呼び出すトークンを取得します。

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a>認証スキームを設定する

複数の認証ミドルウェアを使用していることにより、複数の認証スキームがあるアプリの場合、Blazor で使用されるスキームを、`Startup.Configure` のエンドポイント構成で明示的に設定できます。 次の例では、Azure Active Directory スキームを設定します。

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>OpenID Connect (OIDC) v2.0 エンドポイントを使用する

認証ライブラリと Blazor テンプレートでは、OpenID Connect (OIDC) v1.0 エンドポイントが使用されます。 v2.0 エンドポイントを使用するには、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> で <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> オプションを構成します。

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

または、アプリ設定ファイル (`appsettings.json`) で設定を行うこともできます。

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

証明機関へのセグメントを追跡することがアプリの OIDC プロバイダー (AAD 以外のプロバイダーなど) にとって適切でない場合は、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> プロパティを直接設定します。 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> またはアプリ設定ファイルで <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> キーを使用してプロパティを設定します。

### <a name="code-changes"></a>コード変更

* ID トークンの要求のリストは、v2.0 エンドポイントで変更されています。 詳細については、「[Microsoft ID プラットフォーム (v2.0) に更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を Azure のドキュメントで参照してください。
* リソースは v2.0 エンドポイントのスコープ URI で指定されているため、<xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> の <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> プロパティ設定を削除します。

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

使用するアプリ ID URI は、OIDC プロバイダーのアプリ登録の説明で見つけることができます。
