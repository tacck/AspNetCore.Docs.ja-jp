---
title: ASP.NET Core Blazor の構成
author: guardrex
description: アプリの設定、認証、ログの構成など、Blazor アプリの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- appsettings.json
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234375"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>ASP.NET Core Blazor の構成

> [!NOTE]
> このトピックの対象は、Blazor WebAssembly です。 ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/configuration/index>」を参照してください。

Blazor WebAssembly では既定で、アプリ設定ファイルから構成が読み込まれます。

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

アプリによって登録されたその他の構成プロバイダーから構成を取得することもできます。

すべてのプロバイダーまたはプロバイダー機能が Blazor WebAssembly アプリに適しているわけではありません。

* [Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration): クライアント シークレットのシナリオでは、プロバイダーはマネージド ID およびアプリケーション ID (クライアント ID) に対してはサポートされていません。 クライアント シークレットを使用したアプリケーション ID は、ASP.NET Core アプリ、特に Blazor WebAssembly アプリについては推奨されません。これは、クライアント シークレットをサービスにアクセスするためにクライアント側でセキュリティで保護することができないためです。
* [Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app): このプロバイダーは Blazor WebAssembly アプリに適していません。これは、Blazor WebAssembly アプリは Azure 内のサーバー上では実行されないためです。

> [!WARNING]
> Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。 **アプリのシークレットや資格情報を構成に保存しないでください。**

構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。

## <a name="app-settings-configuration"></a>アプリ設定の構成

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>EF Core を使用したカスタム構成プロバイダー

「<xref:fundamentals/configuration/index#custom-configuration-provider>」で説明されている EF Core を使用したカスタム構成プロバイダーは、Blazor WebAssembly アプリで動作します。

`Program.Main` (`Program.cs`) で次のコードを使用して、例の構成プロバイダーを追加します。

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>メモリ構成のソース

次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

その他の構成ファイルを `wwwroot` フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。 この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a>認証の構成

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>ログの構成

[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) のパッケージ参照を追加します。

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

プレースホルダー `{VERSION}` では、 [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) のパッケージの **バージョン履歴** にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。

`wwwroot/appsettings.json`:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>ホスト ビルダーの構成

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>キャッシュされた構成

構成ファイルは、オフラインで使用できるようにキャッシュされます。 [プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。 次の理由により、展開間で構成ファイルを編集しても意味がありません。

* ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。
* PWA の `service-worker.js` と `service-worker-assets.js` のファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。

PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。
