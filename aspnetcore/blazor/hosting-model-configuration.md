---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 17ed43a12643f067da73658bec72400acbe1be43
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772075"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="c9c22-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="c9c22-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9c22-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c9c22-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="c9c22-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c9c22-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="c9c22-107">環境</span><span class="sxs-lookup"><span data-stu-id="c9c22-107">Environment</span></span>

<span data-ttu-id="c9c22-108">アプリをローカルで実行する場合、環境は既定で開発に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="c9c22-109">アプリが発行されると、環境は既定で実稼働になります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="c9c22-110">ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="c9c22-111">ヘッダーの値は環境です。</span><span class="sxs-lookup"><span data-stu-id="c9c22-111">The value of the header is the environment.</span></span> <span data-ttu-id="c9c22-112">ホストされた Blazor アプリとサーバー アプリは、同じ環境を共有します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="c9c22-113">環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c9c22-114">ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="c9c22-115">他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="c9c22-116">次の IIS の例では、発行された *web.config* ファイルにカスタム ヘッダーを追加しています。</span><span class="sxs-lookup"><span data-stu-id="c9c22-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="c9c22-117">*web.config* ファイルは、*bin/Release/{TARGET FRAMEWORK}/publish* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="c9c22-118">アプリが *publish* フォルダーに発行されたときに上書きされない IIS 用のカスタム *web.config* ファイルを使用するには、「ASP.NET Core Blazor WebAssembly をホストしてデプロイする<xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="c9c22-119">`IWebAssemblyHostEnvironment` を挿入し、`Environment` プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="c9c22-120">起動時に、`WebAssemblyHostBuilder` では、`HostEnvironment` プロパティを使用して `IWebAssemblyHostEnvironment` が公開されます。これにより、開発者は環境固有のロジックをコードに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="c9c22-121">次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="c9c22-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span><span class="sxs-lookup"><span data-stu-id="c9c22-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="c9c22-123">`IWebAssemblyHostEnvironment.BaseAddress` プロパティは、`NavigationManager` サービスを利用できないときの起動時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="c9c22-124">構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-124">Configuration</span></span>

<span data-ttu-id="c9c22-125">Blazor WebAssembly では、以下から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-125">Blazor WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="c9c22-126">既定のアプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="c9c22-126">App settings files by default:</span></span>
  * <span data-ttu-id="c9c22-127">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="c9c22-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="c9c22-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="c9c22-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="c9c22-129">アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="c9c22-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="c9c22-130">すべてのプロバイダーが Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="c9c22-130">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="c9c22-131">Blazor WebAssembly に対してサポートされているプロバイダーの説明については、「[Blazor WASM の構成プロバイダーの明確化 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)」で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="c9c22-131">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="c9c22-132">Blazor WebAssembly アプリの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-132">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="c9c22-133">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="c9c22-133">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="c9c22-134">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-134">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="c9c22-135">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-135">App settings configuration</span></span>

<span data-ttu-id="c9c22-136">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c9c22-136">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="c9c22-137">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-137">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="c9c22-138">プロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-138">Provider configuration</span></span>

<span data-ttu-id="c9c22-139">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-139">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="c9c22-140">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c9c22-140">`Program.Main`:</span></span>

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

<span data-ttu-id="c9c22-141">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="c9c22-142">その他の構成ファイルを *wwwroot* フォルダーから構成に読み取るには、`HttpClient` を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-142">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="c9c22-143">この方法を使用する場合、既存の `HttpClient` サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-143">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="c9c22-144">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="c9c22-144">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="c9c22-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c9c22-145">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="c9c22-146">認証の構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-146">Authentication configuration</span></span>

<span data-ttu-id="c9c22-147">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c9c22-147">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="c9c22-148">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c9c22-148">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="c9c22-149">ログの構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-149">Logging configuration</span></span>

<span data-ttu-id="c9c22-150">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c9c22-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="c9c22-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c9c22-151">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="c9c22-152">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-152">Host builder configuration</span></span>

<span data-ttu-id="c9c22-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="c9c22-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="c9c22-154">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="c9c22-154">Cached configuration</span></span>

<span data-ttu-id="c9c22-155">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="c9c22-156">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="c9c22-157">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="c9c22-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="c9c22-158">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="c9c22-159">PWA の *service-worker.js* ファイルと *service-worker-assets.js* ファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="c9c22-160">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="c9c22-161">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c9c22-161">Logging</span></span>

<span data-ttu-id="c9c22-162">Blazor WebAssembly ログ記録のサポートについては、「.NET Core および ASP.NET Core でのログ記録<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="c9c22-163">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="c9c22-163">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="c9c22-164">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="c9c22-164">Reflect the connection state in the UI</span></span>

<span data-ttu-id="c9c22-165">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-165">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="c9c22-166">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-166">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="c9c22-167">UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-167">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="c9c22-168">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-168">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="c9c22-169">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="c9c22-169">CSS class</span></span>                       | <span data-ttu-id="c9c22-170">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="c9c22-170">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="c9c22-171">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="c9c22-171">A lost connection.</span></span> <span data-ttu-id="c9c22-172">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="c9c22-172">The client is attempting to reconnect.</span></span> <span data-ttu-id="c9c22-173">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-173">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="c9c22-174">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-174">An active connection is re-established to the server.</span></span> <span data-ttu-id="c9c22-175">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="c9c22-175">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="c9c22-176">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-176">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="c9c22-177">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-177">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="c9c22-178">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="c9c22-178">Reconnection rejected.</span></span> <span data-ttu-id="c9c22-179">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="c9c22-179">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="c9c22-180">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-180">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="c9c22-181">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-181">This connection state may result when:</span></span><ul><li><span data-ttu-id="c9c22-182">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="c9c22-182">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="c9c22-183">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="c9c22-183">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="c9c22-184">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-184">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="c9c22-185">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="c9c22-185">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="c9c22-186">表示モード</span><span class="sxs-lookup"><span data-stu-id="c9c22-186">Render mode</span></span>

<span data-ttu-id="c9c22-187">Blazor サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-187">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="c9c22-188">これは、 *_Host.cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-188">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="c9c22-189">`RenderMode` により、コンポーネントに対して次の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-189">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="c9c22-190">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="c9c22-190">Is prerendered into the page.</span></span>
* <span data-ttu-id="c9c22-191">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="c9c22-191">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="c9c22-192">説明</span><span class="sxs-lookup"><span data-stu-id="c9c22-192">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="c9c22-193">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-193">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9c22-194">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-194">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="c9c22-195">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c9c22-195">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="c9c22-196">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="c9c22-196">Output from the component isn't included.</span></span> <span data-ttu-id="c9c22-197">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-197">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="c9c22-198">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="c9c22-198">Renders the component into static HTML.</span></span> |

<span data-ttu-id="c9c22-199">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c9c22-199">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="c9c22-200">Blazor サーバー アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="c9c22-200">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="c9c22-201">場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9c22-201">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="c9c22-202">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。</span><span class="sxs-lookup"><span data-stu-id="c9c22-202">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="c9c22-203">*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c9c22-203">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="c9c22-204">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-204">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="c9c22-205">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="c9c22-205">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="c9c22-206">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c9c22-206">Logging</span></span>

<span data-ttu-id="c9c22-207">Blazor サーバー ログのサポートの詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9c22-207">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
