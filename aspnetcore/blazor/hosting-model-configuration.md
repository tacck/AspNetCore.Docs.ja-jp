---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: c7e8d1f2dcba6432072a5cc11a6c5d78e50c2398
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159620"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="a4c1c-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="a4c1c-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4c1c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a4c1c-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="a4c1c-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a4c1c-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="a4c1c-107">環境</span><span class="sxs-lookup"><span data-stu-id="a4c1c-107">Environment</span></span>

<span data-ttu-id="a4c1c-108">アプリをローカルで実行する場合、環境は既定で開発に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="a4c1c-109">アプリが発行されると、環境は既定で実稼働になります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="a4c1c-110">ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="a4c1c-111">ヘッダーの値は環境です。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-111">The value of the header is the environment.</span></span> <span data-ttu-id="a4c1c-112">ホストされた Blazor アプリとサーバー アプリは、同じ環境を共有します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="a4c1c-113">環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="a4c1c-114">ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="a4c1c-115">他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="a4c1c-116">次の IIS の例では、発行された *web.config* ファイルにカスタム ヘッダーを追加しています。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="a4c1c-117">*web.config* ファイルは、*bin/Release/{TARGET FRAMEWORK}/publish* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="a4c1c-118">アプリが *publish* フォルダーに発行されたときに上書きされない IIS 用のカスタム *web.config* ファイルを使用するには、「ASP.NET Core Blazor WebAssembly をホストしてデプロイする<xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="a4c1c-119">`IWebAssemblyHostEnvironment` を挿入し、`Environment` プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="a4c1c-120">起動時に、`WebAssemblyHostBuilder` では、`HostEnvironment` プロパティを使用して `IWebAssemblyHostEnvironment` が公開されます。これにより、開発者は環境固有のロジックをコードに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="a4c1c-121">次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="a4c1c-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span><span class="sxs-lookup"><span data-stu-id="a4c1c-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="a4c1c-123">`IWebAssemblyHostEnvironment.BaseAddress` プロパティは、`NavigationManager` サービスを利用できないときの起動時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="a4c1c-124">構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-124">Configuration</span></span>

<span data-ttu-id="a4c1c-125">Blazor WebAssembly は次の構成をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="a4c1c-126">既定でアプリ設定ファイルの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider):</span><span class="sxs-lookup"><span data-stu-id="a4c1c-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="a4c1c-127">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="a4c1c-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="a4c1c-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="a4c1c-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="a4c1c-129">アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a4c1c-130">Blazor WebAssembly アプリの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="a4c1c-131">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="a4c1c-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="a4c1c-132">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="a4c1c-133">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-133">App settings configuration</span></span>

<span data-ttu-id="a4c1c-134">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="a4c1c-135">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="a4c1c-136">プロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-136">Provider configuration</span></span>

<span data-ttu-id="a4c1c-137">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> と[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)を使用して追加の構成を指定しています。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="a4c1c-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

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

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="a4c1c-139">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="a4c1c-140">認証の構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-140">Authentication configuration</span></span>

<span data-ttu-id="a4c1c-141">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="a4c1c-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="a4c1c-143">ログの構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-143">Logging configuration</span></span>

<span data-ttu-id="a4c1c-144">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-144">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="a4c1c-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="a4c1c-146">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-146">Host builder configuration</span></span>

<span data-ttu-id="a4c1c-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="a4c1c-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="a4c1c-148">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="a4c1c-148">Cached configuration</span></span>

<span data-ttu-id="a4c1c-149">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="a4c1c-150">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="a4c1c-151">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="a4c1c-152">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="a4c1c-153">PWA の *service-worker.js* ファイルと *service-worker-assets.js* ファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="a4c1c-154">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="a4c1c-155">ログの記録</span><span class="sxs-lookup"><span data-stu-id="a4c1c-155">Logging</span></span>

<span data-ttu-id="a4c1c-156">Blazor WebAssembly ログ記録のサポートについては、「.NET Core および ASP.NET Core でのログ記録<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="a4c1c-157">Blazor サーバー</span><span class="sxs-lookup"><span data-stu-id="a4c1c-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="a4c1c-158">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="a4c1c-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="a4c1c-159">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="a4c1c-160">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="a4c1c-161">UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="a4c1c-162">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="a4c1c-163">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="a4c1c-163">CSS class</span></span>                       | <span data-ttu-id="a4c1c-164">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="a4c1c-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="a4c1c-165">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-165">A lost connection.</span></span> <span data-ttu-id="a4c1c-166">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="a4c1c-167">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="a4c1c-168">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="a4c1c-169">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="a4c1c-170">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="a4c1c-171">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="a4c1c-172">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-172">Reconnection rejected.</span></span> <span data-ttu-id="a4c1c-173">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="a4c1c-174">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="a4c1c-175">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="a4c1c-176">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="a4c1c-177">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="a4c1c-178">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="a4c1c-179">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="a4c1c-180">表示モード</span><span class="sxs-lookup"><span data-stu-id="a4c1c-180">Render mode</span></span>

<span data-ttu-id="a4c1c-181">Blazor サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="a4c1c-182">これは、 *_Host.cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="a4c1c-183">`RenderMode` により、コンポーネントに対して次の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="a4c1c-184">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="a4c1c-185">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="a4c1c-186">説明</span><span class="sxs-lookup"><span data-stu-id="a4c1c-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="a4c1c-187">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="a4c1c-188">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="a4c1c-189">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="a4c1c-190">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-190">Output from the component isn't included.</span></span> <span data-ttu-id="a4c1c-191">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="a4c1c-192">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="a4c1c-193">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="a4c1c-194">Blazor サーバー アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="a4c1c-194">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="a4c1c-195">場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-195">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="a4c1c-196">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-196">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="a4c1c-197">*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-197">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="a4c1c-198">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-198">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="a4c1c-199">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-199">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="a4c1c-200">ログの記録</span><span class="sxs-lookup"><span data-stu-id="a4c1c-200">Logging</span></span>

<span data-ttu-id="a4c1c-201">Blazor サーバー ログのサポートの詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4c1c-201">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
