---
<span data-ttu-id="95c41-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-102">'Blazor'</span></span>
- <span data-ttu-id="95c41-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-103">'Identity'</span></span>
- <span data-ttu-id="95c41-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-105">'Razor'</span></span>
- <span data-ttu-id="95c41-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-module"></a><span data-ttu-id="95c41-107">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="95c41-107">ASP.NET Core Module</span></span>

<span data-ttu-id="95c41-108">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="95c41-108">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="95c41-109">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-109">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="95c41-110">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="95c41-110">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="95c41-111">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="95c41-111">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="95c41-112">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="95c41-112">Supported Windows versions:</span></span>

* <span data-ttu-id="95c41-113">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-113">Windows 7 or later</span></span>
* <span data-ttu-id="95c41-114">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-114">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="95c41-115">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-115">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="95c41-116">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="95c41-116">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="95c41-117">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="95c41-117">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="95c41-118">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-118">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="95c41-119">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-119">In-process hosting model</span></span>

<span data-ttu-id="95c41-120">ASP.NET Core アプリの既定はインプロセス ホスティング モデルです。</span><span class="sxs-lookup"><span data-stu-id="95c41-120">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="95c41-121">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-121">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="95c41-122">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-122">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="95c41-123">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-123">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="95c41-124">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="95c41-124">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="95c41-125">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-125">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="95c41-126">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-126">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="95c41-127">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-127">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="95c41-128">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-128">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="95c41-129">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="95c41-129">Use one app pool per app.</span></span>

* <span data-ttu-id="95c41-130">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-130">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="95c41-131">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-131">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="95c41-132">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-132">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="95c41-133">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-133">Client disconnects are detected.</span></span> <span data-ttu-id="95c41-134">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="95c41-135">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="95c41-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="95c41-136">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="95c41-137">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="95c41-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="95c41-138"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="95c41-139">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="95c41-140">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="95c41-141"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="95c41-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="95c41-142">[Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-142">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="95c41-143">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-143">Out-of-process hosting model</span></span>

<span data-ttu-id="95c41-144">アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( *.csproj*) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-144">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (*.csproj*):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="95c41-145">インプロセス ホスティングは `InProcess` で設定され、これが既定値です。</span><span class="sxs-lookup"><span data-stu-id="95c41-145">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="95c41-146">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="95c41-146">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="95c41-147">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="95c41-148">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="95c41-149">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="95c41-150">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="95c41-151">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="95c41-151">Hosting model changes</span></span>

<span data-ttu-id="95c41-152">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="95c41-153">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="95c41-154">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="95c41-155">プロセス名</span><span class="sxs-lookup"><span data-stu-id="95c41-155">Process name</span></span>

<span data-ttu-id="95c41-156">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="95c41-157">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="95c41-157">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="95c41-158">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-158">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="95c41-159">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="95c41-159">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="95c41-160">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="95c41-160">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="95c41-161">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="95c41-161">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="95c41-162">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="95c41-162">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="95c41-163">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="95c41-163">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="95c41-164">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-164">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="95c41-165">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="95c41-165">Configuration with web.config</span></span>

<span data-ttu-id="95c41-166">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-166">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="95c41-167">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-167">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="95c41-168">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="95c41-168">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="95c41-169"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="95c41-169">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="95c41-170">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-170">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-171">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="95c41-171">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="95c41-172">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-172">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="95c41-173">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="95c41-173">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="95c41-174">属性</span><span class="sxs-lookup"><span data-stu-id="95c41-174">Attribute</span></span> | <span data-ttu-id="95c41-175">説明</span><span class="sxs-lookup"><span data-stu-id="95c41-175">Description</span></span> | <span data-ttu-id="95c41-176">Default</span><span class="sxs-lookup"><span data-stu-id="95c41-176">Default</span></span> |
| ---
<span data-ttu-id="95c41-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-178">'Blazor'</span></span>
- <span data-ttu-id="95c41-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-179">'Identity'</span></span>
- <span data-ttu-id="95c41-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-181">'Razor'</span></span>
- <span data-ttu-id="95c41-182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-184">'Blazor'</span></span>
- <span data-ttu-id="95c41-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-185">'Identity'</span></span>
- <span data-ttu-id="95c41-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-187">'Razor'</span></span>
- <span data-ttu-id="95c41-188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-188">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-189">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-190">'Blazor'</span></span>
- <span data-ttu-id="95c41-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-191">'Identity'</span></span>
- <span data-ttu-id="95c41-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-193">'Razor'</span></span>
- <span data-ttu-id="95c41-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-196">'Blazor'</span></span>
- <span data-ttu-id="95c41-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-197">'Identity'</span></span>
- <span data-ttu-id="95c41-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-199">'Razor'</span></span>
- <span data-ttu-id="95c41-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-202">'Blazor'</span></span>
- <span data-ttu-id="95c41-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-203">'Identity'</span></span>
- <span data-ttu-id="95c41-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-205">'Razor'</span></span>
- <span data-ttu-id="95c41-206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-206">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-207">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="95c41-207">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="95c41-208">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-208">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-209">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="95c41-209">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="95c41-210">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="95c41-210">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="95c41-211">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-211">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-212">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-212">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="95c41-213">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="95c41-213">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="95c41-214">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-214">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-215">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-215">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="95c41-216">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-216">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="95c41-217">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="95c41-217">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="95c41-218">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-218">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-219">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-219">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br><span data-ttu-id="95c41-220">`inprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="95c41-220">`inprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="95c41-221">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-222">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-222">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="95c41-223">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-223">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="95c41-224">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-224">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="95c41-225">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-225">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="95c41-226">| 既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-226">| Default: `1`</span></span><br><span data-ttu-id="95c41-227">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-227">Min: `1`</span></span><br><span data-ttu-id="95c41-228">最大値: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="95c41-228">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="95c41-229">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-229">Required string attribute.</span></span></p><p><span data-ttu-id="95c41-230">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="95c41-230">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="95c41-231">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="95c41-231">Relative paths are supported.</span></span> <span data-ttu-id="95c41-232">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-232">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="95c41-233">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="95c41-233">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="95c41-234">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-235">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-235">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="95c41-236">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="95c41-236">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="95c41-237">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-237">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="95c41-238">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-238">| Default: `10`</span></span><br><span data-ttu-id="95c41-239">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-239">Min: `0`</span></span><br><span data-ttu-id="95c41-240">最大値: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="95c41-240">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="95c41-241">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-241">Optional timespan attribute.</span></span></p><p><span data-ttu-id="95c41-242">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-242">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="95c41-243">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-243">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="95c41-244">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-244">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="95c41-245">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="95c41-245">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="95c41-246">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="95c41-246">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="95c41-247">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="95c41-247">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="95c41-248">| 既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-248">| Default: `00:02:00`</span></span><br><span data-ttu-id="95c41-249">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-249">Min: `00:00:00`</span></span><br><span data-ttu-id="95c41-250">最大値: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-250">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="95c41-251">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-251">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-252">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-252">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="95c41-253">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-253">| Default: `10`</span></span><br><span data-ttu-id="95c41-254">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-254">Min: `0`</span></span><br><span data-ttu-id="95c41-255">最大値: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-255">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="95c41-256">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-256">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-257">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-257">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="95c41-258">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-258">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="95c41-259">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="95c41-259">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="95c41-260">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="95c41-260">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="95c41-261">| 既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="95c41-261">| Default: `120`</span></span><br><span data-ttu-id="95c41-262">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-262">Min: `0`</span></span><br><span data-ttu-id="95c41-263">最大値: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="95c41-263">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="95c41-264">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-264">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-265">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-265">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="95c41-266">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="95c41-266">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="95c41-267">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-267">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-268">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-268">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="95c41-269">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="95c41-269">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="95c41-270">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="95c41-270">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="95c41-271">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-271">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-272">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-272">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="95c41-273">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-273">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="95c41-274">環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="95c41-274">Set environment variables</span></span>

<span data-ttu-id="95c41-275">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-275">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="95c41-276">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-276">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="95c41-277">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-277">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="95c41-278">次の例では、*web.config* で 2 つの環境変数を設定します。`ASPNETCORE_ENVIRONMENT` では、アプリの環境が `Development` に構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-278">The following example sets two environment variables in *web.config*. `ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="95c41-279">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-279">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="95c41-280">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="95c41-280">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="95c41-281">*web.config* 内で環境を直接設定する代わりに、[発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="95c41-281">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="95c41-282">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-282">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="95c41-283">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="95c41-283">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="95c41-284">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="95c41-284">app_offline.htm</span></span>

<span data-ttu-id="95c41-285">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="95c41-285">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="95c41-286">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-286">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="95c41-287">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="95c41-287">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="95c41-288">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-288">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="95c41-289">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-289">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="95c41-290">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-290">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="95c41-291">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="95c41-291">Start-up error page</span></span>

<span data-ttu-id="95c41-292">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-292">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="95c41-293">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-293">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-294">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-294">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-295">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-295">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-296">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-296">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="95c41-297">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー \<httpErrors>](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-297">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="95c41-298">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="95c41-298">Log creation and redirection</span></span>

<span data-ttu-id="95c41-299">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-299">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="95c41-300">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-300">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-301">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="95c41-301">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="95c41-302">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-302">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="95c41-303">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-303">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="95c41-304">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-304">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="95c41-305">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="95c41-305">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="95c41-306">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-306">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="95c41-307">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-307">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="95c41-308">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-308">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="95c41-309">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-309">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="95c41-310">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="95c41-310">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="95c41-311">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-311">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="95c41-312">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-312">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="95c41-313">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-313">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="95c41-314">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-314">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="95c41-315">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-315">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-316">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-316">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="95c41-317">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-317">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="95c41-318">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-318">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="95c41-319">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="95c41-319">Enhanced diagnostic logs</span></span>

<span data-ttu-id="95c41-320">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-320">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="95c41-321">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="95c41-321">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="95c41-322">パスに含まれるフォルダー (前の例では *logs*) は、ログ ファイルの作成時に、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-322">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-323">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="95c41-323">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="95c41-324">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="95c41-324">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="95c41-325">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="95c41-325">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="95c41-326">ERROR</span><span class="sxs-lookup"><span data-stu-id="95c41-326">ERROR</span></span>
* <span data-ttu-id="95c41-327">WARNING</span><span class="sxs-lookup"><span data-stu-id="95c41-327">WARNING</span></span>
* <span data-ttu-id="95c41-328">INFO</span><span class="sxs-lookup"><span data-stu-id="95c41-328">INFO</span></span>
* <span data-ttu-id="95c41-329">TRACE</span><span class="sxs-lookup"><span data-stu-id="95c41-329">TRACE</span></span>

<span data-ttu-id="95c41-330">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="95c41-330">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="95c41-331">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="95c41-331">CONSOLE</span></span>
* <span data-ttu-id="95c41-332">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="95c41-332">EVENTLOG</span></span>
* <span data-ttu-id="95c41-333">ファイル</span><span class="sxs-lookup"><span data-stu-id="95c41-333">FILE</span></span>

<span data-ttu-id="95c41-334">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="95c41-334">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="95c41-335">`ASPNETCORE_MODULE_DEBUG_FILE`:デバッグ ログ ファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="95c41-335">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="95c41-336">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="95c41-336">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="95c41-337">`ASPNETCORE_MODULE_DEBUG`:デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="95c41-337">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="95c41-338">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="95c41-338">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="95c41-339">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-339">The size of the log isn't limited.</span></span> <span data-ttu-id="95c41-340">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-340">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="95c41-341">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-341">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="95c41-342">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="95c41-342">Modify the stack size</span></span>

<span data-ttu-id="95c41-343">"*インプロセス ホスティング モデルを使用している場合にのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="95c41-343">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="95c41-344">*web.config* で `stackSize` の設定を使用してマネージド スタックのサイズを構成します (バイト単位)。既定のサイズは `1048576` バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="95c41-344">Configure the managed stack size using the `stackSize` setting in bytes in *web.config*. The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="95c41-345">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="95c41-345">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="95c41-346">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="95c41-346">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="95c41-347">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-347">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="95c41-348">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-348">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="95c41-349">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-349">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="95c41-350">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-350">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="95c41-351">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-351">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="95c41-352">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="95c41-352">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="95c41-353">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-353">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="95c41-354">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="95c41-354">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="95c41-355">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="95c41-355">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="95c41-356">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="95c41-356">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="95c41-357">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-357">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="95c41-358">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-358">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="95c41-359">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-359">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="95c41-360">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-360">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="95c41-361">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-361">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="95c41-362">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-362">Run the installer.</span></span>
1. <span data-ttu-id="95c41-363">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="95c41-363">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="95c41-364">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-364">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="95c41-365">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="95c41-365">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="95c41-366">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="95c41-366">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="95c41-367">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="95c41-367">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="95c41-368">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="95c41-368">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="95c41-369">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="95c41-369">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="95c41-370">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="95c41-370">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="95c41-371">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="95c41-371">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="95c41-372">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="95c41-372">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="95c41-373">Module</span><span class="sxs-lookup"><span data-stu-id="95c41-373">Module</span></span>

<span data-ttu-id="95c41-374">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-374">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-375">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-375">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-376">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-377">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="95c41-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-378">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="95c41-379">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-379">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-380">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-381">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-382">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="95c41-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-383">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="95c41-384">Schema</span><span class="sxs-lookup"><span data-stu-id="95c41-384">Schema</span></span>

<span data-ttu-id="95c41-385">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-385">**IIS**</span></span>

* <span data-ttu-id="95c41-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-386">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="95c41-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-387">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="95c41-388">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-388">**IIS Express**</span></span>

* <span data-ttu-id="95c41-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-389">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="95c41-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-390">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="95c41-391">構成</span><span class="sxs-lookup"><span data-stu-id="95c41-391">Configuration</span></span>

<span data-ttu-id="95c41-392">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-392">**IIS**</span></span>

* <span data-ttu-id="95c41-393">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-393">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="95c41-394">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-394">**IIS Express**</span></span>

* <span data-ttu-id="95c41-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-395">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="95c41-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-396">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="95c41-397">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="95c41-397">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="95c41-398">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-398">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="95c41-399">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="95c41-399">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="95c41-400">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="95c41-400">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="95c41-401">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="95c41-401">Supported Windows versions:</span></span>

* <span data-ttu-id="95c41-402">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-402">Windows 7 or later</span></span>
* <span data-ttu-id="95c41-403">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-403">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="95c41-404">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-404">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="95c41-405">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="95c41-405">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="95c41-406">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="95c41-406">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="95c41-407">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-407">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="95c41-408">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-408">In-process hosting model</span></span>

<span data-ttu-id="95c41-409">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="95c41-409">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="95c41-410">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-410">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="95c41-411">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="95c41-411">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="95c41-412">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="95c41-412">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="95c41-413">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-413">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="95c41-414">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-414">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="95c41-415">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-415">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="95c41-416">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="95c41-416">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="95c41-417">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-417">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="95c41-418">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-418">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="95c41-419">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-419">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="95c41-420">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-420">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="95c41-421">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="95c41-421">Use one app pool per app.</span></span>

* <span data-ttu-id="95c41-422">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-422">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="95c41-423">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-423">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="95c41-424">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-424">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="95c41-425">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-425">Client disconnects are detected.</span></span> <span data-ttu-id="95c41-426">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-426">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="95c41-427">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="95c41-427">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="95c41-428">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-428">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="95c41-429">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="95c41-429">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="95c41-430"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-430">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="95c41-431">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-431">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="95c41-432">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-432">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="95c41-433"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="95c41-433">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="95c41-434">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="95c41-434">Out-of-process hosting model</span></span>

<span data-ttu-id="95c41-435">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="95c41-435">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="95c41-436">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="95c41-436">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="95c41-437">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="95c41-437">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="95c41-438">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="95c41-438">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="95c41-439">この値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="95c41-439">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="95c41-440">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-440">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="95c41-441">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-441">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="95c41-442">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-442">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="95c41-443">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-443">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="95c41-444">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="95c41-444">Hosting model changes</span></span>

<span data-ttu-id="95c41-445">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-445">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="95c41-446">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-446">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="95c41-447">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-447">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="95c41-448">プロセス名</span><span class="sxs-lookup"><span data-stu-id="95c41-448">Process name</span></span>

<span data-ttu-id="95c41-449">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-449">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="95c41-450">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="95c41-450">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="95c41-451">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-451">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="95c41-452">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="95c41-452">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="95c41-453">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="95c41-453">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="95c41-454">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="95c41-454">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="95c41-455">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="95c41-455">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="95c41-456">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="95c41-456">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="95c41-457">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-457">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="95c41-458">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="95c41-458">Configuration with web.config</span></span>

<span data-ttu-id="95c41-459">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-459">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="95c41-460">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-460">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="95c41-461">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="95c41-461">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="95c41-462"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="95c41-462">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="95c41-463">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-463">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-464">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="95c41-464">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="95c41-465">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-465">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="95c41-466">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="95c41-466">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="95c41-467">属性</span><span class="sxs-lookup"><span data-stu-id="95c41-467">Attribute</span></span> | <span data-ttu-id="95c41-468">説明</span><span class="sxs-lookup"><span data-stu-id="95c41-468">Description</span></span> | <span data-ttu-id="95c41-469">Default</span><span class="sxs-lookup"><span data-stu-id="95c41-469">Default</span></span> |
| ---
<span data-ttu-id="95c41-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-471">'Blazor'</span></span>
- <span data-ttu-id="95c41-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-472">'Identity'</span></span>
- <span data-ttu-id="95c41-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-474">'Razor'</span></span>
- <span data-ttu-id="95c41-475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-477">'Blazor'</span></span>
- <span data-ttu-id="95c41-478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-478">'Identity'</span></span>
- <span data-ttu-id="95c41-479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-479">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-480">'Razor'</span></span>
- <span data-ttu-id="95c41-481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-481">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-482">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-483">'Blazor'</span></span>
- <span data-ttu-id="95c41-484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-484">'Identity'</span></span>
- <span data-ttu-id="95c41-485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-485">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-486">'Razor'</span></span>
- <span data-ttu-id="95c41-487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-489">'Blazor'</span></span>
- <span data-ttu-id="95c41-490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-490">'Identity'</span></span>
- <span data-ttu-id="95c41-491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-491">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-492">'Razor'</span></span>
- <span data-ttu-id="95c41-493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-495">'Blazor'</span></span>
- <span data-ttu-id="95c41-496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-496">'Identity'</span></span>
- <span data-ttu-id="95c41-497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-497">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-498">'Razor'</span></span>
- <span data-ttu-id="95c41-499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-499">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-500">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="95c41-500">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="95c41-501">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-501">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-502">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="95c41-502">Arguments to the executable specified in **processPath**.</span></span></p> <span data-ttu-id="95c41-503">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="95c41-503">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="95c41-504">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-504">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-505">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-505">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="95c41-506">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="95c41-506">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="95c41-507">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-507">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-508">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-508">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="95c41-509">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-509">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="95c41-510">| `true` | | `hostingModel` | </span><span class="sxs-lookup"><span data-stu-id="95c41-510">| `true` | | `hostingModel` | </span></span><p><span data-ttu-id="95c41-511">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-511">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-512">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-512">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br><span data-ttu-id="95c41-513">`outofprocess` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="95c41-513">`outofprocess` | | `processesPerApplication` | </span></span><p><span data-ttu-id="95c41-514">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-514">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-515">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-515">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="95c41-516">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-516">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="95c41-517">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-517">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="95c41-518">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-518">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="95c41-519">| 既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-519">| Default: `1`</span></span><br><span data-ttu-id="95c41-520">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-520">Min: `1`</span></span><br><span data-ttu-id="95c41-521">最大値: `100`&dagger; | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="95c41-521">Max: `100`&dagger; | | `processPath` | </span></span><p><span data-ttu-id="95c41-522">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-522">Required string attribute.</span></span></p><p><span data-ttu-id="95c41-523">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="95c41-523">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="95c41-524">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="95c41-524">Relative paths are supported.</span></span> <span data-ttu-id="95c41-525">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-525">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="95c41-526">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="95c41-526">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="95c41-527">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-527">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-528">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-528">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="95c41-529">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="95c41-529">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="95c41-530">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-530">Not supported with in-process hosting.</span></span></p> <span data-ttu-id="95c41-531">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-531">| Default: `10`</span></span><br><span data-ttu-id="95c41-532">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-532">Min: `0`</span></span><br><span data-ttu-id="95c41-533">最大値: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="95c41-533">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="95c41-534">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-534">Optional timespan attribute.</span></span></p><p><span data-ttu-id="95c41-535">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-535">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="95c41-536">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-536">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="95c41-537">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-537">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="95c41-538">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="95c41-538">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="95c41-539">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="95c41-539">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="95c41-540">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="95c41-540">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> <span data-ttu-id="95c41-541">| 既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-541">| Default: `00:02:00`</span></span><br><span data-ttu-id="95c41-542">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-542">Min: `00:00:00`</span></span><br><span data-ttu-id="95c41-543">最大値: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-543">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="95c41-544">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-544">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-545">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-545">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="95c41-546">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-546">| Default: `10`</span></span><br><span data-ttu-id="95c41-547">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-547">Min: `0`</span></span><br><span data-ttu-id="95c41-548">最大値: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-548">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="95c41-549">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-549">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-550">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-550">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="95c41-551">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-551">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="95c41-552">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="95c41-552">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="95c41-553">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="95c41-553">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="95c41-554">| 既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="95c41-554">| Default: `120`</span></span><br><span data-ttu-id="95c41-555">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-555">Min: `0`</span></span><br><span data-ttu-id="95c41-556">最大値: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="95c41-556">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="95c41-557">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-557">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-558">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-558">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="95c41-559">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="95c41-559">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="95c41-560">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-560">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-561">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-561">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="95c41-562">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="95c41-562">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="95c41-563">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="95c41-563">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="95c41-564">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-564">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-565">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-565">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="95c41-566">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-566">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="95c41-567">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="95c41-567">Setting environment variables</span></span>

<span data-ttu-id="95c41-568">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-568">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="95c41-569">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-569">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="95c41-570">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-570">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="95c41-571">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="95c41-571">The following example sets two environment variables.</span></span> <span data-ttu-id="95c41-572">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-572">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="95c41-573">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-573">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="95c41-574">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="95c41-574">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="95c41-575">*web.config* 内で環境を直接設定する代わりに、[発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="95c41-575">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="95c41-576">この方法では、プロジェクトが発行されるときに *web.config* に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-576">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="95c41-577">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="95c41-577">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="95c41-578">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="95c41-578">app_offline.htm</span></span>

<span data-ttu-id="95c41-579">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="95c41-579">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="95c41-580">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-580">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="95c41-581">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="95c41-581">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="95c41-582">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-582">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

<span data-ttu-id="95c41-583">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-583">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="95c41-584">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-584">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="95c41-585">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="95c41-585">Start-up error page</span></span>

<span data-ttu-id="95c41-586">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-586">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="95c41-587">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-587">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-588">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-588">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-589">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-589">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="95c41-590">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-590">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="95c41-591">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー \<httpErrors>](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-591">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="95c41-592">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="95c41-592">Log creation and redirection</span></span>

<span data-ttu-id="95c41-593">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-593">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="95c41-594">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-594">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-595">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="95c41-595">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="95c41-596">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-596">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="95c41-597">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-597">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="95c41-598">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-598">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="95c41-599">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="95c41-599">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="95c41-600">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-600">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="95c41-601">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-601">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="95c41-602">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-602">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="95c41-603">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-603">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="95c41-604">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="95c41-604">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="95c41-605">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-605">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="95c41-606">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-606">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="95c41-607">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-607">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="95c41-608">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-608">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="95c41-609">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-609">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-610">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-610">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="95c41-611">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-611">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="95c41-612">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="95c41-612">Enhanced diagnostic logs</span></span>

<span data-ttu-id="95c41-613">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-613">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="95c41-614">*web.config* で、`<aspNetCore>` 要素に `<handlerSettings>` 要素を追加します。`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="95c41-614">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="95c41-615">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では *logs*) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-615">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="95c41-616">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="95c41-616">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="95c41-617">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="95c41-617">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="95c41-618">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="95c41-618">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="95c41-619">ERROR</span><span class="sxs-lookup"><span data-stu-id="95c41-619">ERROR</span></span>
* <span data-ttu-id="95c41-620">WARNING</span><span class="sxs-lookup"><span data-stu-id="95c41-620">WARNING</span></span>
* <span data-ttu-id="95c41-621">INFO</span><span class="sxs-lookup"><span data-stu-id="95c41-621">INFO</span></span>
* <span data-ttu-id="95c41-622">TRACE</span><span class="sxs-lookup"><span data-stu-id="95c41-622">TRACE</span></span>

<span data-ttu-id="95c41-623">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="95c41-623">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="95c41-624">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="95c41-624">CONSOLE</span></span>
* <span data-ttu-id="95c41-625">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="95c41-625">EVENTLOG</span></span>
* <span data-ttu-id="95c41-626">ファイル</span><span class="sxs-lookup"><span data-stu-id="95c41-626">FILE</span></span>

<span data-ttu-id="95c41-627">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="95c41-627">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="95c41-628">`ASPNETCORE_MODULE_DEBUG_FILE`:デバッグ ログ ファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="95c41-628">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="95c41-629">(既定: *aspnetcore debug.log*)。</span><span class="sxs-lookup"><span data-stu-id="95c41-629">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="95c41-630">`ASPNETCORE_MODULE_DEBUG`:デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="95c41-630">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="95c41-631">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="95c41-631">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="95c41-632">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="95c41-632">The size of the log isn't limited.</span></span> <span data-ttu-id="95c41-633">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-633">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="95c41-634">*web.config* ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-634">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="95c41-635">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="95c41-635">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="95c41-636">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="95c41-636">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="95c41-637">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-637">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="95c41-638">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-638">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="95c41-639">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-639">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="95c41-640">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-640">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="95c41-641">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-641">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="95c41-642">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="95c41-642">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="95c41-643">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-643">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="95c41-644">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="95c41-644">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="95c41-645">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="95c41-645">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="95c41-646">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="95c41-646">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="95c41-647">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-647">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="95c41-648">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-648">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="95c41-649">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-649">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="95c41-650">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-650">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="95c41-651">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-651">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="95c41-652">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-652">Run the installer.</span></span>
1. <span data-ttu-id="95c41-653">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="95c41-653">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="95c41-654">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-654">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="95c41-655">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="95c41-655">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="95c41-656">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="95c41-656">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="95c41-657">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="95c41-657">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="95c41-658">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="95c41-658">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="95c41-659">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="95c41-659">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="95c41-660">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="95c41-660">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="95c41-661">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="95c41-661">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="95c41-662">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="95c41-662">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="95c41-663">Module</span><span class="sxs-lookup"><span data-stu-id="95c41-663">Module</span></span>

<span data-ttu-id="95c41-664">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-664">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-665">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-665">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-666">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-667">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="95c41-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-668">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

<span data-ttu-id="95c41-669">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-669">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-670">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-671">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-672">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="95c41-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-673">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

### <a name="schema"></a><span data-ttu-id="95c41-674">Schema</span><span class="sxs-lookup"><span data-stu-id="95c41-674">Schema</span></span>

<span data-ttu-id="95c41-675">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-675">**IIS**</span></span>

* <span data-ttu-id="95c41-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-676">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="95c41-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-677">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

<span data-ttu-id="95c41-678">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-678">**IIS Express**</span></span>

* <span data-ttu-id="95c41-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-679">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

* <span data-ttu-id="95c41-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-680">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="95c41-681">構成</span><span class="sxs-lookup"><span data-stu-id="95c41-681">Configuration</span></span>

<span data-ttu-id="95c41-682">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-682">**IIS**</span></span>

* <span data-ttu-id="95c41-683">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-683">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="95c41-684">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-684">**IIS Express**</span></span>

* <span data-ttu-id="95c41-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-685">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="95c41-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-686">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="95c41-687">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="95c41-687">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="95c41-688">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-688">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="95c41-689">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="95c41-689">Supported Windows versions:</span></span>

* <span data-ttu-id="95c41-690">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-690">Windows 7 or later</span></span>
* <span data-ttu-id="95c41-691">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="95c41-691">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="95c41-692">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="95c41-692">The module only works with Kestrel.</span></span> <span data-ttu-id="95c41-693">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-693">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="95c41-694">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="95c41-694">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="95c41-695">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="95c41-695">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="95c41-696">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="95c41-696">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="95c41-697">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="95c41-697">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="95c41-699">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="95c41-699">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="95c41-700">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="95c41-700">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="95c41-701">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-701">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="95c41-702">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-702">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="95c41-703">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-703">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="95c41-704">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-704">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="95c41-705">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-705">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="95c41-706">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="95c41-706">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="95c41-707">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-707">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="95c41-708">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="95c41-708">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="95c41-709">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="95c41-709">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="95c41-710">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-710">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="95c41-711">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="95c41-711">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="95c41-712">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="95c41-712">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="95c41-713">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="95c41-713">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="95c41-714">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="95c41-714">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="95c41-715">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="95c41-715">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="95c41-716">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-716">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="95c41-717">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="95c41-717">Configuration with web.config</span></span>

<span data-ttu-id="95c41-718">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-718">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="95c41-719">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-719">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="95c41-720">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="95c41-720">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="95c41-721">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-721">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-722">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="95c41-722">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="95c41-723">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-723">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="95c41-724">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="95c41-724">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="95c41-725">属性</span><span class="sxs-lookup"><span data-stu-id="95c41-725">Attribute</span></span> | <span data-ttu-id="95c41-726">説明</span><span class="sxs-lookup"><span data-stu-id="95c41-726">Description</span></span> | <span data-ttu-id="95c41-727">Default</span><span class="sxs-lookup"><span data-stu-id="95c41-727">Default</span></span> |
| ---
<span data-ttu-id="95c41-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-728">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-729">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-729">'Blazor'</span></span>
- <span data-ttu-id="95c41-730">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-730">'Identity'</span></span>
- <span data-ttu-id="95c41-731">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-731">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-732">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-732">'Razor'</span></span>
- <span data-ttu-id="95c41-733">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-733">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-734">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-735">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-735">'Blazor'</span></span>
- <span data-ttu-id="95c41-736">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-736">'Identity'</span></span>
- <span data-ttu-id="95c41-737">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-737">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-738">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-738">'Razor'</span></span>
- <span data-ttu-id="95c41-739">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-739">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-740">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-741">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-741">'Blazor'</span></span>
- <span data-ttu-id="95c41-742">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-742">'Identity'</span></span>
- <span data-ttu-id="95c41-743">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-743">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-744">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-744">'Razor'</span></span>
- <span data-ttu-id="95c41-745">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-745">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-746">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-747">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-747">'Blazor'</span></span>
- <span data-ttu-id="95c41-748">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-748">'Identity'</span></span>
- <span data-ttu-id="95c41-749">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-749">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-750">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-750">'Razor'</span></span>
- <span data-ttu-id="95c41-751">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-751">'SignalR' uid:</span></span> 

-
<span data-ttu-id="95c41-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="95c41-752">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="95c41-753">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="95c41-753">'Blazor'</span></span>
- <span data-ttu-id="95c41-754">'Identity'</span><span class="sxs-lookup"><span data-stu-id="95c41-754">'Identity'</span></span>
- <span data-ttu-id="95c41-755">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="95c41-755">'Let's Encrypt'</span></span>
- <span data-ttu-id="95c41-756">'Razor'</span><span class="sxs-lookup"><span data-stu-id="95c41-756">'Razor'</span></span>
- <span data-ttu-id="95c41-757">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="95c41-757">'SignalR' uid:</span></span> 

<span data-ttu-id="95c41-758">------ | :-----: | | `arguments` | </span><span class="sxs-lookup"><span data-stu-id="95c41-758">------ | :-----: | | `arguments` | </span></span><p><span data-ttu-id="95c41-759">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-759">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-760">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="95c41-760">Arguments to the executable specified in **processPath**.</span></span></p><span data-ttu-id="95c41-761">| | | `disableStartUpErrorPage` | </span><span class="sxs-lookup"><span data-stu-id="95c41-761">| | | `disableStartUpErrorPage` | </span></span><p><span data-ttu-id="95c41-762">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-762">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-763">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-763">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> <span data-ttu-id="95c41-764">| `false` | | `forwardWindowsAuthToken` | </span><span class="sxs-lookup"><span data-stu-id="95c41-764">| `false` | | `forwardWindowsAuthToken` | </span></span><p><span data-ttu-id="95c41-765">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-765">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-766">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-766">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="95c41-767">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-767">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> <span data-ttu-id="95c41-768">| `true` | | `processesPerApplication` | </span><span class="sxs-lookup"><span data-stu-id="95c41-768">| `true` | | `processesPerApplication` | </span></span><p><span data-ttu-id="95c41-769">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-769">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-770">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-770">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="95c41-771">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-771">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="95c41-772">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-772">This attribute will be removed in a future release.</span></span></p> <span data-ttu-id="95c41-773">| 既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-773">| Default: `1`</span></span><br><span data-ttu-id="95c41-774">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="95c41-774">Min: `1`</span></span><br><span data-ttu-id="95c41-775">最大値: `100` | | `processPath` | </span><span class="sxs-lookup"><span data-stu-id="95c41-775">Max: `100` | | `processPath` | </span></span><p><span data-ttu-id="95c41-776">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-776">Required string attribute.</span></span></p><p><span data-ttu-id="95c41-777">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="95c41-777">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="95c41-778">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="95c41-778">Relative paths are supported.</span></span> <span data-ttu-id="95c41-779">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-779">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> <span data-ttu-id="95c41-780">| | | `rapidFailsPerMinute` | </span><span class="sxs-lookup"><span data-stu-id="95c41-780">| | | `rapidFailsPerMinute` | </span></span><p><span data-ttu-id="95c41-781">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-781">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-782">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-782">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="95c41-783">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="95c41-783">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> <span data-ttu-id="95c41-784">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-784">| Default: `10`</span></span><br><span data-ttu-id="95c41-785">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-785">Min: `0`</span></span><br><span data-ttu-id="95c41-786">最大値: `100` | | `requestTimeout` | </span><span class="sxs-lookup"><span data-stu-id="95c41-786">Max: `100` | | `requestTimeout` | </span></span><p><span data-ttu-id="95c41-787">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-787">Optional timespan attribute.</span></span></p><p><span data-ttu-id="95c41-788">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-788">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="95c41-789">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-789">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> <span data-ttu-id="95c41-790">| 既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-790">| Default: `00:02:00`</span></span><br><span data-ttu-id="95c41-791">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="95c41-791">Min: `00:00:00`</span></span><br><span data-ttu-id="95c41-792">最大値: `360:00:00` | | `shutdownTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-792">Max: `360:00:00` | | `shutdownTimeLimit` | </span></span><p><span data-ttu-id="95c41-793">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-793">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-794">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-794">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> <span data-ttu-id="95c41-795">| 既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="95c41-795">| Default: `10`</span></span><br><span data-ttu-id="95c41-796">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-796">Min: `0`</span></span><br><span data-ttu-id="95c41-797">最大値: `600` | | `startupTimeLimit` | </span><span class="sxs-lookup"><span data-stu-id="95c41-797">Max: `600` | | `startupTimeLimit` | </span></span><p><span data-ttu-id="95c41-798">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="95c41-798">Optional integer attribute.</span></span></p><p><span data-ttu-id="95c41-799">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="95c41-799">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="95c41-800">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-800">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="95c41-801">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="95c41-801">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="95c41-802">値 0 (ゼロ) は無限のタイムアウトと見なされ**ません**。</span><span class="sxs-lookup"><span data-stu-id="95c41-802">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> <span data-ttu-id="95c41-803">| 既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="95c41-803">| Default: `120`</span></span><br><span data-ttu-id="95c41-804">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="95c41-804">Min: `0`</span></span><br><span data-ttu-id="95c41-805">最大値: `3600` | | `stdoutLogEnabled` | </span><span class="sxs-lookup"><span data-stu-id="95c41-805">Max: `3600` | | `stdoutLogEnabled` | </span></span><p><span data-ttu-id="95c41-806">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="95c41-806">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="95c41-807">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-807">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> <span data-ttu-id="95c41-808">| `false` | | `stdoutLogFile` | </span><span class="sxs-lookup"><span data-stu-id="95c41-808">| `false` | | `stdoutLogFile` | </span></span><p><span data-ttu-id="95c41-809">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="95c41-809">Optional string attribute.</span></span></p><p><span data-ttu-id="95c41-810">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-810">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="95c41-811">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="95c41-811">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="95c41-812">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="95c41-812">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="95c41-813">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="95c41-813">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="95c41-814">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-814">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="95c41-815">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-815">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="95c41-816">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="95c41-816">Setting environment variables</span></span>

<span data-ttu-id="95c41-817">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-817">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="95c41-818">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="95c41-818">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="95c41-819">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="95c41-819">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="95c41-820">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="95c41-820">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="95c41-821">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="95c41-821">The following example sets two environment variables.</span></span> <span data-ttu-id="95c41-822">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="95c41-822">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="95c41-823">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-823">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="95c41-824">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="95c41-824">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="95c41-825">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="95c41-825">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="95c41-826">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="95c41-826">app_offline.htm</span></span>

<span data-ttu-id="95c41-827">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="95c41-827">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="95c41-828">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="95c41-828">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="95c41-829">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="95c41-829">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="95c41-830">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-830">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="95c41-831">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="95c41-831">Start-up error page</span></span>

<span data-ttu-id="95c41-832">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー*状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-832">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="95c41-833">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-833">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="95c41-834">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー \<httpErrors>](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-834">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 処理エラーの状態コード ページ](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a><span data-ttu-id="95c41-836">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="95c41-836">Log creation and redirection</span></span>

<span data-ttu-id="95c41-837">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-837">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="95c41-838">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-838">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="95c41-839">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="95c41-839">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="95c41-840">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-840">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="95c41-841">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="95c41-841">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="95c41-842">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="95c41-842">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="95c41-843">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="95c41-843">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="95c41-844">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-844">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="95c41-845">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="95c41-845">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="95c41-846">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-846">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="95c41-847">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-847">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="95c41-848">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="95c41-848">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="95c41-849">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-849">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="95c41-850">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-850">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="95c41-851">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-851">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="95c41-852">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-852">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="95c41-853">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-853">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="95c41-854">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="95c41-854">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="95c41-855">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="95c41-855">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="95c41-856">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="95c41-856">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="95c41-857">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="95c41-857">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="95c41-858">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="95c41-858">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="95c41-859">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-859">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="95c41-860">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-860">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="95c41-861">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="95c41-861">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="95c41-862">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="95c41-862">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="95c41-863">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="95c41-863">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="95c41-864">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="95c41-864">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="95c41-865">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="95c41-865">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="95c41-866">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-866">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="95c41-867">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="95c41-867">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="95c41-868">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="95c41-868">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="95c41-869">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-869">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="95c41-870">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="95c41-870">Run the installer.</span></span>
1. <span data-ttu-id="95c41-871">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="95c41-871">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="95c41-872">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="95c41-872">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="95c41-873">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="95c41-873">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="95c41-874">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="95c41-874">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="95c41-875">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="95c41-875">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="95c41-876">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="95c41-876">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="95c41-877">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="95c41-877">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="95c41-878">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="95c41-878">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="95c41-879">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="95c41-879">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="95c41-880">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="95c41-880">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="95c41-881">Module</span><span class="sxs-lookup"><span data-stu-id="95c41-881">Module</span></span>

<span data-ttu-id="95c41-882">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-882">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-883">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-883">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-884">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="95c41-885">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="95c41-885">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="95c41-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-886">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="95c41-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="95c41-887">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="95c41-888">Schema</span><span class="sxs-lookup"><span data-stu-id="95c41-888">Schema</span></span>

<span data-ttu-id="95c41-889">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-889">**IIS**</span></span>

* <span data-ttu-id="95c41-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-890">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="95c41-891">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-891">**IIS Express**</span></span>

* <span data-ttu-id="95c41-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="95c41-892">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="95c41-893">構成</span><span class="sxs-lookup"><span data-stu-id="95c41-893">Configuration</span></span>

<span data-ttu-id="95c41-894">**IIS**</span><span class="sxs-lookup"><span data-stu-id="95c41-894">**IIS**</span></span>

* <span data-ttu-id="95c41-895">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-895">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="95c41-896">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="95c41-896">**IIS Express**</span></span>

* <span data-ttu-id="95c41-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-897">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="95c41-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="95c41-898">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="95c41-899">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="95c41-899">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="95c41-900">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="95c41-900">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="95c41-901">[ASP.NET Core モジュール参照ソース (マスター ブランチ)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): **[Branch]** ドロップダウン リストを使用して、特定のリリース (たとえば、`release/3.1`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="95c41-901">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
