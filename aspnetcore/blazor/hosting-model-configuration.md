---
<span data-ttu-id="0351e-101">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-103">'Blazor'</span></span>
- <span data-ttu-id="0351e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-104">'Identity'</span></span>
- <span data-ttu-id="0351e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-106">'Razor'</span></span>
- <span data-ttu-id="0351e-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="0351e-108">ASP.NET Core Blazor ホスティング モデルの構成</span><span class="sxs-lookup"><span data-stu-id="0351e-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="0351e-109">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0351e-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0351e-110">この記事では、ホスティング モデルの構成について説明します。</span><span class="sxs-lookup"><span data-stu-id="0351e-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="0351e-111"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="0351e-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="0351e-112">環境</span><span class="sxs-lookup"><span data-stu-id="0351e-112">Environment</span></span>

<span data-ttu-id="0351e-113">アプリをローカルで実行する場合、環境は既定で開発に設定されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="0351e-114">アプリが発行されると、環境は既定で実稼働になります。</span><span class="sxs-lookup"><span data-stu-id="0351e-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="0351e-115">ホストされている Blazor WebAssembly アプリは、`blazor-environment` ヘッダーを追加して環境をブラウザーに送信するミドルウェアを介して、サーバーから環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="0351e-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="0351e-116">ヘッダーの値は環境です。</span><span class="sxs-lookup"><span data-stu-id="0351e-116">The value of the header is the environment.</span></span> <span data-ttu-id="0351e-117">ホストされた Blazor アプリとサーバー アプリによって、同じ環境が共有されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="0351e-118">環境の構成方法などの詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0351e-119">ローカルで実行されているスタンドアロン アプリの場合、開発サーバーでは `blazor-environment` ヘッダーが追加され、開発環境が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="0351e-120">他のホスト環境の環境を指定するには、`blazor-environment` ヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="0351e-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="0351e-121">次の IIS の例では、発行された *web.config* ファイルにカスタム ヘッダーを追加しています。</span><span class="sxs-lookup"><span data-stu-id="0351e-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="0351e-122">*web.config* ファイルは、*bin/Release/{TARGET FRAMEWORK}/publish* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="0351e-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="0351e-123">アプリが *publish* フォルダーに発行されたときに上書きされない IIS 用のカスタム *web.config* ファイルを使用するには、「ASP.NET Core Blazor WebAssembly をホストしてデプロイする<xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="0351e-124"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> を挿入し、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> プロパティを読み取ることで、コンポーネント内のアプリの環境を取得します。</span><span class="sxs-lookup"><span data-stu-id="0351e-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="0351e-125">起動時に、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> では、<xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> プロパティを使用して <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> が公開されます。これにより、開発者は環境固有のロジックをコードに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="0351e-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="0351e-126">次の便利な拡張メソッドを使用すると、現在の環境で開発、運用、ステージング、およびカスタムの環境名を確認できます。</span><span class="sxs-lookup"><span data-stu-id="0351e-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="0351e-127"><xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> プロパティは、<xref:Microsoft.AspNetCore.Components.NavigationManager> サービスを利用できないときの起動時に使用できます。</span><span class="sxs-lookup"><span data-stu-id="0351e-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="0351e-128">構成</span><span class="sxs-lookup"><span data-stu-id="0351e-128">Configuration</span></span>

Blazor<span data-ttu-id="0351e-129"> WebAssembly では、以下から構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0351e-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="0351e-130">既定のアプリ設定ファイル</span><span class="sxs-lookup"><span data-stu-id="0351e-130">App settings files by default:</span></span>
  * <span data-ttu-id="0351e-131">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="0351e-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="0351e-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span><span class="sxs-lookup"><span data-stu-id="0351e-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="0351e-133">アプリによって登録されたその他の[構成プロバイダー](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="0351e-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="0351e-134">すべてのプロバイダーが Blazor WebAssembly アプリに適しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0351e-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="0351e-135">どのプロバイダーが Blazor WebAssembly でサポートされているかについては、「[Blazor WASM の構成プロバイダーの明確化 (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134)」で追跡されています。</span><span class="sxs-lookup"><span data-stu-id="0351e-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="0351e-136">Blazor WebAssembly アプリでの構成は、ユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="0351e-137">**アプリのシークレットや資格情報を構成に保存しないでください。**</span><span class="sxs-lookup"><span data-stu-id="0351e-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="0351e-138">構成プロバイダーの詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="0351e-139">アプリ設定の構成</span><span class="sxs-lookup"><span data-stu-id="0351e-139">App settings configuration</span></span>

<span data-ttu-id="0351e-140">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0351e-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="0351e-141">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="0351e-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="0351e-142">プロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="0351e-142">Provider configuration</span></span>

<span data-ttu-id="0351e-143">次の例では、<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> を使用して追加の構成を指定します。</span><span class="sxs-lookup"><span data-stu-id="0351e-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="0351e-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0351e-144">`Program.Main`:</span></span>

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

<span data-ttu-id="0351e-145">構成データにアクセスするために、コンポーネントに <xref:Microsoft.Extensions.Configuration.IConfiguration> インスタンスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="0351e-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="0351e-146">その他の構成ファイルを *wwwroot* フォルダーから構成に読み取るには、<xref:System.Net.Http.HttpClient> を使用してファイルの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="0351e-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="0351e-147">この方法を使用する場合、既存の <xref:System.Net.Http.HttpClient> サービスの登録では、次の例に示すように、作成されたローカル クライアントを使用してファイルを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0351e-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="0351e-148">*wwwroot/cars.json*:</span><span class="sxs-lookup"><span data-stu-id="0351e-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="0351e-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0351e-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="0351e-150">認証の構成</span><span class="sxs-lookup"><span data-stu-id="0351e-150">Authentication configuration</span></span>

<span data-ttu-id="0351e-151">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0351e-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="0351e-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0351e-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions);
```

#### <a name="logging-configuration"></a><span data-ttu-id="0351e-153">ログの構成</span><span class="sxs-lookup"><span data-stu-id="0351e-153">Logging configuration</span></span>

<span data-ttu-id="0351e-154">*wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0351e-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="0351e-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0351e-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="0351e-156">ホスト ビルダーの構成</span><span class="sxs-lookup"><span data-stu-id="0351e-156">Host builder configuration</span></span>

<span data-ttu-id="0351e-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="0351e-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="0351e-158">キャッシュされた構成</span><span class="sxs-lookup"><span data-stu-id="0351e-158">Cached configuration</span></span>

<span data-ttu-id="0351e-159">構成ファイルは、オフラインで使用できるようにキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="0351e-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="0351e-160">[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) では、新しい展開を作成するときにのみ構成ファイルを更新できます。</span><span class="sxs-lookup"><span data-stu-id="0351e-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="0351e-161">次の理由により、展開間で構成ファイルを編集しても意味がありません。</span><span class="sxs-lookup"><span data-stu-id="0351e-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="0351e-162">ユーザーには、引き続き使用するファイルのキャッシュされたバージョンがあります。</span><span class="sxs-lookup"><span data-stu-id="0351e-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="0351e-163">PWA の *service-worker.js* ファイルと *service-worker-assets.js* ファイルは、コンパイル時に再構築される必要があります。これにより、ユーザーの次回のオンライン アクセス時に、アプリが再展開されたことが通知されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="0351e-164">PWA によるバックグラウンド更新の処理方法の詳細については、「<xref:blazor/progressive-web-app#background-updates>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="0351e-165">ログの記録</span><span class="sxs-lookup"><span data-stu-id="0351e-165">Logging</span></span>

<span data-ttu-id="0351e-166">Blazor WebAssembly ログ記録の詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="0351e-167"> サーバー</span><span class="sxs-lookup"><span data-stu-id="0351e-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="0351e-168">UI に接続状態を反映する</span><span class="sxs-lookup"><span data-stu-id="0351e-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="0351e-169">接続が失われたことがクライアントで検出されると、クライアントによって再接続が試行される間、ユーザーに対して既定の UI が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="0351e-170">再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="0351e-171">UI をカスタマイズするには、 *_Host.cshtml* Razor ページの `<body>` に、`components-reconnect-modal` の `id` を持つ要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="0351e-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="0351e-172">次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。</span><span class="sxs-lookup"><span data-stu-id="0351e-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="0351e-173">CSS クラス</span><span class="sxs-lookup"><span data-stu-id="0351e-173">CSS class</span></span>                       | <span data-ttu-id="0351e-174">示す内容&hellip;</span><span class="sxs-lookup"><span data-stu-id="0351e-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="0351e-175">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-177">'Blazor'</span></span>
- <span data-ttu-id="0351e-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-178">'Identity'</span></span>
- <span data-ttu-id="0351e-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-180">'Razor'</span></span>
- <span data-ttu-id="0351e-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-182">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-184">'Blazor'</span></span>
- <span data-ttu-id="0351e-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-185">'Identity'</span></span>
- <span data-ttu-id="0351e-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-187">'Razor'</span></span>
- <span data-ttu-id="0351e-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-189">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-191">'Blazor'</span></span>
- <span data-ttu-id="0351e-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-192">'Identity'</span></span>
- <span data-ttu-id="0351e-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-194">'Razor'</span></span>
- <span data-ttu-id="0351e-195">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-196">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-198">'Blazor'</span></span>
- <span data-ttu-id="0351e-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-199">'Identity'</span></span>
- <span data-ttu-id="0351e-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-201">'Razor'</span></span>
- <span data-ttu-id="0351e-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-203">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-205">'Blazor'</span></span>
- <span data-ttu-id="0351e-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-206">'Identity'</span></span>
- <span data-ttu-id="0351e-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-208">'Razor'</span></span>
- <span data-ttu-id="0351e-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-210">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-212">'Blazor'</span></span>
- <span data-ttu-id="0351e-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-213">'Identity'</span></span>
- <span data-ttu-id="0351e-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-215">'Razor'</span></span>
- <span data-ttu-id="0351e-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-217">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-219">'Blazor'</span></span>
- <span data-ttu-id="0351e-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-220">'Identity'</span></span>
- <span data-ttu-id="0351e-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-222">'Razor'</span></span>
- <span data-ttu-id="0351e-223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-224">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-226">'Blazor'</span></span>
- <span data-ttu-id="0351e-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-227">'Identity'</span></span>
- <span data-ttu-id="0351e-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-229">'Razor'</span></span>
- <span data-ttu-id="0351e-230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-231">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-233">'Blazor'</span></span>
- <span data-ttu-id="0351e-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-234">'Identity'</span></span>
- <span data-ttu-id="0351e-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-236">'Razor'</span></span>
- <span data-ttu-id="0351e-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-238">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-240">'Blazor'</span></span>
- <span data-ttu-id="0351e-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-241">'Identity'</span></span>
- <span data-ttu-id="0351e-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-243">'Razor'</span></span>
- <span data-ttu-id="0351e-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-245">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-247">'Blazor'</span></span>
- <span data-ttu-id="0351e-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-248">'Identity'</span></span>
- <span data-ttu-id="0351e-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-250">'Razor'</span></span>
- <span data-ttu-id="0351e-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-252">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-254">'Blazor'</span></span>
- <span data-ttu-id="0351e-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-255">'Identity'</span></span>
- <span data-ttu-id="0351e-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-257">'Razor'</span></span>
- <span data-ttu-id="0351e-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-259">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-261">'Blazor'</span></span>
- <span data-ttu-id="0351e-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-262">'Identity'</span></span>
- <span data-ttu-id="0351e-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-264">'Razor'</span></span>
- <span data-ttu-id="0351e-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-265">'SignalR' uid:</span></span> 

<span data-ttu-id="0351e-266">---------------- | --- title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-268">'Blazor'</span></span>
- <span data-ttu-id="0351e-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-269">'Identity'</span></span>
- <span data-ttu-id="0351e-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-271">'Razor'</span></span>
- <span data-ttu-id="0351e-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-273">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-275">'Blazor'</span></span>
- <span data-ttu-id="0351e-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-276">'Identity'</span></span>
- <span data-ttu-id="0351e-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-278">'Razor'</span></span>
- <span data-ttu-id="0351e-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-280">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-282">'Blazor'</span></span>
- <span data-ttu-id="0351e-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-283">'Identity'</span></span>
- <span data-ttu-id="0351e-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-285">'Razor'</span></span>
- <span data-ttu-id="0351e-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-287">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-289">'Blazor'</span></span>
- <span data-ttu-id="0351e-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-290">'Identity'</span></span>
- <span data-ttu-id="0351e-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-292">'Razor'</span></span>
- <span data-ttu-id="0351e-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-294">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-296">'Blazor'</span></span>
- <span data-ttu-id="0351e-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-297">'Identity'</span></span>
- <span data-ttu-id="0351e-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-299">'Razor'</span></span>
- <span data-ttu-id="0351e-300">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="0351e-301">title:'ASP.NET Core Blazor ホスティング モデルの構成' author: description:'Razor コンポーネントを Razor Pages および MVC アプリに統合する方法など、Blazor ホスティング モデルの構成について学習します。'</span><span class="sxs-lookup"><span data-stu-id="0351e-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="0351e-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="0351e-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0351e-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0351e-303">'Blazor'</span></span>
- <span data-ttu-id="0351e-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0351e-304">'Identity'</span></span>
- <span data-ttu-id="0351e-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0351e-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="0351e-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0351e-306">'Razor'</span></span>
- <span data-ttu-id="0351e-307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="0351e-307">'SignalR' uid:</span></span> 

<span data-ttu-id="0351e-308">--------- | | `components-reconnect-show`     | 接続が失われました。</span><span class="sxs-lookup"><span data-stu-id="0351e-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="0351e-309">クライアントによって再接続が試行されています。</span><span class="sxs-lookup"><span data-stu-id="0351e-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="0351e-310">モーダルを表示します。</span><span class="sxs-lookup"><span data-stu-id="0351e-310">Show the modal.</span></span> <span data-ttu-id="0351e-311">| | `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="0351e-312">モーダルを非表示にします。</span><span class="sxs-lookup"><span data-stu-id="0351e-312">Hide the modal.</span></span> <span data-ttu-id="0351e-313">| | `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0351e-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="0351e-314">再接続を試みるには、`window.Blazor.reconnect()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0351e-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="0351e-315">| | `components-reconnect-rejected` | 再接続が拒否されました。</span><span class="sxs-lookup"><span data-stu-id="0351e-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="0351e-316">サーバーに到達したが接続が拒否されたため、サーバー上のユーザーの状態が失われました。</span><span class="sxs-lookup"><span data-stu-id="0351e-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="0351e-317">アプリを再度読み込むには、`location.reload()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0351e-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="0351e-318">この接続状態は、次の場合に発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0351e-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="0351e-319">サーバー側回線でクラッシュが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="0351e-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="0351e-320">クライアントが長時間切断されているため、サーバーでユーザーの状態が削除された場合。</span><span class="sxs-lookup"><span data-stu-id="0351e-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="0351e-321">ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="0351e-322">サーバーが再起動されたか、アプリのワーカー プロセスがリサイクルされた場合。</span><span class="sxs-lookup"><span data-stu-id="0351e-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="0351e-323">表示モード</span><span class="sxs-lookup"><span data-stu-id="0351e-323">Render mode</span></span>

Blazor<span data-ttu-id="0351e-324"> サーバー アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI をプリレンダリングするよう既定で設定されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="0351e-325">これは、 *_Host.cshtml* Razor ページで設定されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="0351e-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="0351e-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="0351e-327">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="0351e-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="0351e-328">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="0351e-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="0351e-329">説明</span><span class="sxs-lookup"><span data-stu-id="0351e-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="0351e-330">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="0351e-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="0351e-331">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="0351e-332">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="0351e-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="0351e-333">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="0351e-333">Output from the component isn't included.</span></span> <span data-ttu-id="0351e-334">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="0351e-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="0351e-335">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="0351e-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="0351e-336">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="0351e-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="0351e-337">Blazor サーバー アプリ用に SignalR クライアントを構成する</span><span class="sxs-lookup"><span data-stu-id="0351e-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="0351e-338">場合によっては、Blazor サーバー アプリによって使用される SignalR クライアントを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0351e-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="0351e-339">たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成できます。</span><span class="sxs-lookup"><span data-stu-id="0351e-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="0351e-340">*Pages/_Host* ファイルで SignalR クライアントを構成するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="0351e-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="0351e-341">`blazor.server.js` スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="0351e-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="0351e-342">`Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="0351e-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="0351e-343">ログの記録</span><span class="sxs-lookup"><span data-stu-id="0351e-343">Logging</span></span>

<span data-ttu-id="0351e-344">Blazor サーバー ログのサポートの詳細については、「<xref:fundamentals/logging/index#create-logs-in-blazor>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0351e-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
