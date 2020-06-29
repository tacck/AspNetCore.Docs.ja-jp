---
title: ASP.NET Core Blazor の構成
author: guardrex
description: アプリの設定、認証、ログの構成など、Blazor アプリの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 0e36b81d771b07e85158724c02210ee50a3ab118
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242681"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="9507a-103">ASP.NET Core Blazor の構成</span><span class="sxs-lookup"><span data-stu-id="9507a-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="9507a-104">このトピックは Blazor WebAssembly を対象としています。</span><span class="sxs-lookup"><span data-stu-id="9507a-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="9507a-105">ASP.NET Core アプリの構成に関する一般的なガイダンスについては、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9507a-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

Blazor<span data-ttu-id="9507a-106"> WebAssembly では、以下から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="9507a-106"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="9507a-107">既定のアプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="9507a-107">App settings files by default:</span></span>
  * `wwwroot/appsettings.json`
  * `wwwroot/appsettings.{ENVIRONMENT}.json`
* <span data-ttu-id="9507a-108">アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="9507a-108">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="9507a-109">すべてのプロバイダーが Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="9507a-109">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="9507a-110">どのプロバイダーが Blazor WebAssembly でサポートされているかについては、「[Blazor WASM の構成プロバイダーの明確化 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)」で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="9507a-110">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="9507a-111">Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9507a-111">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="9507a-112">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="9507a-112">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="9507a-113">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9507a-113">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="9507a-114">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="9507a-114">App settings configuration</span></span>

<span data-ttu-id="9507a-115">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9507a-115">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="9507a-116">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="9507a-116">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="provider-configuration"></a><span data-ttu-id="9507a-117">プロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="9507a-117">Provider configuration</span></span>

<span data-ttu-id="9507a-118">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="9507a-118">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="9507a-119">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9507a-119">`Program.Main`:</span></span>

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

<span data-ttu-id="9507a-120">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="9507a-120">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="9507a-121">その他の構成ファイルを `wwwroot` フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="9507a-121">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="9507a-122">この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="9507a-122">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="9507a-123">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="9507a-123">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="9507a-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9507a-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="9507a-125">認証の構成</span><span class="sxs-lookup"><span data-stu-id="9507a-125">Authentication configuration</span></span>

<span data-ttu-id="9507a-126">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9507a-126">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="9507a-127">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9507a-127">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="9507a-128">ログの構成</span><span class="sxs-lookup"><span data-stu-id="9507a-128">Logging configuration</span></span>

<span data-ttu-id="9507a-129">[`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="9507a-129">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="9507a-130">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="9507a-130">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="9507a-131">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9507a-131">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="9507a-132">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="9507a-132">Host builder configuration</span></span>

<span data-ttu-id="9507a-133">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9507a-133">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="9507a-134">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="9507a-134">Cached configuration</span></span>

<span data-ttu-id="9507a-135">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="9507a-135">Configuration files are cached for offline use.</span></span> <span data-ttu-id="9507a-136">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="9507a-136">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="9507a-137">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="9507a-137">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="9507a-138">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="9507a-138">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="9507a-139">PWA の `service-worker.js` と `service-worker-assets.js` のファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="9507a-139">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="9507a-140">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9507a-140">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
