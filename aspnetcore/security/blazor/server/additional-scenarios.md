---
title: ASP.NET Core Blazorサーバーの追加のセキュリティシナリオ
author: guardrex
description: 追加のセキュリティシナリオBlazor用にサーバーを構成する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 95e9e57889fdbb5270f895874c9b8148ae4ca48d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772805"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a>ASP.NET Core Blazorサーバーの追加のセキュリティシナリオ

作成者: [Javier Calvarro Jeannine](https://github.com/javiercn)

## <a name="pass-tokens-to-a-blazor-server-app"></a>Blazorサーバーアプリにトークンを渡す

BlazorサーバーアプリのRazorコンポーネントの外部で利用できるトークンは、このセクションで説明する方法を使用してコンポーネントに渡すことができます。 完全`Startup.ConfigureServices`な例を含むサンプルコードについては、「[サーバー側Blazorアプリケーションへのトークンの引き渡し](https://github.com/javiercn/blazor-server-aad-sample)」を参照してください。

通常RazorのBlazorページや MVC アプリの場合と同様に、サーバーアプリを認証します。 トークンをプロビジョニングし、認証 cookie に保存します。 次に例を示します。

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

[依存関係の挿入 (DI)](xref:blazor/dependency-injection)からトークンを解決するBlazorためにアプリ内で使用できる**スコープ**付きトークンプロバイダーサービスを定義します。

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

で`Startup.ConfigureServices`、次のサービスを追加します。

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host*ファイルで、の`InitialApplicationState`インスタンスを作成し、それをパラメーターとしてアプリに渡します。

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
