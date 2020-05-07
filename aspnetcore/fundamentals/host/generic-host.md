---
title: .NET での汎用ホスト
author: rick-anderson
description: アプリの起動と有効期間の管理を行う .NET Core 汎用ホストについて説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/generic-host
ms.openlocfilehash: 268c507ee35c9c0432c3dd2da2a389308531b9f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775597"
---
# <a name="net-generic-host"></a><span data-ttu-id="d202a-103">.NET での汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="d202a-103">.NET Generic Host</span></span>

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

<span data-ttu-id="d202a-104">ASP.NET Core テンプレートでは、.NET Core の汎用ホストである <xref:Microsoft.Extensions.Hosting.HostBuilder> が生成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-104">The ASP.NET Core templates create a .NET Core Generic Host, <xref:Microsoft.Extensions.Hosting.HostBuilder>.</span></span>

## <a name="host-definition"></a><span data-ttu-id="d202a-105">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="d202a-105">Host definition</span></span>

<span data-ttu-id="d202a-106">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="d202a-106">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d202a-107">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="d202a-107">Dependency injection (DI)</span></span>
* <span data-ttu-id="d202a-108">ログの記録</span><span class="sxs-lookup"><span data-stu-id="d202a-108">Logging</span></span>
* <span data-ttu-id="d202a-109">構成</span><span class="sxs-lookup"><span data-stu-id="d202a-109">Configuration</span></span>
* <span data-ttu-id="d202a-110">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="d202a-110">`IHostedService` implementations</span></span>

<span data-ttu-id="d202a-111">ホストを開始すると、DI コンテナー内で検出された <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装に対して `IHostedService.StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-111">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="d202a-112">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="d202a-112">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d202a-113">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="d202a-113">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d202a-114">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="d202a-114">Set up a host</span></span>

<span data-ttu-id="d202a-115">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-115">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d202a-116">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-116">The `Main` method:</span></span>

* <span data-ttu-id="d202a-117">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-117">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d202a-118">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-118">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d202a-119">ASP.NET Core の web テンプレートでは、汎用ホストを作成するために次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-119">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d202a-120">次のコードでは、HTTP 以外のワークロードを使用して、汎用ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-120">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="d202a-121">次のコードでは、`IHostedService` の実装が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-121">The `IHostedService` implementation is added to the DI container:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d202a-122">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-122">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d202a-123">上記のコードは、ASP.NET Core テンプレートによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-123">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="d202a-124">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="d202a-124">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d202a-125">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="d202a-125">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d202a-126">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d202a-126">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d202a-127">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="d202a-127">Default builder settings</span></span>

<span data-ttu-id="d202a-128"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-128">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d202a-129">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-129">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d202a-130">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-130">Loads host configuration from:</span></span>
  * <span data-ttu-id="d202a-131">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="d202a-131">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d202a-132">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d202a-132">Command-line arguments.</span></span>
* <span data-ttu-id="d202a-133">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-133">Loads app configuration from:</span></span>
  * <span data-ttu-id="d202a-134">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="d202a-134">*appsettings.json*.</span></span>
  * <span data-ttu-id="d202a-135">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="d202a-135">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d202a-136">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="d202a-136">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d202a-137">環境変数。</span><span class="sxs-lookup"><span data-stu-id="d202a-137">Environment variables.</span></span>
  * <span data-ttu-id="d202a-138">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d202a-138">Command-line arguments.</span></span>
* <span data-ttu-id="d202a-139">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-139">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d202a-140">コンソール</span><span class="sxs-lookup"><span data-stu-id="d202a-140">Console</span></span>
  * <span data-ttu-id="d202a-141">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d202a-141">Debug</span></span>
  * <span data-ttu-id="d202a-142">EventSource</span><span class="sxs-lookup"><span data-stu-id="d202a-142">EventSource</span></span>
  * <span data-ttu-id="d202a-143">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="d202a-143">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d202a-144">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d202a-144">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d202a-145">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-145">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d202a-146">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-146">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d202a-147">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-147">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d202a-148">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-148">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d202a-149">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-149">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d202a-150">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-150">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d202a-151">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d202a-151">Enables IIS integration.</span></span> <span data-ttu-id="d202a-152">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-152">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d202a-153">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-153">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d202a-154">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="d202a-154">Framework-provided services</span></span>

<span data-ttu-id="d202a-155">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-155">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d202a-156">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-156">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d202a-157">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-157">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d202a-158">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d202a-158">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d202a-159">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-159">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d202a-160">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-160">IHostApplicationLifetime</span></span>

<span data-ttu-id="d202a-161">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="d202a-161">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d202a-162">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="d202a-162">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d202a-163">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="d202a-163">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d202a-164">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="d202a-164">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d202a-165">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-165">IHostLifetime</span></span>

<span data-ttu-id="d202a-166"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-166">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d202a-167">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-167">The last implementation registered is used.</span></span>

<span data-ttu-id="d202a-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="d202a-168">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d202a-169">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d202a-169">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d202a-170"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-170">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d202a-171">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="d202a-171">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d202a-172">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d202a-172">IHostEnvironment</span></span>

<span data-ttu-id="d202a-173">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="d202a-173">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d202a-174">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d202a-174">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d202a-175">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d202a-175">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d202a-176">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d202a-176">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d202a-177">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-177">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d202a-178">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-178">Host configuration</span></span>

<span data-ttu-id="d202a-179">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-179">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d202a-180">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-180">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d202a-181">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d202a-181">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d202a-182">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-182">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d202a-183">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-183">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-184">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-184">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d202a-185">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-185">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d202a-186">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-186">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d202a-187">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-187">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d202a-188">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="d202a-188">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d202a-189">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-189">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d202a-190">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-190">App configuration</span></span>

<span data-ttu-id="d202a-191">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-191">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d202a-192">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-192">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-193">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-193">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d202a-194">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-194">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d202a-195">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-195">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d202a-196">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-196">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d202a-197">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="d202a-197">Settings for all app types</span></span>

<span data-ttu-id="d202a-198">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="d202a-198">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d202a-199">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-199">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d202a-200">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d202a-200">ApplicationName</span></span>

<span data-ttu-id="d202a-201">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-201">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d202a-202">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d202a-202">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d202a-203">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-203">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-204">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="d202a-204">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d202a-205">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d202a-205">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d202a-206">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-206">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d202a-207">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-207">ContentRoot</span></span>

<span data-ttu-id="d202a-208">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-208">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d202a-209">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="d202a-209">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d202a-210">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d202a-210">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d202a-211">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-211">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-212">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="d202a-212">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d202a-213">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d202a-213">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d202a-214">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-214">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d202a-215">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="d202a-215">For more information, see:</span></span>

* [<span data-ttu-id="d202a-216">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="d202a-216">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d202a-217">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-217">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d202a-218">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d202a-218">EnvironmentName</span></span>

<span data-ttu-id="d202a-219">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-219">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d202a-220">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-220">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d202a-221">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="d202a-221">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d202a-222">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d202a-222">**Key**: `environment`</span></span>  
<span data-ttu-id="d202a-223">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-223">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-224">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d202a-224">**Default**: `Production`</span></span>  
<span data-ttu-id="d202a-225">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d202a-225">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d202a-226">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-226">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d202a-227">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d202a-227">ShutdownTimeout</span></span>

<span data-ttu-id="d202a-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-228">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d202a-229">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="d202a-229">The default value is five seconds.</span></span>  <span data-ttu-id="d202a-230">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d202a-230">During the timeout period, the host:</span></span>

* <span data-ttu-id="d202a-231">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="d202a-231">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d202a-232">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d202a-232">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d202a-233">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="d202a-233">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d202a-234">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="d202a-234">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d202a-235">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="d202a-235">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d202a-236">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d202a-236">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d202a-237">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="d202a-237">**Type**: `int`</span></span>  
<span data-ttu-id="d202a-238">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="d202a-238">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d202a-239">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d202a-239">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d202a-240">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-240">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d202a-241">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-241">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="d202a-242">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="d202a-242">Settings for web apps</span></span>

<span data-ttu-id="d202a-243">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-243">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d202a-244">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-244">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d202a-245">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-245">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d202a-246">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="d202a-246">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d202a-247">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d202a-247">CaptureStartupErrors</span></span>

<span data-ttu-id="d202a-248">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="d202a-248">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d202a-249">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="d202a-249">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d202a-250">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d202a-250">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d202a-251">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-251">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-252">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-252">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d202a-253">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d202a-253">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d202a-254">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-254">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d202a-255">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d202a-255">DetailedErrors</span></span>

<span data-ttu-id="d202a-256">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-256">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d202a-257">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d202a-257">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d202a-258">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-258">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-259">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="d202a-259">**Default**: `false`</span></span>  
<span data-ttu-id="d202a-260">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d202a-260">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d202a-261">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-261">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d202a-262">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d202a-262">HostingStartupAssemblies</span></span>

<span data-ttu-id="d202a-263">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="d202a-263">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d202a-264">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-264">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d202a-265">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-265">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d202a-266">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d202a-266">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d202a-267">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-267">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-268">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="d202a-268">**Default**: Empty string</span></span>  
<span data-ttu-id="d202a-269">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d202a-269">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d202a-270">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-270">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d202a-271">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d202a-271">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d202a-272">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="d202a-272">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d202a-273">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d202a-273">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d202a-274">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-274">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-275">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="d202a-275">**Default**: Empty string</span></span>  
<span data-ttu-id="d202a-276">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d202a-276">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d202a-277">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-277">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d202a-278">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d202a-278">HTTPS_Port</span></span>

<span data-ttu-id="d202a-279">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="d202a-279">The HTTPS redirect port.</span></span> <span data-ttu-id="d202a-280">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-280">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d202a-281">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="d202a-281">**Key**: `https_port`</span></span>  
<span data-ttu-id="d202a-282">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-282">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-283">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="d202a-283">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d202a-284">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d202a-284">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d202a-285">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-285">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d202a-286">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d202a-286">PreferHostingUrls</span></span>

<span data-ttu-id="d202a-287">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-287">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d202a-288">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d202a-288">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d202a-289">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-289">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-290">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="d202a-290">**Default**: `true`</span></span>  
<span data-ttu-id="d202a-291">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d202a-291">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d202a-292">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-292">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d202a-293">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d202a-293">PreventHostingStartup</span></span>

<span data-ttu-id="d202a-294">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="d202a-294">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d202a-295">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-295">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d202a-296">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d202a-296">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d202a-297">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-297">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-298">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="d202a-298">**Default**: `false`</span></span>  
<span data-ttu-id="d202a-299">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d202a-299">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d202a-300">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-300">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d202a-301">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d202a-301">StartupAssembly</span></span>

<span data-ttu-id="d202a-302">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="d202a-302">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d202a-303">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d202a-303">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d202a-304">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-304">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-305">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="d202a-305">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d202a-306">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d202a-306">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d202a-307">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-307">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d202a-308">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-308">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d202a-309">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-309">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d202a-310">URL</span><span class="sxs-lookup"><span data-stu-id="d202a-310">URLs</span></span>

<span data-ttu-id="d202a-311">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="d202a-311">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d202a-312">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="d202a-312">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d202a-313">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-313">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d202a-314">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-314">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d202a-315">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="d202a-315">Supported formats vary among servers.</span></span>

<span data-ttu-id="d202a-316">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="d202a-316">**Key**: `urls`</span></span>  
<span data-ttu-id="d202a-317">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-317">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-318">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d202a-318">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d202a-319">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d202a-319">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d202a-320">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-320">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d202a-321">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-321">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d202a-322">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-322">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d202a-323">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-323">WebRoot</span></span>

<span data-ttu-id="d202a-324">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-324">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d202a-325">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-325">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d202a-326">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="d202a-326">**Key**: `webroot`</span></span>  
<span data-ttu-id="d202a-327">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-327">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-328">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="d202a-328">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d202a-329">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-329">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d202a-330">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d202a-330">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d202a-331">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-331">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d202a-332">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="d202a-332">For more information, see:</span></span>

* [<span data-ttu-id="d202a-333">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="d202a-333">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d202a-334">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-334">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d202a-335">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="d202a-335">Manage the host lifetime</span></span>

<span data-ttu-id="d202a-336">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="d202a-336">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d202a-337">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="d202a-337">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d202a-338">実行</span><span class="sxs-lookup"><span data-stu-id="d202a-338">Run</span></span>

<span data-ttu-id="d202a-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="d202a-339"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d202a-340">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-340">RunAsync</span></span>

<span data-ttu-id="d202a-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="d202a-341"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d202a-342">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-342">RunConsoleAsync</span></span>

<span data-ttu-id="d202a-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="d202a-343"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d202a-344">[開始]</span><span class="sxs-lookup"><span data-stu-id="d202a-344">Start</span></span>

<span data-ttu-id="d202a-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-345"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d202a-346">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-346">StartAsync</span></span>

<span data-ttu-id="d202a-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-347"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d202a-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="d202a-348"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d202a-349">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-349">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d202a-350">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-350">StopAsync</span></span>

<span data-ttu-id="d202a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-351"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d202a-352">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d202a-352">WaitForShutdown</span></span>

<span data-ttu-id="d202a-353"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-353"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d202a-354">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-354">WaitForShutdownAsync</span></span>

<span data-ttu-id="d202a-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-355"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d202a-356">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="d202a-356">External control</span></span>

<span data-ttu-id="d202a-357">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-357">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d202a-358">ASP.NET Core アプリはホストを構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="d202a-358">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="d202a-359">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="d202a-359">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="d202a-360">この記事では、HTTP 要求を処理しないアプリに使用される、ASP.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について説明します。</span><span class="sxs-lookup"><span data-stu-id="d202a-360">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="d202a-361">汎用ホストの目的は、Web ホスト API から HTTP パイプラインを切り離して、多様なホスト シナリオを有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="d202a-361">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="d202a-362">メッセージング、バックグラウンド タスク、汎用ホストに基づくその他の HTTP ワークロードに対して、構成、依存関係の挿入 (DI)、ログなどの横断的機能によるメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="d202a-362">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="d202a-363">汎用ホストは ASP.NET Core 2.1 の新機能であり、Web ホスティングのシナリオには適していません。</span><span class="sxs-lookup"><span data-stu-id="d202a-363">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="d202a-364">Web ホスティングのシナリオの場合は、[Web ホスト](xref:fundamentals/host/web-host)を使ってください。</span><span class="sxs-lookup"><span data-stu-id="d202a-364">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="d202a-365">汎用ホストは将来のリリースで Web ホストに代わるものであり、HTTP と非 HTTP 両方のシナリオのプライマリ ホスト API として機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="d202a-365">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="d202a-366">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d202a-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d202a-367">[Visual Studio Code](https://code.visualstudio.com/) でサンプル アプリを実行するときは、"*外部ターミナルまたは統合ターミナル*" を使います。</span><span class="sxs-lookup"><span data-stu-id="d202a-367">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="d202a-368">`internalConsole` ではサンプルを実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="d202a-368">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="d202a-369">Visual Studio Code でコンソールを設定するには:</span><span class="sxs-lookup"><span data-stu-id="d202a-369">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="d202a-370">*.vscode/launch.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d202a-370">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="d202a-371">**.NET Core Launch (console)** の構成で、**console** エントリを探します。</span><span class="sxs-lookup"><span data-stu-id="d202a-371">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="d202a-372">値を `externalTerminal` または `integratedTerminal` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-372">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="d202a-373">はじめに</span><span class="sxs-lookup"><span data-stu-id="d202a-373">Introduction</span></span>

<span data-ttu-id="d202a-374">汎用ホスト ライブラリは、<xref:Microsoft.Extensions.Hosting> 名前空間で使用でき、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) パッケージで提供されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-374">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="d202a-375">`Microsoft.Extensions.Hosting` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="d202a-375">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="d202a-376"><xref:Microsoft.Extensions.Hosting.IHostedService> がコード実行のエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="d202a-376"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="d202a-377"><xref:Microsoft.Extensions.Hosting.IHostedService> の各実装は、[ConfigureServices でのサービス登録](#configureservices)の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-377">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="d202a-378">ホストが開始するときは <xref:Microsoft.Extensions.Hosting.IHostedService> ごとに <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> が呼び出され、ホストが正常に終了するときは登録と逆の順序で <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-378"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d202a-379">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="d202a-379">Set up a host</span></span>

<span data-ttu-id="d202a-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> は、ライブラリとアプリがホストの初期化、ビルド、実行に使用するメイン コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="d202a-380"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="d202a-381">オプション</span><span class="sxs-lookup"><span data-stu-id="d202a-381">Options</span></span>

<span data-ttu-id="d202a-382"><xref:Microsoft.Extensions.Hosting.IHost> の <xref:Microsoft.Extensions.Hosting.HostOptions> 構成オプションです。</span><span class="sxs-lookup"><span data-stu-id="d202a-382"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="d202a-383">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="d202a-383">Shutdown timeout</span></span>

<span data-ttu-id="d202a-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> によって <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-384"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d202a-385">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="d202a-385">The default value is five seconds.</span></span>

<span data-ttu-id="d202a-386">次に示す `Program.Main` のオプション構成では、既定の 5 秒のシャットダウン タイムアウトが 20 秒に延長されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-386">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a><span data-ttu-id="d202a-387">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="d202a-387">Default services</span></span>

<span data-ttu-id="d202a-388">次のサービスは、ホストの初期化中に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-388">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="d202a-389">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="d202a-389">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="d202a-390">[構成](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="d202a-390">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="d202a-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d202a-391"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="d202a-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="d202a-392"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="d202a-393">[オプション](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="d202a-393">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="d202a-394">[ログ](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="d202a-394">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d202a-395">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-395">Host configuration</span></span>

<span data-ttu-id="d202a-396">ホストの構成は、次のようにして作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-396">Host configuration is created by:</span></span>

* <span data-ttu-id="d202a-397"><xref:Microsoft.Extensions.Hosting.IHostBuilder> で拡張メソッドを呼び出して、[コンテンツ ルート](#content-root)と[環境](#environment)を設定する。</span><span class="sxs-lookup"><span data-stu-id="d202a-397">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="d202a-398"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> の構成プロバイダーから構成を読み取る。</span><span class="sxs-lookup"><span data-stu-id="d202a-398">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="d202a-399">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="d202a-399">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="d202a-400">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="d202a-400">Application key (name)</span></span>

<span data-ttu-id="d202a-401">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-401">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="d202a-402">値を明示的に設定するには、[HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-402">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="d202a-403">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d202a-403">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d202a-404">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-404">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-405">**既定**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="d202a-405">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="d202a-406">**次を使用して設定**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="d202a-406">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="d202a-407">**環境変数**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d202a-407">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="d202a-408">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="d202a-408">Content root</span></span>

<span data-ttu-id="d202a-409">この設定では、ホストがコンテンツ ファイルの検索を開始する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-409">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="d202a-410">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d202a-410">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d202a-411">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-411">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-412">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-412">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="d202a-413">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="d202a-413">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="d202a-414">**環境変数**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d202a-414">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d202a-415">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="d202a-415">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="d202a-416">詳細については、[基礎: コンテンツ ルート](xref:fundamentals/index#content-root)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-416">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="d202a-417">環境</span><span class="sxs-lookup"><span data-stu-id="d202a-417">Environment</span></span>

<span data-ttu-id="d202a-418">アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-418">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="d202a-419">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d202a-419">**Key**: `environment`</span></span>  
<span data-ttu-id="d202a-420">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-420">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-421">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d202a-421">**Default**: `Production`</span></span>  
<span data-ttu-id="d202a-422">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="d202a-422">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="d202a-423">**環境変数**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="d202a-423">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="d202a-424">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-424">The environment can be set to any value.</span></span> <span data-ttu-id="d202a-425">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-425">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d202a-426">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="d202a-426">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="d202a-427">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="d202a-427">ConfigureHostConfiguration</span></span>

<span data-ttu-id="d202a-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、ホストの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-428"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="d202a-429">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> をアプリのビルド プロセスで使用するための初期化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-429">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="d202a-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-430"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-431">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-431">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d202a-432">既定ではプロバイダーが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="d202a-432">No providers are included by default.</span></span> <span data-ttu-id="d202a-433">次のような、アプリが <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> で必要とする構成プロバイダーを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-433">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="d202a-434">ファイルの構成 (*hostsettings.json* ファイルからなど)。</span><span class="sxs-lookup"><span data-stu-id="d202a-434">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="d202a-435">環境変数の構成。</span><span class="sxs-lookup"><span data-stu-id="d202a-435">Environment variable configuration.</span></span>
* <span data-ttu-id="d202a-436">コマンドライン引数の構成。</span><span class="sxs-lookup"><span data-stu-id="d202a-436">Command-line argument configuration.</span></span>
* <span data-ttu-id="d202a-437">その他に必要なすべての構成プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="d202a-437">Any other required configuration providers.</span></span>

<span data-ttu-id="d202a-438">ホストのファイル構成は、いずれかの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)に対する呼び出しの前に `SetBasePath` のアプリのベース パスを指定することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="d202a-438">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="d202a-439">サンプル アプリは、JSON ファイル (*hostsettings.json*) を使用し、<xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> を呼び出して、ファイルのホスト構成設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-439">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="d202a-440">ホストの[環境変数の構成](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を追加するには、ホスト ビルダーで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-440">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="d202a-441">`AddEnvironmentVariables` は、オプションのユーザー定義プレフィックスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="d202a-441">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="d202a-442">サンプル アプリは、`PREFIX_` のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-442">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="d202a-443">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-443">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d202a-444">サンプル アプリのホストが構成されると、`PREFIX_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="d202a-444">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d202a-445">開発中に [Visual Studio](https://visualstudio.microsoft.com) を使用している、または `dotnet run` を使用してアプリを実行している場合は、環境変数を *Properties/launchSettings.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-445">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="d202a-446">[Visual Studio Code](https://code.visualstudio.com/) では、開発中に環境変数を *.vscode/launch.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-446">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="d202a-447">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-447">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="d202a-448">[コマンドラインの構成](xref:fundamentals/configuration/index#command-line-configuration-provider)は、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>を呼び出すことで追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-448">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="d202a-449">コマンドラインの構成は、コマンドライン引数を許可して以前の構成プロバイダーから提供された構成をオーバーライドするために最後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-449">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="d202a-450">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d202a-450">*hostsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="d202a-451">追加の構成は、[applicationName](#application-key-name) と [contentRoot](#content-root) キーで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-451">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="d202a-452"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を使う `HostBuilder` の構成の例:</span><span class="sxs-lookup"><span data-stu-id="d202a-452">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="d202a-453">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="d202a-453">ConfigureAppConfiguration</span></span>

<span data-ttu-id="d202a-454">アプリの構成は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-454">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="d202a-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、アプリの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-455"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="d202a-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-456"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-457">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-457">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="d202a-458"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> によって作成された構成は、以降の操作に対する [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) において、および <xref:Microsoft.Extensions.Hosting.IHost.Services*>サービス内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-458">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="d202a-459">アプリの構成は、[ConfigureHostConfiguration](#configurehostconfiguration) によって提供されたホストの構成を自動的に受信します。</span><span class="sxs-lookup"><span data-stu-id="d202a-459">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="d202a-460"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を使うアプリ構成の例:</span><span class="sxs-lookup"><span data-stu-id="d202a-460">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="d202a-461">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d202a-461">*appsettings.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="d202a-462">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="d202a-462">*appsettings.Development.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="d202a-463">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="d202a-463">*appsettings.Production.json*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="d202a-464">設定ファイルを出力ディレクトリに移動するには、プロジェクト ファイルの設定ファイルを [MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) として指定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-464">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="d202a-465">サンプル アプリはその JSON アプリ設定ファイルと、次の `<Content>` 項目を含む *hostsettings.json* を移動します。</span><span class="sxs-lookup"><span data-stu-id="d202a-465">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="d202a-466"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> や <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> などの構成拡張メソッドでは、[Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) や [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) など、追加の NuGet パッケージを必要とします。</span><span class="sxs-lookup"><span data-stu-id="d202a-466">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="d202a-467">アプリが [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使用しない限り、中心となる [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) パッケージに加えて、これらのパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-467">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="d202a-468">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-468">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="d202a-469">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d202a-469">ConfigureServices</span></span>

<span data-ttu-id="d202a-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> は、アプリの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーにサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-470"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d202a-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-471"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d202a-472">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="d202a-472">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="d202a-473">詳細については、「<xref:fundamentals/host/hosted-services>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-473">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="d202a-474">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)では、`AddHostedService` 拡張メソッドを使って、有効期間イベント `LifetimeEventsHostedService` と時刻指定付きバックグラウンド タスク `TimedHostedService` のサービスをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-474">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="d202a-475">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="d202a-475">ConfigureLogging</span></span>

<span data-ttu-id="d202a-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> は、指定された <xref:Microsoft.Extensions.Logging.ILoggingBuilder> を構成するためのデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-476"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="d202a-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-477"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="d202a-478">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-478">UseConsoleLifetime</span></span>

<span data-ttu-id="d202a-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> では、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-479"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="d202a-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> は、[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="d202a-480"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="d202a-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の有効期間の実装として事前に登録されています。</span><span class="sxs-lookup"><span data-stu-id="d202a-481">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="d202a-482">最後に登録された有効期間が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-482">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="d202a-483">コンテナーの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-483">Container configuration</span></span>

<span data-ttu-id="d202a-484">他のコンテナーの接続をサポートするため、ホストは <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-484">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="d202a-485">ファクトリの提供は、DI コンテナーの登録の一部ではなく、具象 DI コンテナーの作成に使用されるホストの組み込みです。</span><span class="sxs-lookup"><span data-stu-id="d202a-485">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="d202a-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) は、アプリのサービス プロバイダーの作成に使われる既定のファクトリをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d202a-486">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="d202a-487">カスタム コンテナーの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> メソッドによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-487">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="d202a-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> は、基盤のホスト API を基にしてコンテナーを構成するための、厳密に型指定されたエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d202a-488"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="d202a-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-489"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="d202a-490">アプリのサービス コンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-490">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="d202a-491">サービス コンテナーのファクトリを提供します。</span><span class="sxs-lookup"><span data-stu-id="d202a-491">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="d202a-492">ファクトリを使って、アプリのカスタム サービス コンテナーを構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-492">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="d202a-493">機能拡張</span><span class="sxs-lookup"><span data-stu-id="d202a-493">Extensibility</span></span>

<span data-ttu-id="d202a-494">ホスト拡張機能は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> 上の拡張メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-494">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="d202a-495">次の例では、拡張メソッドが <xref:fundamentals/host/hosted-services> で示される [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) の例を使って <xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装を拡張する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d202a-495">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="d202a-496">アプリは `UseHostedService` 拡張メソッドを確率して `T` に渡されたホステッド サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="d202a-496">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a><span data-ttu-id="d202a-497">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="d202a-497">Manage the host</span></span>

<span data-ttu-id="d202a-498"><xref:Microsoft.Extensions.Hosting.IHost> の実装は、サービス コンテナーに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の実装の開始と停止を行います。</span><span class="sxs-lookup"><span data-stu-id="d202a-498">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d202a-499">実行</span><span class="sxs-lookup"><span data-stu-id="d202a-499">Run</span></span>

<span data-ttu-id="d202a-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="d202a-500"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a><span data-ttu-id="d202a-501">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-501">RunAsync</span></span>

<span data-ttu-id="d202a-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="d202a-502"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a><span data-ttu-id="d202a-503">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-503">RunConsoleAsync</span></span>

<span data-ttu-id="d202a-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="d202a-504"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a><span data-ttu-id="d202a-505">Start と StopAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-505">Start and StopAsync</span></span>

<span data-ttu-id="d202a-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-506"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="d202a-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-507"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a><span data-ttu-id="d202a-508">StartAsync と StopAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-508">StartAsync and StopAsync</span></span>

<span data-ttu-id="d202a-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> がアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="d202a-509"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="d202a-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> がアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="d202a-510"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a><span data-ttu-id="d202a-511">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d202a-511">WaitForShutdown</span></span>

<span data-ttu-id="d202a-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は、`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンする) などの <xref:Microsoft.Extensions.Hosting.IHostLifetime> を介してトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-512"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="d202a-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-513"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a><span data-ttu-id="d202a-514">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-514">WaitForShutdownAsync</span></span>

<span data-ttu-id="d202a-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-515"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a><span data-ttu-id="d202a-516">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="d202a-516">External control</span></span>

<span data-ttu-id="d202a-517">ホストの外部コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-517">External control of the host can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<span data-ttu-id="d202a-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="d202a-518"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d202a-519">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-519">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="d202a-520">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="d202a-520">IHostingEnvironment interface</span></span>

<span data-ttu-id="d202a-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> は、アプリのホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="d202a-521"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="d202a-522">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> を取得します。</span><span class="sxs-lookup"><span data-stu-id="d202a-522">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

<span data-ttu-id="d202a-523">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-523">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="d202a-524">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="d202a-524">IApplicationLifetime interface</span></span>

<span data-ttu-id="d202a-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> は、正常なシャットダウンの要求など、起動後とシャットダウンのアクティビティに対応します。</span><span class="sxs-lookup"><span data-stu-id="d202a-525"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="d202a-526">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する <xref:System.Action> メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="d202a-526">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="d202a-527">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="d202a-527">Cancellation Token</span></span> | <span data-ttu-id="d202a-528">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="d202a-528">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="d202a-529">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="d202a-529">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="d202a-530">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="d202a-530">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="d202a-531">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-531">All requests should be processed.</span></span> <span data-ttu-id="d202a-532">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-532">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="d202a-533">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="d202a-533">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="d202a-534">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="d202a-534">Requests may still be processing.</span></span> <span data-ttu-id="d202a-535">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-535">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="d202a-536">コンストラクターは任意のクラスに <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="d202a-536">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="d202a-537">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)は、`LifetimeEventsHostedService` クラス (<xref:Microsoft.Extensions.Hosting.IHostedService> の実装) へのコンストラクターの挿入を使って、イベントを登録します。</span><span class="sxs-lookup"><span data-stu-id="d202a-537">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="d202a-538">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="d202a-538">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="d202a-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="d202a-539"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="d202a-540">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="d202a-540">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d202a-541">ASP.NET Core テンプレートでは、.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-541">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

## <a name="host-definition"></a><span data-ttu-id="d202a-542">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="d202a-542">Host definition</span></span>

<span data-ttu-id="d202a-543">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="d202a-543">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="d202a-544">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="d202a-544">Dependency injection (DI)</span></span>
* <span data-ttu-id="d202a-545">ログの記録</span><span class="sxs-lookup"><span data-stu-id="d202a-545">Logging</span></span>
* <span data-ttu-id="d202a-546">構成</span><span class="sxs-lookup"><span data-stu-id="d202a-546">Configuration</span></span>
* <span data-ttu-id="d202a-547">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="d202a-547">`IHostedService` implementations</span></span>

<span data-ttu-id="d202a-548">ホストを開始すると、DI コンテナー内で検出された <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装に対して `IHostedService.StartAsync` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-548">When a host starts, it calls `IHostedService.StartAsync` on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> that it finds in the DI container.</span></span> <span data-ttu-id="d202a-549">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="d202a-549">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="d202a-550">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="d202a-550">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="d202a-551">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="d202a-551">Set up a host</span></span>

<span data-ttu-id="d202a-552">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-552">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="d202a-553">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-553">The `Main` method:</span></span>

* <span data-ttu-id="d202a-554">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-554">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="d202a-555">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-555">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="d202a-556">ASP.NET Core の web テンプレートでは、ホストを作成するために、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-556">The ASP.NET Core web templates generate the following code to create a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="d202a-557">次のコードでは、DI コンテナーに追加された `IHostedService` の実装を使用して、非 HTTP ワークロードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-557">The following code creates a non-HTTP workload with a `IHostedService` implementation added to the DI container.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

<span data-ttu-id="d202a-558">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-558">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="d202a-559">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="d202a-559">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="d202a-560">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="d202a-560">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="d202a-561">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d202a-561">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="d202a-562">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="d202a-562">Default builder settings</span></span>

<span data-ttu-id="d202a-563"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-563">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="d202a-564">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-564">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="d202a-565">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-565">Loads host configuration from:</span></span>
  * <span data-ttu-id="d202a-566">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="d202a-566">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="d202a-567">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d202a-567">Command-line arguments.</span></span>
* <span data-ttu-id="d202a-568">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-568">Loads app configuration from:</span></span>
  * <span data-ttu-id="d202a-569">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="d202a-569">*appsettings.json*.</span></span>
  * <span data-ttu-id="d202a-570">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="d202a-570">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="d202a-571">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="d202a-571">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="d202a-572">環境変数。</span><span class="sxs-lookup"><span data-stu-id="d202a-572">Environment variables.</span></span>
  * <span data-ttu-id="d202a-573">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d202a-573">Command-line arguments.</span></span>
* <span data-ttu-id="d202a-574">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-574">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="d202a-575">コンソール</span><span class="sxs-lookup"><span data-stu-id="d202a-575">Console</span></span>
  * <span data-ttu-id="d202a-576">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d202a-576">Debug</span></span>
  * <span data-ttu-id="d202a-577">EventSource</span><span class="sxs-lookup"><span data-stu-id="d202a-577">EventSource</span></span>
  * <span data-ttu-id="d202a-578">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="d202a-578">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="d202a-579">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d202a-579">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="d202a-580">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="d202a-580">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="d202a-581">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-581">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="d202a-582">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-582">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="d202a-583">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-583">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="d202a-584">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-584">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="d202a-585">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="d202a-585">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="d202a-586">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d202a-586">Enables IIS integration.</span></span> <span data-ttu-id="d202a-587">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-587">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="d202a-588">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-588">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="d202a-589">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="d202a-589">Framework-provided services</span></span>

<span data-ttu-id="d202a-590">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-590">The following services are registered automatically:</span></span>

* [<span data-ttu-id="d202a-591">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-591">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="d202a-592">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-592">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="d202a-593">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d202a-593">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="d202a-594">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-594">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="d202a-595">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-595">IHostApplicationLifetime</span></span>

<span data-ttu-id="d202a-596">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="d202a-596">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="d202a-597">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="d202a-597">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="d202a-598">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="d202a-598">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="d202a-599">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="d202a-599">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="d202a-600">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="d202a-600">IHostLifetime</span></span>

<span data-ttu-id="d202a-601"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-601">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="d202a-602">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-602">The last implementation registered is used.</span></span>

<span data-ttu-id="d202a-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="d202a-603">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="d202a-604">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="d202a-604">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="d202a-605"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-605">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="d202a-606">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="d202a-606">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="d202a-607">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="d202a-607">IHostEnvironment</span></span>

<span data-ttu-id="d202a-608">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="d202a-608">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="d202a-609">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d202a-609">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="d202a-610">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d202a-610">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="d202a-611">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="d202a-611">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="d202a-612">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-612">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="d202a-613">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-613">Host configuration</span></span>

<span data-ttu-id="d202a-614">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-614">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="d202a-615">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-615">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="d202a-616">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="d202a-616">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="d202a-617">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-617">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d202a-618">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-618">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-619">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-619">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="d202a-620">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-620">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="d202a-621">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-621">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="d202a-622">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-622">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="d202a-623">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="d202a-623">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="d202a-624">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-624">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="d202a-625">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="d202a-625">App configuration</span></span>

<span data-ttu-id="d202a-626">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-626">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="d202a-627">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-627">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="d202a-628">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-628">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="d202a-629">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-629">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="d202a-630">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-630">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="d202a-631">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-631">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="d202a-632">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="d202a-632">Settings for all app types</span></span>

<span data-ttu-id="d202a-633">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="d202a-633">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="d202a-634">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-634">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="d202a-635">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="d202a-635">ApplicationName</span></span>

<span data-ttu-id="d202a-636">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-636">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="d202a-637">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="d202a-637">**Key**: `applicationName`</span></span>  
<span data-ttu-id="d202a-638">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-638">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-639">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="d202a-639">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="d202a-640">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="d202a-640">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="d202a-641">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="d202a-641">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="d202a-642">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-642">ContentRoot</span></span>

<span data-ttu-id="d202a-643">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-643">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="d202a-644">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="d202a-644">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="d202a-645">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="d202a-645">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="d202a-646">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-646">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-647">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="d202a-647">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="d202a-648">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="d202a-648">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="d202a-649">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-649">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="d202a-650">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="d202a-650">For more information, see:</span></span>

* [<span data-ttu-id="d202a-651">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="d202a-651">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="d202a-652">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-652">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="d202a-653">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="d202a-653">EnvironmentName</span></span>

<span data-ttu-id="d202a-654">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-654">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="d202a-655">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-655">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="d202a-656">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="d202a-656">Values aren't case-sensitive.</span></span>

<span data-ttu-id="d202a-657">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="d202a-657">**Key**: `environment`</span></span>  
<span data-ttu-id="d202a-658">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-658">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-659">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="d202a-659">**Default**: `Production`</span></span>  
<span data-ttu-id="d202a-660">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="d202a-660">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="d202a-661">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-661">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="d202a-662">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="d202a-662">ShutdownTimeout</span></span>

<span data-ttu-id="d202a-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-663">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="d202a-664">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="d202a-664">The default value is five seconds.</span></span>  <span data-ttu-id="d202a-665">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="d202a-665">During the timeout period, the host:</span></span>

* <span data-ttu-id="d202a-666">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="d202a-666">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="d202a-667">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d202a-667">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="d202a-668">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="d202a-668">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="d202a-669">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="d202a-669">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="d202a-670">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="d202a-670">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="d202a-671">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="d202a-671">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="d202a-672">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="d202a-672">**Type**: `int`</span></span>  
<span data-ttu-id="d202a-673">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="d202a-673">**Default**: 5 seconds</span></span>  
<span data-ttu-id="d202a-674">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="d202a-674">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="d202a-675">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="d202a-675">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="d202a-676">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-676">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="d202a-677">変更時にアプリ構成の再度読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="d202a-677">Disable app configuration reload on change</span></span>

<span data-ttu-id="d202a-678">[既定](xref:fundamentals/configuration/index#default)では、*appsettings.json* と *appsettings.{Environment}.json* は、ファイルの変更時に再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-678">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="d202a-679">ASP.NET Core 5.0 Preview 3 以降でこの再度読み込み動作を無効にするには、`hostBuilder:reloadConfigOnChange` キーを `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="d202a-679">To disable this reload behavior in ASP.NET Core 5.0 Preview 3 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="d202a-680">**キー**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d202a-680">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d202a-681">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-681">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-682">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="d202a-682">**Default**: `true`</span></span>  
<span data-ttu-id="d202a-683">**コマンドライン引数**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d202a-683">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="d202a-684">**環境変数**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="d202a-684">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="d202a-685">コロン (`:`) の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。</span><span class="sxs-lookup"><span data-stu-id="d202a-685">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="d202a-686">詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-686">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="d202a-687">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="d202a-687">Settings for web apps</span></span>

<span data-ttu-id="d202a-688">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-688">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="d202a-689">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-689">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="d202a-690">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-690">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="d202a-691">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="d202a-691">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="d202a-692">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="d202a-692">CaptureStartupErrors</span></span>

<span data-ttu-id="d202a-693">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="d202a-693">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="d202a-694">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="d202a-694">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="d202a-695">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="d202a-695">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="d202a-696">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-696">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-697">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-697">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="d202a-698">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="d202a-698">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="d202a-699">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-699">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="d202a-700">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="d202a-700">DetailedErrors</span></span>

<span data-ttu-id="d202a-701">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-701">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="d202a-702">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="d202a-702">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="d202a-703">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-703">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-704">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="d202a-704">**Default**: `false`</span></span>  
<span data-ttu-id="d202a-705">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="d202a-705">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="d202a-706">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-706">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="d202a-707">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="d202a-707">HostingStartupAssemblies</span></span>

<span data-ttu-id="d202a-708">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="d202a-708">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="d202a-709">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="d202a-709">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="d202a-710">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-710">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="d202a-711">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d202a-711">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="d202a-712">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-712">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-713">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="d202a-713">**Default**: Empty string</span></span>  
<span data-ttu-id="d202a-714">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d202a-714">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="d202a-715">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-715">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="d202a-716">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="d202a-716">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="d202a-717">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="d202a-717">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="d202a-718">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="d202a-718">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="d202a-719">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-719">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-720">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="d202a-720">**Default**: Empty string</span></span>  
<span data-ttu-id="d202a-721">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="d202a-721">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="d202a-722">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-722">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="d202a-723">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="d202a-723">HTTPS_Port</span></span>

<span data-ttu-id="d202a-724">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="d202a-724">The HTTPS redirect port.</span></span> <span data-ttu-id="d202a-725">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-725">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d202a-726">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="d202a-726">**Key**: `https_port`</span></span>  
<span data-ttu-id="d202a-727">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-727">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-728">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="d202a-728">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="d202a-729">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="d202a-729">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="d202a-730">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-730">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="d202a-731">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="d202a-731">PreferHostingUrls</span></span>

<span data-ttu-id="d202a-732">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-732">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="d202a-733">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="d202a-733">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="d202a-734">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-734">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-735">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="d202a-735">**Default**: `true`</span></span>  
<span data-ttu-id="d202a-736">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="d202a-736">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="d202a-737">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-737">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="d202a-738">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="d202a-738">PreventHostingStartup</span></span>

<span data-ttu-id="d202a-739">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="d202a-739">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="d202a-740">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-740">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="d202a-741">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="d202a-741">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="d202a-742">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="d202a-742">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="d202a-743">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="d202a-743">**Default**: `false`</span></span>  
<span data-ttu-id="d202a-744">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="d202a-744">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="d202a-745">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-745">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="d202a-746">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="d202a-746">StartupAssembly</span></span>

<span data-ttu-id="d202a-747">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="d202a-747">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="d202a-748">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="d202a-748">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="d202a-749">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-749">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-750">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="d202a-750">**Default**: The app's assembly</span></span>  
<span data-ttu-id="d202a-751">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="d202a-751">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="d202a-752">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-752">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="d202a-753">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-753">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="d202a-754">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-754">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="d202a-755">URL</span><span class="sxs-lookup"><span data-stu-id="d202a-755">URLs</span></span>

<span data-ttu-id="d202a-756">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="d202a-756">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="d202a-757">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="d202a-757">For example, `http://localhost:123`.</span></span> <span data-ttu-id="d202a-758">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="d202a-758">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="d202a-759">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-759">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="d202a-760">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="d202a-760">Supported formats vary among servers.</span></span>

<span data-ttu-id="d202a-761">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="d202a-761">**Key**: `urls`</span></span>  
<span data-ttu-id="d202a-762">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-762">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-763">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="d202a-763">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="d202a-764">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="d202a-764">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="d202a-765">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-765">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="d202a-766">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-766">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="d202a-767">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d202a-767">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="d202a-768">WebRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-768">WebRoot</span></span>

<span data-ttu-id="d202a-769">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-769">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="d202a-770">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-770">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="d202a-771">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="d202a-771">**Key**: `webroot`</span></span>  
<span data-ttu-id="d202a-772">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="d202a-772">**Type**: `string`</span></span>  
<span data-ttu-id="d202a-773">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="d202a-773">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="d202a-774">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d202a-774">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="d202a-775">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="d202a-775">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="d202a-776">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-776">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="d202a-777">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="d202a-777">For more information, see:</span></span>

* [<span data-ttu-id="d202a-778">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="d202a-778">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="d202a-779">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="d202a-779">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="d202a-780">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="d202a-780">Manage the host lifetime</span></span>

<span data-ttu-id="d202a-781">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="d202a-781">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="d202a-782">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="d202a-782">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="d202a-783">実行</span><span class="sxs-lookup"><span data-stu-id="d202a-783">Run</span></span>

<span data-ttu-id="d202a-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="d202a-784"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="d202a-785">RunAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-785">RunAsync</span></span>

<span data-ttu-id="d202a-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="d202a-786"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="d202a-787">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-787">RunConsoleAsync</span></span>

<span data-ttu-id="d202a-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="d202a-788"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="d202a-789">[開始]</span><span class="sxs-lookup"><span data-stu-id="d202a-789">Start</span></span>

<span data-ttu-id="d202a-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="d202a-790"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="d202a-791">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-791">StartAsync</span></span>

<span data-ttu-id="d202a-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="d202a-792"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="d202a-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="d202a-793"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="d202a-794">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="d202a-794">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="d202a-795">StopAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-795">StopAsync</span></span>

<span data-ttu-id="d202a-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="d202a-796"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="d202a-797">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="d202a-797">WaitForShutdown</span></span>

<span data-ttu-id="d202a-798"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="d202a-798"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="d202a-799">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="d202a-799">WaitForShutdownAsync</span></span>

<span data-ttu-id="d202a-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d202a-800"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="d202a-801">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="d202a-801">External control</span></span>

<span data-ttu-id="d202a-802">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="d202a-802">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d202a-803">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d202a-803">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
