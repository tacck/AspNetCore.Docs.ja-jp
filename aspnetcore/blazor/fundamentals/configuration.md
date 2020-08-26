---
title: ASP.NET Core Blazor の構成
author: guardrex
description: アプリの設定、認証、ログの構成など、Blazor アプリの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 1607a80ea12cedd82b5996cf1b09e02e1aada2fd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628808"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="ea585-103">ASP.NET Core Blazor の構成</span><span class="sxs-lookup"><span data-stu-id="ea585-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="ea585-104">このトピックの対象は、Blazor WebAssembly です。</span><span class="sxs-lookup"><span data-stu-id="ea585-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="ea585-105">ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea585-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="ea585-106">Blazor WebAssembly では既定で、アプリ設定ファイルから構成が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="ea585-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="ea585-107">アプリによって登録されたその他の構成プロバイダーから構成を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="ea585-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="ea585-108">すべてのプロバイダーまたはプロバイダー機能が Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="ea585-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="ea585-109">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration): クライアント シークレットのシナリオでは、プロバイダーはマネージド ID およびアプリケーション ID (クライアント ID) に対してはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ea585-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="ea585-110">クライアント シークレットを使用したアプリケーション ID は、ASP.NET Core アプリ、特に Blazor WebAssembly アプリについては推奨されません。これは、クライアント シークレットをサービスにアクセスするためにクライアント側でセキュリティで保護することができないためです。</span><span class="sxs-lookup"><span data-stu-id="ea585-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="ea585-111">[Azure App Configuration プロバイダー](/azure/azure-app-configuration/quickstart-aspnet-core-app): このプロバイダーは Blazor WebAssembly アプリに適していません。これは、Blazor WebAssembly アプリは Azure 内のサーバー上では実行されないためです。</span><span class="sxs-lookup"><span data-stu-id="ea585-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="ea585-112">Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ea585-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="ea585-113">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="ea585-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="ea585-114">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea585-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="ea585-115">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="ea585-115">App settings configuration</span></span>

<span data-ttu-id="ea585-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ea585-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="ea585-117">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea585-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="ea585-118">EF Core を使用したカスタム構成プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ea585-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="ea585-119">「<xref:fundamentals/configuration/index#custom-configuration-provider>」で説明されている EF Core を使用したカスタム構成プロバイダーは、Blazor WebAssembly アプリで動作します。</span><span class="sxs-lookup"><span data-stu-id="ea585-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ea585-120">`Program.Main` (`Program.cs`) で次のコードを使用して、例の構成プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea585-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="ea585-121">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea585-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="ea585-122">メモリ構成のソース</span><span class="sxs-lookup"><span data-stu-id="ea585-122">Memory Configuration Source</span></span>

<span data-ttu-id="ea585-123">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="ea585-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="ea585-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea585-124">`Program.Main`:</span></span>

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

<span data-ttu-id="ea585-125">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ea585-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="ea585-126">その他の構成ファイルを `wwwroot` フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="ea585-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="ea585-127">この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="ea585-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="ea585-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="ea585-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="ea585-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea585-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="ea585-130">認証の構成</span><span class="sxs-lookup"><span data-stu-id="ea585-130">Authentication configuration</span></span>

<span data-ttu-id="ea585-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ea585-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="ea585-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea585-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="ea585-133">ログの構成</span><span class="sxs-lookup"><span data-stu-id="ea585-133">Logging configuration</span></span>

<span data-ttu-id="ea585-134">[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="ea585-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="ea585-135">プレースホルダー `{VERSION}` では、[NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) のパッケージの**バージョン履歴**にある、アプリの共有フレームワークのバージョンに一致するパッケージの安定した最新バージョンを確認できます。</span><span class="sxs-lookup"><span data-stu-id="ea585-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="ea585-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="ea585-136">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="ea585-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea585-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="ea585-138">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="ea585-138">Host builder configuration</span></span>

<span data-ttu-id="ea585-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="ea585-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="ea585-140">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="ea585-140">Cached configuration</span></span>

<span data-ttu-id="ea585-141">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="ea585-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="ea585-142">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="ea585-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="ea585-143">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="ea585-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="ea585-144">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="ea585-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="ea585-145">PWA の `service-worker.js` と `service-worker-assets.js` のファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="ea585-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="ea585-146">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea585-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
