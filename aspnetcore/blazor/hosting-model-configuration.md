---
title: ASP.NET Core Blazor ホスティング モデルの構成
author: guardrex
description: Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。
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
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 3cef67806ce0e2e045122bdc962e93795be68572
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679580"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="1dbe4-103">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="1dbe4-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1dbe4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1dbe4-105">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="1dbe4-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1dbe4-106"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="1dbe4-107">環境</span><span class="sxs-lookup"><span data-stu-id="1dbe4-107">Environment</span></span>

<span data-ttu-id="1dbe4-108">アプリをローカルで実行する場合、環境は既定で開発に設定されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="1dbe4-109">アプリが発行されると、環境は既定で実稼働になります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="1dbe4-110">ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="1dbe4-111">ヘッダーの値は環境です。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-111">The value of the header is the environment.</span></span> <span data-ttu-id="1dbe4-112">ホストされた Blazor アプリとサーバー アプリによって、同じ環境が共有されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="1dbe4-113">環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="1dbe4-114">ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="1dbe4-115">他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="1dbe4-116">次の IIS の例では、発行された *web.config* ファイルにカスタム ヘッダーを追加しています。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="1dbe4-117">*web.config* ファイルは、*bin/Release/{TARGET FRAMEWORK}/publish* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="1dbe4-118">アプリが *publish* フォルダーに発行されたときに上書きされない IIS 用のカスタム *web.config* ファイルを使用するには、「ASP.NET Core Blazor WebAssembly をホストしてデプロイする<xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="1dbe4-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> を挿入し、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-119">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="1dbe4-120">起動時に、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> プロパティを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> が公開されます。これにより、開発者は環境固有のロジックをコードに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-120">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="1dbe4-121">次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="1dbe4-122"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> プロパティは、<xref:Microsoft.AspNetCore.Components.NavigationManager> サービスを利用できないときの起動時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-122">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="1dbe4-123">構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-123">Configuration</span></span>

Blazor<span data-ttu-id="1dbe4-124"> WebAssembly では、以下から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-124"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="1dbe4-125">既定のアプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="1dbe4-125">App settings files by default:</span></span>
  * <span data-ttu-id="1dbe4-126">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="1dbe4-126">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="1dbe4-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="1dbe4-127">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="1dbe4-128">アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-128">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="1dbe4-129">すべてのプロバイダーが Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-129">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="1dbe4-130">どのプロバイダーが Blazor WebAssembly でサポートされているかについては、「[Blazor WASM の構成プロバイダーの明確化 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)」で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-130">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="1dbe4-131">Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-131">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="1dbe4-132">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="1dbe4-132">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="1dbe4-133">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-133">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="1dbe4-134">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-134">App settings configuration</span></span>

<span data-ttu-id="1dbe4-135">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-135">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="1dbe4-136">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-136">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="1dbe4-137">プロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-137">Provider configuration</span></span>

<span data-ttu-id="1dbe4-138">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="1dbe4-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-139">`Program.Main`:</span></span>

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

<span data-ttu-id="1dbe4-140">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-140">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="1dbe4-141">その他の構成ファイルを *wwwroot* フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-141">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="1dbe4-142">この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-142">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="1dbe4-143">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-143">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="1dbe4-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-144">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="1dbe4-145">認証の構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-145">Authentication configuration</span></span>

<span data-ttu-id="1dbe4-146">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-146">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="1dbe4-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-147">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="1dbe4-148">ログの構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-148">Logging configuration</span></span>

<span data-ttu-id="1dbe4-149">[Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-149">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="1dbe4-150">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-150">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="1dbe4-151">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-151">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="1dbe4-152">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-152">Host builder configuration</span></span>

<span data-ttu-id="1dbe4-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-153">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="1dbe4-154">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="1dbe4-154">Cached configuration</span></span>

<span data-ttu-id="1dbe4-155">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-155">Configuration files are cached for offline use.</span></span> <span data-ttu-id="1dbe4-156">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-156">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="1dbe4-157">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-157">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="1dbe4-158">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-158">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="1dbe4-159">PWA の *service-worker.js* ファイルと *service-worker-assets.js* ファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-159">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="1dbe4-160">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-160">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="1dbe4-161">ログの記録</span><span class="sxs-lookup"><span data-stu-id="1dbe4-161">Logging</span></span>

<span data-ttu-id="1dbe4-162">Blazor WebAssembly ログ記録の詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-162">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

### <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="1dbe4-163">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="1dbe4-163">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="1dbe4-164">Cookie や HTTP 認証ヘッダーなどの資格情報を送信するように SignalR の基となるクライアントを構成するには:</span><span class="sxs-lookup"><span data-stu-id="1dbe4-164">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="1dbe4-165"><xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> を使用して、クロスオリジン [フェッチ](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)要求に <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> を設定します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-165">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessagHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="1dbe4-166"><xref:System.Net.Http.HttpMessageHandler> を <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> オプションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-166">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var client = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessagHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="1dbe4-167">詳細については、「<xref:signalr/configuration#configure-additional-options>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-167">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="1dbe4-168"> サーバー</span><span class="sxs-lookup"><span data-stu-id="1dbe4-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="1dbe4-169">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="1dbe4-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="1dbe4-170">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="1dbe4-171">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="1dbe4-172">UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="1dbe4-173">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="1dbe4-174">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="1dbe4-174">CSS class</span></span>                       | <span data-ttu-id="1dbe4-175">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="1dbe4-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="1dbe4-176">接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-176">A lost connection.</span></span> <span data-ttu-id="1dbe4-177">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="1dbe4-178">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="1dbe4-179">サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="1dbe4-180">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="1dbe4-181">再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="1dbe4-182">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="1dbe4-183">再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-183">Reconnection rejected.</span></span> <span data-ttu-id="1dbe4-184">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="1dbe4-185">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="1dbe4-186">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="1dbe4-187">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="1dbe4-188">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="1dbe4-189">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="1dbe4-190">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="1dbe4-191">表示モード</span><span class="sxs-lookup"><span data-stu-id="1dbe4-191">Render mode</span></span>

Blazor<span data-ttu-id="1dbe4-192"> サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="1dbe4-193">これは、 *_Host.cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="1dbe4-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1dbe4-195">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="1dbe4-196">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="1dbe4-197">説明</span><span class="sxs-lookup"><span data-stu-id="1dbe4-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="1dbe4-198">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="1dbe4-199">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="1dbe4-200">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="1dbe4-201">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-201">Output from the component isn't included.</span></span> <span data-ttu-id="1dbe4-202">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="1dbe4-203">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="1dbe4-204">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="1dbe4-205">Blazor サーバー アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="1dbe4-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="1dbe4-206">場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="1dbe4-207">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="1dbe4-208">*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="1dbe4-209">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="1dbe4-210">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="1dbe4-211">ログの記録</span><span class="sxs-lookup"><span data-stu-id="1dbe4-211">Logging</span></span>

<span data-ttu-id="1dbe4-212">Blazor サーバー ログのサポートの詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dbe4-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
