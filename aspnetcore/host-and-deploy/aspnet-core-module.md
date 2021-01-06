---
title: ASP.NET Core モジュール
author: rick-anderson
description: IIS を使用して ASP.NET Core アプリをホストするための ASP.NET Core モジュールについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901237"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="53d33-103">ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="53d33-103">ASP.NET Core Module</span></span>

<span data-ttu-id="53d33-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Strahl](https://github.com/RickStrahl)、[Chris Ross](https://github.com/Tratcher)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Sourabh Shirhatti](https://twitter.com/sshirhatti)、[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="53d33-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="53d33-105">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、IIS パイプラインにプラグインされ、ASP.NET Core アプリケーションが IIS と連携できるようにします。</span><span class="sxs-lookup"><span data-stu-id="53d33-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="53d33-106">IIS で ASP.NET Core アプリを実行するには、次のいずれかのようにします。</span><span class="sxs-lookup"><span data-stu-id="53d33-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="53d33-107">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](xref:host-and-deploy/iis/in-process-hosting)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="53d33-108">Kestrel サーバーが実行されているバックエンドの ASP.NET Core アプリに、Web 要求を転送します。これは、[アウトプロセス ホスティング モデル](xref:host-and-deploy/iis/out-of-process-hosting)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="53d33-109">各ホスティング モデルの間にはトレードオフがあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="53d33-110">パフォーマンスと診断が向上するため、既定ではインプロセス ホスティング モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="53d33-111">ASP.NET Core モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="53d33-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="53d33-112">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="53d33-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="53d33-113">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="53d33-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="53d33-114">ASP.NET Core モジュールのインストール方法、または異なるバージョンのモジュールのインストールの詳細については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="53d33-115">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="53d33-116">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="53d33-117">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="53d33-118">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="53d33-119">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="53d33-119">Supported Windows versions:</span></span>

* <span data-ttu-id="53d33-120">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-120">Windows 7 or later</span></span>
* <span data-ttu-id="53d33-121">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="53d33-122">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="53d33-123">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="53d33-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="53d33-124">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="53d33-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="53d33-125">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="53d33-126">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-126">In-process hosting model</span></span>

<span data-ttu-id="53d33-127">ASP.NET Core アプリの既定はインプロセス ホスティング モデルです。</span><span class="sxs-lookup"><span data-stu-id="53d33-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="53d33-128">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="53d33-129">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="53d33-130">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="53d33-131">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="53d33-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="53d33-132">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="53d33-133">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="53d33-134">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="53d33-135">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="53d33-136">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="53d33-136">Use one app pool per app.</span></span>

* <span data-ttu-id="53d33-137">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用するとき、または[配置に `app_offline.htm` ファイル](xref:host-and-deploy/iis/index#locked-deployment-files)を手動で置くときは、開いている接続がある場合、アプリがすぐにシャットダウンされないことがあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="53d33-138">たとえば、WebSocket 接続によってアプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="53d33-139">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="53d33-140">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-140">Client disconnects are detected.</span></span> <span data-ttu-id="53d33-141">クライアントが切断されると、[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="53d33-142">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、`w3wp.exe` に対して `C:\Windows\System32\inetsrv`)。</span><span class="sxs-lookup"><span data-stu-id="53d33-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="53d33-143">アプリの現在のディレクトリを設定するサンプル コードについては、[`CurrentDirectoryHelpers` クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="53d33-144">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="53d33-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="53d33-145"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="53d33-146">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="53d33-147">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="53d33-148"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="53d33-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="53d33-149">[Web パッケージ (単一ファイル) の配置](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages)はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="53d33-150">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-150">Out-of-process hosting model</span></span>

<span data-ttu-id="53d33-151">アウトプロセス ホスティング用にアプリを構成するには、プロジェクト ファイル ( `.csproj`) で、`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="53d33-152">インプロセス ホスティングは `InProcess` で設定され、これが既定値です。</span><span class="sxs-lookup"><span data-stu-id="53d33-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="53d33-153">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="53d33-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="53d33-154">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="53d33-155">アウトプロセスの場合は、[`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) によって <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="53d33-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="53d33-156">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="53d33-157">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="53d33-158">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="53d33-158">Hosting model changes</span></span>

<span data-ttu-id="53d33-159">`hostingModel` の設定が `web.config` ファイル内で変更された場合 (「[`web.config` での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="53d33-160">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="53d33-161">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="53d33-162">プロセス名</span><span class="sxs-lookup"><span data-stu-id="53d33-162">Process name</span></span>

<span data-ttu-id="53d33-163">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="53d33-164">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="53d33-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="53d33-165">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="53d33-166">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="53d33-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="53d33-167">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="53d33-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="53d33-168">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="53d33-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="53d33-169">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="53d33-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="53d33-170">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="53d33-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="53d33-171">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="53d33-172">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="53d33-172">Configuration with web.config</span></span>

<span data-ttu-id="53d33-173">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="53d33-174">次に示す `web.config` ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="53d33-175">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="53d33-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="53d33-176"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="53d33-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="53d33-177">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-178">パスは stdout ログを `LogFiles` フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="53d33-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="53d33-179">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="53d33-180">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="53d33-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="53d33-181">属性</span><span class="sxs-lookup"><span data-stu-id="53d33-181">Attribute</span></span> | <span data-ttu-id="53d33-182">説明</span><span class="sxs-lookup"><span data-stu-id="53d33-182">Description</span></span> | <span data-ttu-id="53d33-183">Default</span><span class="sxs-lookup"><span data-stu-id="53d33-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="53d33-184">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-184">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-185">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="53d33-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="53d33-186">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-187">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="53d33-188">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-189">true の場合、トークンは、`%ASPNETCORE_PORT%` 上でリッスンしている子プロセスに、要求ごとのヘッダー `'MS-ASPNETCORE-WINAUTHTOKEN'` として転送されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="53d33-190">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="53d33-191">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-191">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-192">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="53d33-193">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-194">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="53d33-195">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="53d33-196">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="53d33-197">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="53d33-198">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-198">Default: `1`</span></span><br><span data-ttu-id="53d33-199">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-199">Min: `1`</span></span><br><span data-ttu-id="53d33-200">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="53d33-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="53d33-201">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-201">Required string attribute.</span></span></p><p><span data-ttu-id="53d33-202">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="53d33-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="53d33-203">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53d33-203">Relative paths are supported.</span></span> <span data-ttu-id="53d33-204">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="53d33-205">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-206">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="53d33-207">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="53d33-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="53d33-208">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="53d33-209">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-209">Default: `10`</span></span><br><span data-ttu-id="53d33-210">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-210">Min: `0`</span></span><br><span data-ttu-id="53d33-211">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="53d33-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="53d33-212">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="53d33-213">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="53d33-214">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="53d33-215">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="53d33-216">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="53d33-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="53d33-217">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="53d33-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="53d33-218">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="53d33-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="53d33-219">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-219">Default: `00:02:00`</span></span><br><span data-ttu-id="53d33-220">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-220">Min: `00:00:00`</span></span><br><span data-ttu-id="53d33-221">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="53d33-222">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-223">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="53d33-224">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-224">Default: `10`</span></span><br><span data-ttu-id="53d33-225">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-225">Min: `0`</span></span><br><span data-ttu-id="53d33-226">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="53d33-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="53d33-227">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-228">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="53d33-229">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="53d33-230">"*インプロセス*" でホスティングしている場合: プロセスは再起動されて **おらず**、**rapidFailsPerMinute** 設定が使用されて **いません**。</span><span class="sxs-lookup"><span data-stu-id="53d33-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="53d33-231">"*アウト プロセス*" でホスティングしている場合: 最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="53d33-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="53d33-232">値 0 (ゼロ) は無限のタイムアウトと見なされ **ません**。</span><span class="sxs-lookup"><span data-stu-id="53d33-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="53d33-233">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="53d33-233">Default: `120`</span></span><br><span data-ttu-id="53d33-234">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-234">Min: `0`</span></span><br><span data-ttu-id="53d33-235">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="53d33-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="53d33-236">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-237">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="53d33-238">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-238">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-239">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="53d33-240">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="53d33-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="53d33-241">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="53d33-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="53d33-242">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-243">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="53d33-244">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは `logs` フォルダーに `stdout_20180205194132_1934.log` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="53d33-245">環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="53d33-245">Set environment variables</span></span>

<span data-ttu-id="53d33-246">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="53d33-247">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="53d33-248">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="53d33-249">次の例では、`web.config` 内に 2 つの環境変数が設定されています。</span><span class="sxs-lookup"><span data-stu-id="53d33-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="53d33-250">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="53d33-251">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、`web.config` ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="53d33-252">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="53d33-253">`web.config` 内で環境を直接設定する代わりに、[発行プロファイル (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="53d33-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="53d33-254">この方法では、プロジェクトが発行されるときに `web.config` に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="53d33-255">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="53d33-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="53d33-256">`app_offline.htm` という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="53d33-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="53d33-257">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="53d33-258">`app_offline.htm` ファイルが存在している間、ASP.NET Core モジュールは、`app_offline.htm` ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="53d33-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="53d33-259">`app_offline.htm` ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="53d33-260">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="53d33-261">たとえば、WebSocket 接続によってアプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="53d33-262">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="53d33-262">Start-up error page</span></span>

<span data-ttu-id="53d33-263">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="53d33-264">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-265">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-266">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-267">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="53d33-268">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="53d33-269">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="53d33-269">Log creation and redirection</span></span>

<span data-ttu-id="53d33-270">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="53d33-271">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-272">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="53d33-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="53d33-273">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="53d33-274">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="53d33-275">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="53d33-276">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="53d33-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="53d33-277">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="53d33-278">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="53d33-279">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="53d33-280">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) を `stdoutLogFile` パスの最後のセグメント (通常は `stdout`) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="53d33-281">`stdoutLogFile` パスが `stdout` で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、`stdout_20180205194132_1934.log` になります。</span><span class="sxs-lookup"><span data-stu-id="53d33-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="53d33-282">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="53d33-283">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="53d33-284">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="53d33-285">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="53d33-286">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-287">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="53d33-288">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="53d33-289">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="53d33-290">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="53d33-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="53d33-291">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="53d33-292">`<handlerSettings>` 要素を、`web.config` 内の `<aspNetCore>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="53d33-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="53d33-293">`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="53d33-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="53d33-294">パスに含まれるフォルダー (前の例では `logs`) は、ログ ファイルの作成時に、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-295">アプリ プールには、ログが書き込まれる場所への書き込みアクセス権が付与されている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\{APP POOL NAME}` を使用します。ここで、プレースホルダー `{APP POOL NAME}` はアプリ プール名です)。</span><span class="sxs-lookup"><span data-stu-id="53d33-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="53d33-296">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="53d33-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="53d33-297">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="53d33-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="53d33-298">ERROR</span><span class="sxs-lookup"><span data-stu-id="53d33-298">ERROR</span></span>
* <span data-ttu-id="53d33-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="53d33-299">WARNING</span></span>
* <span data-ttu-id="53d33-300">INFO</span><span class="sxs-lookup"><span data-stu-id="53d33-300">INFO</span></span>
* <span data-ttu-id="53d33-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="53d33-301">TRACE</span></span>

<span data-ttu-id="53d33-302">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="53d33-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="53d33-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="53d33-303">CONSOLE</span></span>
* <span data-ttu-id="53d33-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="53d33-304">EVENTLOG</span></span>
* <span data-ttu-id="53d33-305">ファイル</span><span class="sxs-lookup"><span data-stu-id="53d33-305">FILE</span></span>

<span data-ttu-id="53d33-306">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="53d33-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="53d33-307">`ASPNETCORE_MODULE_DEBUG_FILE`:デバッグ ログ ファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="53d33-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="53d33-308">(既定値: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="53d33-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="53d33-309">`ASPNETCORE_MODULE_DEBUG`:デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="53d33-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="53d33-310">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="53d33-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="53d33-311">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-311">The size of the log isn't limited.</span></span> <span data-ttu-id="53d33-312">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="53d33-313">`web.config` ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="53d33-314">スタック サイズを変更する</span><span class="sxs-lookup"><span data-stu-id="53d33-314">Modify the stack size</span></span>

<span data-ttu-id="53d33-315">"*インプロセス ホスティング モデルを使用している場合にのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="53d33-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="53d33-316">`web.config` で `stackSize` の設定を使用してマネージド スタックのサイズを構成します (バイト単位)。</span><span class="sxs-lookup"><span data-stu-id="53d33-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="53d33-317">既定のサイズは 1,048,576 バイト (1 MB) です。</span><span class="sxs-lookup"><span data-stu-id="53d33-317">The default size is 1,048,576 bytes (1 MB).</span></span>

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="53d33-318">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="53d33-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="53d33-319">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="53d33-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="53d33-320">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="53d33-321">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="53d33-322">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="53d33-323">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="53d33-324">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="53d33-325">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="53d33-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="53d33-326">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="53d33-327">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="53d33-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="53d33-328">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="53d33-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="53d33-329">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="53d33-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="53d33-330">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="53d33-331">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の `applicationHost.config` ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="53d33-332">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="53d33-333">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="53d33-334">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="53d33-335">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-335">Run the installer.</span></span>
1. <span data-ttu-id="53d33-336">更新された `applicationHost.config` ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="53d33-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="53d33-337">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="53d33-338">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="53d33-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="53d33-339">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="53d33-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="53d33-340">ホスティング システム上で、 `%windir%\System32\inetsrv` に移動します。</span><span class="sxs-lookup"><span data-stu-id="53d33-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="53d33-341">`aspnetcore.dll` ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="53d33-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="53d33-342">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="53d33-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="53d33-343">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="53d33-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="53d33-344">モジュールに関するホスティング バンドル インストーラーのログは、`C:\Users\%UserName%\AppData\Local\Temp` にあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="53d33-345">ファイルの名前は `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` です。</span><span class="sxs-lookup"><span data-stu-id="53d33-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="53d33-346">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="53d33-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="53d33-347">Module</span><span class="sxs-lookup"><span data-stu-id="53d33-347">Module</span></span>

<span data-ttu-id="53d33-348">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="53d33-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="53d33-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="53d33-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="53d33-350">Schema</span><span class="sxs-lookup"><span data-stu-id="53d33-350">Schema</span></span>

<span data-ttu-id="53d33-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="53d33-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="53d33-353">構成</span><span class="sxs-lookup"><span data-stu-id="53d33-353">Configuration</span></span>

<span data-ttu-id="53d33-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="53d33-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-355">**IIS Express**</span></span>

* <span data-ttu-id="53d33-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="53d33-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="53d33-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="53d33-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="53d33-358">ファイルは、`applicationHost.config` ファイルで `aspnetcore` を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="53d33-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="53d33-359">ASP.NET Core モジュールはネイティブな IIS モジュールであり、次のいずれかを目的として、IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="53d33-360">IIS ワーカー プロセス (`w3wp.exe`) の内部で ASP.NET Core アプリをホストします。これは、[インプロセス ホスティング モデル](#in-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="53d33-361">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を実行しているバックエンドの ASP.NET Core アプリに Web 要求を転送します。これは、[アウト プロセス ホスティング モデル](#out-of-process-hosting-model)と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="53d33-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="53d33-362">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="53d33-362">Supported Windows versions:</span></span>

* <span data-ttu-id="53d33-363">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-363">Windows 7 or later</span></span>
* <span data-ttu-id="53d33-364">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="53d33-365">インプロセス ホスティングの場合、モジュールでは IIS HTTP サーバー (`IISHttpServer`) と呼ばれる IIS 用のインプロセス サーバー実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="53d33-366">アウト プロセスでホストする場合、モジュールは Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="53d33-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="53d33-367">このモジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) では機能しません。</span><span class="sxs-lookup"><span data-stu-id="53d33-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="53d33-368">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="53d33-369">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-369">In-process hosting model</span></span>

<span data-ttu-id="53d33-370">アプリに対してインプロセス ホスティングを構成するには、そのアプリのプロジェクト ファイルに `<AspNetCoreHostingModel>` プロパティを追加して、値を `InProcess` に設定します。(アウト プロセス ホスティングは `OutOfProcess` を使用して設定します)。</span><span class="sxs-lookup"><span data-stu-id="53d33-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="53d33-371">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="53d33-372">`<AspNetCoreHostingModel>` の値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="53d33-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="53d33-373">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="53d33-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="53d33-374">インプロセスでホストする場合は、次の特性が適用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="53d33-375">[Kestrel](xref:fundamentals/servers/kestrel) サーバーの代わりに、IIS HTTP サーバー (`IISHttpServer`) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="53d33-376">インプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="53d33-377">`IISHttpServer` を登録します。</span><span class="sxs-lookup"><span data-stu-id="53d33-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="53d33-378">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="53d33-379">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="53d33-380">[requestTimeout 属性](#attributes-of-the-aspnetcore-element) は、インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="53d33-381">アプリ プールをアプリ間で共有することはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="53d33-382">アプリごとに 1 つのアプリ プールを使用します。</span><span class="sxs-lookup"><span data-stu-id="53d33-382">Use one app pool per app.</span></span>

* <span data-ttu-id="53d33-383">[Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用したとき、または [app_offline.htm ファイルを手動で配置内に置いた](xref:host-and-deploy/iis/index#locked-deployment-files)ときには、開いている接続があると、アプリがすぐにシャットダウンされない場合があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="53d33-384">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="53d33-385">アプリとインストールされているランタイム (x64 または x86) のアーキテクチャ (ビット) は、アプリ プールのアーキテクチャと一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="53d33-386">クライアントの切断が検出されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-386">Client disconnects are detected.</span></span> <span data-ttu-id="53d33-387">クライアントが切断されると、[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) キャンセル トークンが取り消されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="53d33-388">ASP.NET Core 2.2.1 以前の場合、<xref:System.IO.Directory.GetCurrentDirectory*> は、アプリのディレクトリではなく、IIS によって開始されたプロセスのワーカー ディレクトリを返します (たとえば、*w3wp.exe* に対して *C:\Windows\System32\inetsrv*)。</span><span class="sxs-lookup"><span data-stu-id="53d33-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="53d33-389">アプリの現在のディレクトリを設定するサンプル コードについては、「[CurrentDirectoryHelpers クラス](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="53d33-390">`SetCurrentDirectory` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="53d33-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="53d33-391"><xref:System.IO.Directory.GetCurrentDirectory*> の後続の呼び出しによって、アプリのディレクトリが指定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="53d33-392">インプロセス ホスティングの場合、ユーザーを初期化するために内部で <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> が呼び出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="53d33-393">そのため、認証のたびに要求を変換するための <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装は既定で有効になっていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="53d33-394"><xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 実装で要求を返還するとき、<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> を呼び出し、認証サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="53d33-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="53d33-395">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="53d33-395">Out-of-process hosting model</span></span>

<span data-ttu-id="53d33-396">アウト プロセス ホスティング用のアプリを構成するには、プロジェクト ファイルで次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="53d33-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="53d33-397">`<AspNetCoreHostingModel>` プロパティは指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="53d33-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="53d33-398">`<AspNetCoreHostingModel>` プロパティがファイルに存在しない場合、既定値は `OutOfProcess` です。</span><span class="sxs-lookup"><span data-stu-id="53d33-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="53d33-399">`<AspNetCoreHostingModel>` プロパティの値を `OutOfProcess` に設定します (インプロセス ホスティングは `InProcess` で設定されます)。</span><span class="sxs-lookup"><span data-stu-id="53d33-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="53d33-400">この値では大文字と小文字が区別されないため、`inprocess` と `outofprocess` は有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="53d33-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="53d33-401">IIS HTTP サーバー (`IISHttpServer`) の代わりに、[Kestrel](xref:fundamentals/servers/kestrel) サーバーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="53d33-402">アウトプロセスの場合、[CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) により <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> が呼び出され、次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="53d33-403">ASP.NET Core モジュールの背後で実行するときにサーバーがリッスンする、ポートとベース パスを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="53d33-404">スタートアップ エラーをキャプチャするホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="53d33-405">ホスティング モデルの変更</span><span class="sxs-lookup"><span data-stu-id="53d33-405">Hosting model changes</span></span>

<span data-ttu-id="53d33-406">`hostingModel` 設定が *web.config* ファイル内で変更された場合 (「[web.config での構成](#configuration-with-webconfig)」セクションを参照)、モジュールによって IIS 用のワーカー プロセスがリサイクルされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="53d33-407">IIS Express の場合、モジュールによってワーカー プロセスのリサイクルは行われませんが、代わりに、現在の IIS Express プロセスの正常なシャットダウンがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="53d33-408">アプリに対して次の要求が出されると、IIS Express の新しいプロセスが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="53d33-409">プロセス名</span><span class="sxs-lookup"><span data-stu-id="53d33-409">Process name</span></span>

<span data-ttu-id="53d33-410">`Process.GetCurrentProcess().ProcessName` から、`w3wp`/`iisexpress` (インプロセス) または `dotnet` (アウト プロセス) がレポートされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="53d33-411">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="53d33-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="53d33-412">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="53d33-413">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="53d33-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="53d33-414">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="53d33-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="53d33-415">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="53d33-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="53d33-416">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="53d33-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="53d33-417">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="53d33-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="53d33-418">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="53d33-419">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="53d33-419">Configuration with web.config</span></span>

<span data-ttu-id="53d33-420">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="53d33-421">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="53d33-422">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="53d33-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="53d33-423"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> プロパティは、`false` に設定されます。これは、[\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 要素内で指定された設定が、アプリのサブディレクトリにあるアプリによって継承されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="53d33-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="53d33-424">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-425">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="53d33-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="53d33-426">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="53d33-427">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="53d33-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="53d33-428">属性</span><span class="sxs-lookup"><span data-stu-id="53d33-428">Attribute</span></span> | <span data-ttu-id="53d33-429">説明</span><span class="sxs-lookup"><span data-stu-id="53d33-429">Description</span></span> | <span data-ttu-id="53d33-430">Default</span><span class="sxs-lookup"><span data-stu-id="53d33-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="53d33-431">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-431">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-432">`processPath` において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="53d33-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="53d33-433">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-434">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="53d33-435">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-436">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="53d33-437">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="53d33-438">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-438">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-439">ホスティング モデルをインプロセス (`InProcess`/`inprocess`) またはアウト プロセス (`OutOfProcess`/`outofprocess`) として指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="53d33-440">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-441">アプリごとにスピンアップすることができる `processPath` 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="53d33-442">&dagger;インプロセス ホスティングの場合、値は `1` に制限されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="53d33-443">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="53d33-444">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="53d33-445">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-445">Default: `1`</span></span><br><span data-ttu-id="53d33-446">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-446">Min: `1`</span></span><br><span data-ttu-id="53d33-447">最大値: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="53d33-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="53d33-448">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-448">Required string attribute.</span></span></p><p><span data-ttu-id="53d33-449">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="53d33-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="53d33-450">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53d33-450">Relative paths are supported.</span></span> <span data-ttu-id="53d33-451">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="53d33-452">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-453">`processPath` で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="53d33-454">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="53d33-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="53d33-455">インプロセス ホスティングではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="53d33-456">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-456">Default: `10`</span></span><br><span data-ttu-id="53d33-457">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-457">Min: `0`</span></span><br><span data-ttu-id="53d33-458">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="53d33-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="53d33-459">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="53d33-460">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="53d33-461">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="53d33-462">インプロセス ホスティングには適用されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="53d33-463">インプロセス ホスティングの場合、アプリによって要求が処理されるまでモジュールは待機します。</span><span class="sxs-lookup"><span data-stu-id="53d33-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="53d33-464">0 から 59 までが文字列の分セグメントと秒セグメントで有効な値となります。</span><span class="sxs-lookup"><span data-stu-id="53d33-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="53d33-465">分または秒の値に **60** を入れると *500 - Internal Server Error* が出ます。</span><span class="sxs-lookup"><span data-stu-id="53d33-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="53d33-466">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-466">Default: `00:02:00`</span></span><br><span data-ttu-id="53d33-467">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-467">Min: `00:00:00`</span></span><br><span data-ttu-id="53d33-468">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="53d33-469">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-470">`app_offline.htm` ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="53d33-471">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-471">Default: `10`</span></span><br><span data-ttu-id="53d33-472">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-472">Min: `0`</span></span><br><span data-ttu-id="53d33-473">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="53d33-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="53d33-474">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-475">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="53d33-476">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="53d33-477">"*インプロセス*" でホスティングしている場合: プロセスは再起動されて **おらず**、`rapidFailsPerMinute` 設定が使用されて **いません**。</span><span class="sxs-lookup"><span data-stu-id="53d33-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="53d33-478">"*アウト プロセス*" でホスティングしている場合: 最後のローリング分においてアプリが `rapidFailsPerMinute` 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="53d33-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="53d33-479">値 0 (ゼロ) は無限のタイムアウトと見なされ **ません**。</span><span class="sxs-lookup"><span data-stu-id="53d33-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="53d33-480">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="53d33-480">Default: `120`</span></span><br><span data-ttu-id="53d33-481">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-481">Min: `0`</span></span><br><span data-ttu-id="53d33-482">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="53d33-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="53d33-483">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-484">true の場合、`processPath` で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="53d33-485">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-485">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-486">`processPath` で指定されているプロセスからの `stdout` と `stderr` がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="53d33-487">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="53d33-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="53d33-488">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="53d33-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="53d33-489">パスで指定されたフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-490">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) が、`stdoutLogFile` パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="53d33-491">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは `logs` フォルダーに `stdout_20180205194132_1934.log` として保存されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="53d33-492">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="53d33-492">Setting environment variables</span></span>

<span data-ttu-id="53d33-493">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="53d33-494">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="53d33-495">このセクションで設定された環境変数は、システム環境変数より優先されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="53d33-496">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="53d33-496">The following example sets two environment variables.</span></span> <span data-ttu-id="53d33-497">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="53d33-498">開発者は、アプリの例外をデバッグするときに[開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、`web.config` ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="53d33-499">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="53d33-500">`web.config` 内で環境を直接設定する代わりに、[発行プロファイル (.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) またはプロジェクト ファイルに `<EnvironmentName>` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="53d33-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="53d33-501">この方法では、プロジェクトが発行されるときに `web.config` に環境が設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="53d33-502">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="53d33-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="53d33-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="53d33-503">app_offline.htm</span></span>

<span data-ttu-id="53d33-504">`app_offline.htm` という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="53d33-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="53d33-505">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="53d33-506">`app_offline.htm` ファイルが存在している間、ASP.NET Core モジュールは、`app_offline.htm` ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="53d33-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="53d33-507">`app_offline.htm` ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="53d33-508">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="53d33-509">たとえば、WebSocket 接続では、アプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="53d33-510">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="53d33-510">Start-up error page</span></span>

<span data-ttu-id="53d33-511">インプロセス ホスティングでもアウト プロセス ホスティングでも、アプリの起動に失敗すると、カスタム エラー ページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="53d33-512">ASP.NET Core モジュールが、インプロセスまたはアウト プロセスのどちらかの要求ハンドラーの検索に失敗した場合、*500.0 - インプロセス/アウト プロセス ハンドラーの読み込みエラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-513">インプロセス ホスティングで、ASP.NET Core モジュールによるアプリの起動が失敗すると、*500.30 - 開始エラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-514">アウト プロセス ホスティングで、ASP.NET Core モジュールがバックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="53d33-515">このページを抑制して、既定の IIS 5xx 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="53d33-516">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー \<httpErrors>](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="53d33-517">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="53d33-517">Log creation and redirection</span></span>

<span data-ttu-id="53d33-518">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="53d33-519">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-520">アプリ プールには、ログが書き込まれる場所への書き込みアクセス権が付与されている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\{APP POOL NAME}` を使用します。ここで、プレースホルダー `{APP POOL NAME}` はアプリ プール名です)。</span><span class="sxs-lookup"><span data-stu-id="53d33-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="53d33-521">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="53d33-522">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="53d33-523">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="53d33-524">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="53d33-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="53d33-525">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="53d33-526">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="53d33-527">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="53d33-528">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) を `stdoutLogFile` パスの最後のセグメント (通常は `stdout`) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="53d33-529">`stdoutLogFile` パスが `stdout` で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、`stdout_20180205194132_1934.log` になります。</span><span class="sxs-lookup"><span data-stu-id="53d33-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="53d33-530">`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="53d33-531">起動後、すべての追加のログが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="53d33-532">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="53d33-533">アプリ プールのユーザー ID に、指定したパスへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="53d33-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="53d33-534">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-535">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="53d33-536">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="53d33-537">強化された診断ログ</span><span class="sxs-lookup"><span data-stu-id="53d33-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="53d33-538">ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="53d33-539">`<handlerSettings>` 要素を、`web.config` 内の `<aspNetCore>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="53d33-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="53d33-540">`debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。</span><span class="sxs-lookup"><span data-stu-id="53d33-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="53d33-541">`<handlerSetting>` 値に提供されるパスのフォルダー (前の例では `logs`) がこのモジュールによって自動的に作成されることはなく、デプロイに事前に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="53d33-542">アプリ プールには、ログが書き込まれる場所への書き込みアクセス権が付与されている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\{APP POOL NAME}` を使用します。ここで、プレースホルダー `{APP POOL NAME}` はアプリ プール名です)。</span><span class="sxs-lookup"><span data-stu-id="53d33-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="53d33-543">デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="53d33-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="53d33-544">レベルは次のとおりです (情報量が少ないものから多いものへの順)。</span><span class="sxs-lookup"><span data-stu-id="53d33-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="53d33-545">ERROR</span><span class="sxs-lookup"><span data-stu-id="53d33-545">ERROR</span></span>
* <span data-ttu-id="53d33-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="53d33-546">WARNING</span></span>
* <span data-ttu-id="53d33-547">INFO</span><span class="sxs-lookup"><span data-stu-id="53d33-547">INFO</span></span>
* <span data-ttu-id="53d33-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="53d33-548">TRACE</span></span>

<span data-ttu-id="53d33-549">場所は次のとおりです (複数の場所を指定できます)。</span><span class="sxs-lookup"><span data-stu-id="53d33-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="53d33-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="53d33-550">CONSOLE</span></span>
* <span data-ttu-id="53d33-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="53d33-551">EVENTLOG</span></span>
* <span data-ttu-id="53d33-552">ファイル</span><span class="sxs-lookup"><span data-stu-id="53d33-552">FILE</span></span>

<span data-ttu-id="53d33-553">ハンドラー設定は、次の環境変数を使用して指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="53d33-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="53d33-554">`ASPNETCORE_MODULE_DEBUG_FILE`:デバッグ ログ ファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="53d33-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="53d33-555">(既定値: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="53d33-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="53d33-556">`ASPNETCORE_MODULE_DEBUG`:デバッグ レベルの設定。</span><span class="sxs-lookup"><span data-stu-id="53d33-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="53d33-557">配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに **しないでください**。</span><span class="sxs-lookup"><span data-stu-id="53d33-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="53d33-558">ログのサイズは制限されていません。</span><span class="sxs-lookup"><span data-stu-id="53d33-558">The size of the log isn't limited.</span></span> <span data-ttu-id="53d33-559">デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="53d33-560">`web.config` ファイルでの `aspNetCore` 要素の例については、「[web.config での構成](#configuration-with-webconfig)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="53d33-561">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="53d33-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="53d33-562">*アウト プロセス ホスティングにのみ適用されます。*</span><span class="sxs-lookup"><span data-stu-id="53d33-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="53d33-563">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="53d33-564">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="53d33-565">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="53d33-566">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="53d33-567">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="53d33-568">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="53d33-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="53d33-569">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="53d33-570">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="53d33-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="53d33-571">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="53d33-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="53d33-572">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="53d33-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="53d33-573">ASP.NET Core モジュールのインストーラーは、`TrustedInstaller` アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="53d33-574">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の `applicationHost.config` ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="53d33-575">IIS がインストールされている同じコンピューターで IIS 共有抗生を使用するとき、`OPT_NO_SHARED_CONFIG_CHECK` パラメーターを `1` に設定して ASP.NET Core Hosting Bundle インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="53d33-576">共有構成のパスが IIS をインストールしている同じコンピューター上にないときは、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="53d33-577">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="53d33-578">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-578">Run the installer.</span></span>
1. <span data-ttu-id="53d33-579">更新された `applicationHost.config` ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="53d33-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="53d33-580">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="53d33-581">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="53d33-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="53d33-582">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="53d33-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="53d33-583">ホスティング システム上で、 `%windir%\System32\inetsrv` に移動します。</span><span class="sxs-lookup"><span data-stu-id="53d33-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="53d33-584">`aspnetcore.dll` ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="53d33-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="53d33-585">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="53d33-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="53d33-586">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="53d33-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="53d33-587">モジュールに関するホスティング バンドル インストーラーのログは、`C:\\Users\\%UserName%\\AppData\\Local\\Temp` にあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="53d33-588">ファイルの名前は `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` です。プレースホルダー `{TIMESTAMP}` はタイムスタンプです。</span><span class="sxs-lookup"><span data-stu-id="53d33-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="53d33-589">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="53d33-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="53d33-590">Module</span><span class="sxs-lookup"><span data-stu-id="53d33-590">Module</span></span>

<span data-ttu-id="53d33-591">**IIS (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="53d33-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="53d33-592">**IIS Express (x86/amd64)** :</span><span class="sxs-lookup"><span data-stu-id="53d33-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="53d33-593">Schema</span><span class="sxs-lookup"><span data-stu-id="53d33-593">Schema</span></span>

<span data-ttu-id="53d33-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="53d33-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="53d33-596">構成</span><span class="sxs-lookup"><span data-stu-id="53d33-596">Configuration</span></span>

<span data-ttu-id="53d33-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="53d33-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-598">**IIS Express**</span></span>

* <span data-ttu-id="53d33-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="53d33-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="53d33-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="53d33-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="53d33-601">ファイルは、`applicationHost.config` ファイルで `aspnetcore` を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="53d33-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="53d33-602">ASP.NET Core モジュールは、ネイティブな IIS モジュールであり、バックエンドの ASP.NET Core アプリに Web 要求を転送するために IIS パイプラインにプラグインされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="53d33-603">サポートされている Windows バージョン:</span><span class="sxs-lookup"><span data-stu-id="53d33-603">Supported Windows versions:</span></span>

* <span data-ttu-id="53d33-604">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-604">Windows 7 or later</span></span>
* <span data-ttu-id="53d33-605">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="53d33-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="53d33-606">モジュールは、Kestrel に対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="53d33-606">The module only works with Kestrel.</span></span> <span data-ttu-id="53d33-607">モジュールは [HTTP.sys](xref:fundamentals/servers/httpsys) と互換性はありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="53d33-608">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、モジュールもプロセス管理を処理します。</span><span class="sxs-lookup"><span data-stu-id="53d33-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="53d33-609">モジュールは、最初の要求を受信したときに ASP.NET Core アプリのプロセスを開始し、プロセスがクラッシュした場合はアプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="53d33-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="53d33-610">この動作は、IIS のインプロセスで実行され、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理される ASP.NET 4.x アプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="53d33-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="53d33-611">次の図は、IIS (ASP.NET Core モジュール) とアプリの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="53d33-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core モジュール](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="53d33-613">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="53d33-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="53d33-614">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="53d33-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="53d33-615">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="53d33-616">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="53d33-617">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="53d33-618">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="53d33-619">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="53d33-620">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="53d33-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="53d33-621">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="53d33-622">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="53d33-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="53d33-623">Windows 認証などの多くのネイティブなモジュールは、アクティブなままです。</span><span class="sxs-lookup"><span data-stu-id="53d33-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="53d33-624">ASP.NET Core モジュールと共にアクティブな IIS モジュールの詳細については、「<xref:host-and-deploy/iis/modules>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="53d33-625">ASP.NET Core モジュールでは次のことも行えます。</span><span class="sxs-lookup"><span data-stu-id="53d33-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="53d33-626">ワーカー プロセスの環境変数を設定する</span><span class="sxs-lookup"><span data-stu-id="53d33-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="53d33-627">起動に関する問題をトラブルシューティングするために、Stdout 出力をファイル ストレージに記録する</span><span class="sxs-lookup"><span data-stu-id="53d33-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="53d33-628">Windows 認証トークンを転送する</span><span class="sxs-lookup"><span data-stu-id="53d33-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="53d33-629">ASP.NET Core モジュールをインストールして使用する方法</span><span class="sxs-lookup"><span data-stu-id="53d33-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="53d33-630">ASP.NET Core モジュールのインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="53d33-631">web.config での構成</span><span class="sxs-lookup"><span data-stu-id="53d33-631">Configuration with web.config</span></span>

<span data-ttu-id="53d33-632">ASP.NET Core モジュールは、サイトの *web.config* ファイルの `system.webServer` ノードの `aspNetCore` セクションを使って構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="53d33-633">次に示す *web.config* ファイルは、[フレームワークに依存する展開](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd)用に発行されたもので、サイトの要求を処理するように ASP.NET Core モジュールを構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="53d33-634">次の *web.config* は、[自己完結型の展開](/dotnet/articles/core/deploying/#self-contained-deployments-scd)用に発行されたものです。</span><span class="sxs-lookup"><span data-stu-id="53d33-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="53d33-635">アプリが [Azure App Service](https://azure.microsoft.com/services/app-service/) に対して展開されると、`stdoutLogFile` パスは `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-636">パスは stdout ログを *LogFiles* フォルダーに保存します。これは、サービスによって自動的に作成される場所です。</span><span class="sxs-lookup"><span data-stu-id="53d33-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="53d33-637">IIS サブアプリケーション構成について詳しくは、「<xref:host-and-deploy/iis/index#sub-applications>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="53d33-638">aspNetCore 要素の属性</span><span class="sxs-lookup"><span data-stu-id="53d33-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="53d33-639">属性</span><span class="sxs-lookup"><span data-stu-id="53d33-639">Attribute</span></span> | <span data-ttu-id="53d33-640">説明</span><span class="sxs-lookup"><span data-stu-id="53d33-640">Description</span></span> | <span data-ttu-id="53d33-641">Default</span><span class="sxs-lookup"><span data-stu-id="53d33-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="53d33-642">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-642">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-643">**processPath** において指定されている実行可能ファイルへの引数です。</span><span class="sxs-lookup"><span data-stu-id="53d33-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="53d33-644">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-645">true の場合、**502.5 - 処理エラー** ページは抑制され、*web.config* で構成されている 502 状態コード ページが優先されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="53d33-646">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-647">true の場合、トークンは、%ASPNETCORE_PORT% でリッスンしている子プロセスに、要求ごとの 'MS-ASPNETCORE-WINAUTHTOKEN' ヘッダーとして転送されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="53d33-648">要求ごとのこのトークンで CloseHandle を呼び出すのは、そのプロセスの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="53d33-649">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-650">アプリごとにスピンアップすることができる **processPath** 設定内で指定したプロセスのインスタンス数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="53d33-651">設定 `processesPerApplication` は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="53d33-652">この属性は将来のリリースで削除されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="53d33-653">既定値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-653">Default: `1`</span></span><br><span data-ttu-id="53d33-654">最小値: `1`</span><span class="sxs-lookup"><span data-stu-id="53d33-654">Min: `1`</span></span><br><span data-ttu-id="53d33-655">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="53d33-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="53d33-656">必須の文字列属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-656">Required string attribute.</span></span></p><p><span data-ttu-id="53d33-657">HTTP 要求をリッスンするプロセスを起動する実行可能ファイルへのパスです。</span><span class="sxs-lookup"><span data-stu-id="53d33-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="53d33-658">相対パスがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="53d33-658">Relative paths are supported.</span></span> <span data-ttu-id="53d33-659">パスが `.` で始まる場合、パスはサイトのルートを基準とする相対パスであると見なされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="53d33-660">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-661">**processPath** で指定されているプロセスが 1 分間にクラッシュできる回数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="53d33-662">この制限を超えた場合、モジュールは、1 分間の残りの間、プロセスの起動を停止します。</span><span class="sxs-lookup"><span data-stu-id="53d33-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="53d33-663">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-663">Default: `10`</span></span><br><span data-ttu-id="53d33-664">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-664">Min: `0`</span></span><br><span data-ttu-id="53d33-665">最大値: `100`</span><span class="sxs-lookup"><span data-stu-id="53d33-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="53d33-666">省略可能な期間属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="53d33-667">%ASPNETCORE_PORT% でリッスンしているプロセスからの応答を ASP.NET Core モジュールが待機する期間を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="53d33-668">ASP.NET Core 2.1 以降のリリースに付属する ASP.NET Core モジュールのバージョンでは、`requestTimeout` は時間、分、および秒単位で指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="53d33-669">既定値: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-669">Default: `00:02:00`</span></span><br><span data-ttu-id="53d33-670">最小値: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-670">Min: `00:00:00`</span></span><br><span data-ttu-id="53d33-671">最大値: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="53d33-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="53d33-672">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-673">*app_offline.htm* ファイルが検出されたときに、モジュールが実行可能ファイルの正常なシャットダウンを待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="53d33-674">既定値: `10`</span><span class="sxs-lookup"><span data-stu-id="53d33-674">Default: `10`</span></span><br><span data-ttu-id="53d33-675">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-675">Min: `0`</span></span><br><span data-ttu-id="53d33-676">最大値: `600`</span><span class="sxs-lookup"><span data-stu-id="53d33-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="53d33-677">省略可能な整数属性</span><span class="sxs-lookup"><span data-stu-id="53d33-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="53d33-678">ポートでリッスンするプロセスを実行可能ファイルが開始するのをモジュールが待機する秒単位の期間です。</span><span class="sxs-lookup"><span data-stu-id="53d33-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="53d33-679">この制限時間を超えた場合、モジュールはプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="53d33-680">最後のローリング分においてアプリが **rapidFailsPerMinute** 回だけ開始を失敗しているのでないかぎり、モジュールは、新しい要求を受け取るとプロセスの再起動を試み、それ以降に受信した要求に対してプロセスの再起動を試み続けます。</span><span class="sxs-lookup"><span data-stu-id="53d33-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="53d33-681">値 0 (ゼロ) は無限のタイムアウトと見なされ **ません**。</span><span class="sxs-lookup"><span data-stu-id="53d33-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="53d33-682">既定値: `120`</span><span class="sxs-lookup"><span data-stu-id="53d33-682">Default: `120`</span></span><br><span data-ttu-id="53d33-683">最小値: `0`</span><span class="sxs-lookup"><span data-stu-id="53d33-683">Min: `0`</span></span><br><span data-ttu-id="53d33-684">最大値: `3600`</span><span class="sxs-lookup"><span data-stu-id="53d33-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="53d33-685">省略可能な Boolean 属性です。</span><span class="sxs-lookup"><span data-stu-id="53d33-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="53d33-686">true の場合、**processPath** で指定されているプロセスの **stdout** と **stderr** は、**stdoutLogFile** で指定されているファイルにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="53d33-687">省略可能な文字列属性。</span><span class="sxs-lookup"><span data-stu-id="53d33-687">Optional string attribute.</span></span></p><p><span data-ttu-id="53d33-688">**processPath** で指定されているプロセスからの **stdout** と **stderr** がログに記録される相対ファイル パスまたは絶対ファイル パスを指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="53d33-689">相対パスの基準はサイトのルートです。</span><span class="sxs-lookup"><span data-stu-id="53d33-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="53d33-690">`.` で始まっているパスはすべてサイト ルートに対する相対パスであり、他のすべてのパスは絶対パスとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="53d33-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="53d33-691">モジュールがログ ファイルを作成するためには、パスで指定されているすべてのフォルダーが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="53d33-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="53d33-692">アンダースコアの区切り記号を使って、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) が、**stdoutLogFile** パスの最後のセグメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="53d33-693">たとえば、値として `.\logs\stdout` を指定し、2018 年 2 月 5 日の 19:41:32 にプロセス ID 1934 で保存すると、stdout ログは *logs* フォルダーに *stdout_20180205194132_1934.log* として保存されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="53d33-694">環境変数の設定</span><span class="sxs-lookup"><span data-stu-id="53d33-694">Setting environment variables</span></span>

<span data-ttu-id="53d33-695">`processPath` 属性で、プロセスに対する環境変数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="53d33-696">`<environmentVariables>` コレクション要素の `<environmentVariable>` 子要素で、環境変数を指定します。</span><span class="sxs-lookup"><span data-stu-id="53d33-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="53d33-697">このセクションで設定した環境変数は、同じ名前を使って設定したシステム環境変数と競合します。</span><span class="sxs-lookup"><span data-stu-id="53d33-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="53d33-698">*web.config* ファイルと Windows のシステム レベルの両方で 1 つの環境変数が設定されている場合、*web.config* ファイルからの値がシステム環境変数の値に追加されるため (例: `ASPNETCORE_ENVIRONMENT: Development;Development`)、アプリが起動できなくなります。</span><span class="sxs-lookup"><span data-stu-id="53d33-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="53d33-699">次の例では、2 つの環境変数を設定しています。</span><span class="sxs-lookup"><span data-stu-id="53d33-699">The following example sets two environment variables.</span></span> <span data-ttu-id="53d33-700">`ASPNETCORE_ENVIRONMENT` は、`Development` に対するアプリの環境を構成します。</span><span class="sxs-lookup"><span data-stu-id="53d33-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="53d33-701">開発者は、アプリの例外をデバッグするときに [開発者例外ページ](xref:fundamentals/error-handling)を強制的に読み込むため、*web.config* ファイルでこの値を一時的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="53d33-702">`CONFIG_DIR` はユーザー定義の環境変数の例です。開発者はここに、アプリの構成ファイルを読み込むためのパスを形成するために起動時に値を読み取るコードを記述してあります。</span><span class="sxs-lookup"><span data-stu-id="53d33-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="53d33-703">インターネットなどの信頼されていないネットワークにアクセスできないステージング サーバーやテスト サーバーでは、`ASPNETCORE_ENVIRONMENT` 環境変数を `Development` に設定するだけです。</span><span class="sxs-lookup"><span data-stu-id="53d33-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="53d33-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="53d33-704">app_offline.htm</span></span>

<span data-ttu-id="53d33-705">*app_offline.htm* という名前のファイルがアプリのルート ディレクトリで検出された場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、受信要求の処理を停止することを試みます。</span><span class="sxs-lookup"><span data-stu-id="53d33-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="53d33-706">`shutdownTimeLimit` で定義されている秒数が経過してもまだアプリが実行している場合、ASP.NET Core モジュールは実行中のプロセスを強制終了します。</span><span class="sxs-lookup"><span data-stu-id="53d33-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="53d33-707">*app_offline.htm* ファイルが存在している間、ASP.NET Core モジュールは、*app_offline.htm* ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="53d33-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="53d33-708">*app_offline.htm* ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="53d33-709">起動エラー ページ</span><span class="sxs-lookup"><span data-stu-id="53d33-709">Start-up error page</span></span>

<span data-ttu-id="53d33-710">ASP.NET Core モジュールが、バックエンド プロセスの起動に失敗した場合、またはバックエンド プロセスは開始しても構成されているポートでのリッスンに失敗した場合は、*502.5 処理エラー* 状態コード ページが表示されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="53d33-711">このページを抑制して、既定の IIS 502 状態コード ページに戻すには、`disableStartUpErrorPage` 属性を使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="53d33-712">カスタム エラー メッセージの構成方法について詳しくは、[HTTP エラー \<httpErrors>](/iis/configuration/system.webServer/httpErrors/) に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="53d33-713">ログの作成とリダイレクト</span><span class="sxs-lookup"><span data-stu-id="53d33-713">Log creation and redirection</span></span>

<span data-ttu-id="53d33-714">`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="53d33-715">`stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="53d33-716">アプリ プールは、ログが書き込まれる場所への書き込みアクセス権を持っている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\<app_pool_name>` を使います)。</span><span class="sxs-lookup"><span data-stu-id="53d33-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="53d33-717">プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="53d33-718">ログが使用するディスク領域を制限するのは、ホストの役割です。</span><span class="sxs-lookup"><span data-stu-id="53d33-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="53d33-719">stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。</span><span class="sxs-lookup"><span data-stu-id="53d33-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="53d33-720">一般的なアプリ ログの目的には、stdout ログを使わないでください。</span><span class="sxs-lookup"><span data-stu-id="53d33-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="53d33-721">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="53d33-722">詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="53d33-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="53d33-723">ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="53d33-724">ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( *.log*) を `stdoutLogFile` パスの最後のセグメント (通常は *stdout*) にアンダースコアで区切って追加することで構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="53d33-725">`stdoutLogFile` パスが *stdout* で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、*stdout_20180205194132_1934.log* になります。</span><span class="sxs-lookup"><span data-stu-id="53d33-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="53d33-726">次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="53d33-727">AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="53d33-728">Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="53d33-729">Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="53d33-730">ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="53d33-731">パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53d33-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="53d33-732">プロキシの構成で HTTP プロトコルとペアリング トークンを使用する</span><span class="sxs-lookup"><span data-stu-id="53d33-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="53d33-733">ASP.NET Core モジュールと Kestrel の間に作成されるプロキシは、HTTP プロトコルを使います。</span><span class="sxs-lookup"><span data-stu-id="53d33-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="53d33-734">モジュールと Kestrel の間のトラフィックが、サーバーから離れた場所で傍受される危険はありません。</span><span class="sxs-lookup"><span data-stu-id="53d33-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="53d33-735">ペアリング トークンを使用すると、Kestrel によって受信される要求が IIS によってプロキシされたものであり、他のソースからのものでないことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="53d33-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="53d33-736">ペアリング トークンは、モジュールによって作成されて、環境変数 (`ASPNETCORE_TOKEN`) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="53d33-737">ペアリング トークンはまた、プロキシされたすべての要求のヘッダー (`MS-ASPNETCORE-TOKEN`) にも設定されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="53d33-738">IIS ミドルウェアは、受信した各要求をチェックし、ペアリング トークン ヘッダーの値が環境変数の値と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="53d33-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="53d33-739">トークンの値が一致しない場合、要求はログに記録され、拒否されます。</span><span class="sxs-lookup"><span data-stu-id="53d33-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="53d33-740">ペアリング トークン環境変数およびモジュールと Kestrel の間のトラフィックには、サーバーから離れた場所からアクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="53d33-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="53d33-741">ペアリング トークンの値がわからなければ、攻撃者は IIS ミドルウェアのチェックをバイパスする要求を送信できません。</span><span class="sxs-lookup"><span data-stu-id="53d33-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="53d33-742">IIS 共有構成での ASP.NET Core モジュール</span><span class="sxs-lookup"><span data-stu-id="53d33-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="53d33-743">ASP.NET Core モジュールのインストーラーは、**TrustedInstaller** アカウントのアクセス許可を使って実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="53d33-744">ローカル システム アカウントには、IIS 共有構成によって使われる共有パスに対する変更アクセス許可がないため、インストーラーが共有上の *applicationHost.config* ファイル内のモジュール設定を構成しようとすると、アクセス拒否エラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="53d33-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="53d33-745">IIS 共有構成を使うときは、次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="53d33-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="53d33-746">IIS 共有構成を無効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="53d33-747">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="53d33-747">Run the installer.</span></span>
1. <span data-ttu-id="53d33-748">更新された *applicationHost.config* ファイルを共有にエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="53d33-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="53d33-749">IIS 共有構成を再び有効にします。</span><span class="sxs-lookup"><span data-stu-id="53d33-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="53d33-750">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="53d33-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="53d33-751">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="53d33-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="53d33-752">ホスティング システム上で、 *%windir%\System32\inetsrv* に移動します。</span><span class="sxs-lookup"><span data-stu-id="53d33-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="53d33-753">*aspnetcore.dll* ファイルを探します。</span><span class="sxs-lookup"><span data-stu-id="53d33-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="53d33-754">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="53d33-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="53d33-755">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="53d33-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="53d33-756">モジュールのホスティング バンドル インストーラーのログは、*C:\\Users\\%UserName%\\AppData\\Local\\Temp* にあります。ファイルの名前は *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log* です。</span><span class="sxs-lookup"><span data-stu-id="53d33-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="53d33-757">モジュール、スキーマ、構成ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="53d33-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="53d33-758">Module</span><span class="sxs-lookup"><span data-stu-id="53d33-758">Module</span></span>

<span data-ttu-id="53d33-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="53d33-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="53d33-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="53d33-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="53d33-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="53d33-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="53d33-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="53d33-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="53d33-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="53d33-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="53d33-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="53d33-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="53d33-765">Schema</span><span class="sxs-lookup"><span data-stu-id="53d33-765">Schema</span></span>

<span data-ttu-id="53d33-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-766">**IIS**</span></span>

* <span data-ttu-id="53d33-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="53d33-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="53d33-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-768">**IIS Express**</span></span>

* <span data-ttu-id="53d33-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="53d33-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="53d33-770">構成</span><span class="sxs-lookup"><span data-stu-id="53d33-770">Configuration</span></span>

<span data-ttu-id="53d33-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="53d33-771">**IIS**</span></span>

* <span data-ttu-id="53d33-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="53d33-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="53d33-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="53d33-773">**IIS Express**</span></span>

* <span data-ttu-id="53d33-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="53d33-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="53d33-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="53d33-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="53d33-776">ファイルは、*applicationHost.config* ファイルで *aspnetcore* を検索することにより見つかります。</span><span class="sxs-lookup"><span data-stu-id="53d33-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="53d33-777">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="53d33-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="53d33-778">[ASP.NET Core モジュール参照ソース [既定のブランチ (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): **[Branch]** ドロップダウン リストを使用して、特定のリリース (たとえば、`release/3.1`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="53d33-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
