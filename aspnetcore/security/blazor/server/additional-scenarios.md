---
title: ASP.NET Core Blazor サーバーの追加のセキュリティシナリオ
author: guardrex
description: Blazor追加のセキュリティシナリオ用にサーバーを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 9d26cde4d8964a8285241bb0158d8e6f8d5f8dbc
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444075"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazor サーバーの追加のセキュリティシナリオ

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>サーバーアプリにトークンを渡す Blazor

サーバーアプリのコンポーネントの外部で利用できるトークンは Razor Blazor 、このセクションで説明する方法を使用してコンポーネントに渡すことができます。 完全な例を含むサンプルコードについ `Startup.ConfigureServices` ては、「[サーバー側 Blazor アプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。

通常の Blazor ページや MVC アプリの場合と同様に、サーバーアプリを認証し Razor ます。 トークンをプロビジョニングし、認証 cookie に保存します。 次に例を示します。

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

アクセストークンと更新トークンを使用して最初のアプリの状態を渡すクラスを定義します。

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

**scoped** Blazor [依存関係の挿入 (DI)](xref:blazor/dependency-injection)からトークンを解決するためにアプリ内で使用できるスコープ付きトークンプロバイダーサービスを定義します。

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

で `Startup.ConfigureServices` 、次のサービスを追加します。

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host*ファイルで、のインスタンスを作成し、 `InitialApplicationState` それをパラメーターとしてアプリに渡します。

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

`App`コンポーネント (*app.xaml*) で、サービスを解決し、パラメーターのデータで初期化します。

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

セキュリティで保護された API 要求を行うサービスで、トークンプロバイダーを挿入し、API を呼び出すトークンを取得します。

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Open ID Connect (OIDC) v2.0 エンドポイントを使用する

認証ライブラリとテンプレートでは、 Blazor OPEN ID Connect (OIDC) v1.0 エンドポイントが使用されます。 V2.0 エンドポイントを使用するには、 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> でオプションを構成し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ます。

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

または、アプリ設定 (*appsettings. json*) ファイルで設定を行うこともできます。

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

証明機関へのトラッキングがアプリの OIDC プロバイダー (AAD 以外のプロバイダーを含む) に適していない場合は、プロパティを直接設定し `Authority` ます。 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>またはアプリ設定ファイルのプロパティを、キーを使用して設定し `Authority` ます。

### <a name="code-changes"></a>コードの変更

* V2.0 エンドポイントの ID トークンの変更要求の一覧。 詳細については、「 [Microsoft identity platform (v2.0) を更新する理由](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)」を参照してください。 Azure のドキュメントを参照してください。
* リソースは、v2.0 エンドポイントのスコープ Uri で指定されているため、 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 次のようにのプロパティ設定を削除し <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> ます。

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

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

OIDC プロバイダーのアプリ登録の説明で使用するアプリ ID URI を見つけることができます。
