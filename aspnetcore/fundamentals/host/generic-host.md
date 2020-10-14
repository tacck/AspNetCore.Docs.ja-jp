---
title: ASP.NET Core の .NET 汎用ホスト
author: rick-anderson
description: ASP.NET Core アプリで .NET Core 汎用ホストを使用します。  汎用ホストが担当するのは、アプリの起動および有効期間の管理です。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
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
uid: fundamentals/host/generic-host
ms.openlocfilehash: d3de81ce7248372279b423da865513ee5db73c79
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762322"
---
# <a name="net-generic-host-in-aspnet-core"></a><span data-ttu-id="c57ad-104">ASP.NET Core の .NET 汎用ホスト</span><span class="sxs-lookup"><span data-stu-id="c57ad-104">.NET Generic Host in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c57ad-105">ASP.NET Core テンプレートでは、.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-105">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="c57ad-106">このトピックでは、ASP.NET Core での .NET 汎用ホストの使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-106">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="c57ad-107">コンソール アプリで .NET 汎用ホストを使用する方法の詳細については、「[.NET 汎用ホスト](/dotnet/core/extensions/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-107">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="c57ad-108">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="c57ad-108">Host definition</span></span>

<span data-ttu-id="c57ad-109">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="c57ad-109">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="c57ad-110">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="c57ad-110">Dependency injection (DI)</span></span>
* <span data-ttu-id="c57ad-111">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c57ad-111">Logging</span></span>
* <span data-ttu-id="c57ad-112">構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-112">Configuration</span></span>
* <span data-ttu-id="c57ad-113">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="c57ad-113">`IHostedService` implementations</span></span>

<span data-ttu-id="c57ad-114">ホストが起動すると、サービス コンテナーのホステッド サービスのコレクションに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装で <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-114">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="c57ad-115">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-115">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="c57ad-116">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-116">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="c57ad-117">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="c57ad-117">Set up a host</span></span>

<span data-ttu-id="c57ad-118">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-118">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="c57ad-119">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-119">The `Main` method:</span></span>

* <span data-ttu-id="c57ad-120">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-120">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="c57ad-121">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-121">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="c57ad-122">ASP.NET Core の web テンプレートでは、ホストを作成するために、次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-122">The ASP.NET Core web templates generate the following code to create a host:</span></span>

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

<span data-ttu-id="c57ad-123">次のコードを実行すると、DI コンテナーに追加された `IHostedService` の実装を使用して、非 HTTP ワークロードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-123">The following code creates a non-HTTP workload with an `IHostedService` implementation added to the DI container.</span></span>

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

<span data-ttu-id="c57ad-124">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-124">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="c57ad-125">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-125">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="c57ad-126">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-126">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="c57ad-127">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-127">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="c57ad-128">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-128">Default builder settings</span></span>

<span data-ttu-id="c57ad-129"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-129">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> method:</span></span>

* <span data-ttu-id="c57ad-130">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory*> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-130">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory*>.</span></span>
* <span data-ttu-id="c57ad-131">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-131">Loads host configuration from:</span></span>
  * <span data-ttu-id="c57ad-132">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-132">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="c57ad-133">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-133">Command-line arguments.</span></span>
* <span data-ttu-id="c57ad-134">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-134">Loads app configuration from:</span></span>
  * <span data-ttu-id="c57ad-135">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="c57ad-135">*appsettings.json*.</span></span>
  * <span data-ttu-id="c57ad-136">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="c57ad-136">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="c57ad-137">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="c57ad-137">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="c57ad-138">環境変数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-138">Environment variables.</span></span>
  * <span data-ttu-id="c57ad-139">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-139">Command-line arguments.</span></span>
* <span data-ttu-id="c57ad-140">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-140">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="c57ad-141">コンソール</span><span class="sxs-lookup"><span data-stu-id="c57ad-141">Console</span></span>
  * <span data-ttu-id="c57ad-142">デバッグ</span><span class="sxs-lookup"><span data-stu-id="c57ad-142">Debug</span></span>
  * <span data-ttu-id="c57ad-143">EventSource</span><span class="sxs-lookup"><span data-stu-id="c57ad-143">EventSource</span></span>
  * <span data-ttu-id="c57ad-144">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="c57ad-144">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="c57ad-145">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-145">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="c57ad-146">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-146">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="c57ad-147">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-147">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="c57ad-148">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-148">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="c57ad-149">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-149">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="c57ad-150">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-150">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="c57ad-151">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-151">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="c57ad-152">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-152">Enables IIS integration.</span></span> <span data-ttu-id="c57ad-153">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-153">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="c57ad-154">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-154">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c57ad-155">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="c57ad-155">Framework-provided services</span></span>

<span data-ttu-id="c57ad-156">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-156">The following services are registered automatically:</span></span>

* [<span data-ttu-id="c57ad-157">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-157">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="c57ad-158">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-158">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="c57ad-159">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="c57ad-159">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="c57ad-160">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-160">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="c57ad-161">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-161">IHostApplicationLifetime</span></span>

<span data-ttu-id="c57ad-162">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-162">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="c57ad-163">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-163">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="c57ad-164">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-164">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="c57ad-165">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-165">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="c57ad-166">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-166">IHostLifetime</span></span>

<span data-ttu-id="c57ad-167"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-167">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="c57ad-168">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-168">The last implementation registered is used.</span></span>

<span data-ttu-id="c57ad-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-169">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="c57ad-170">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="c57ad-170">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="c57ad-171"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-171">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> to start the shutdown process.</span></span>
* <span data-ttu-id="c57ad-172">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-172">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="c57ad-173">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="c57ad-173">IHostEnvironment</span></span>

<span data-ttu-id="c57ad-174">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-174">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="c57ad-175">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="c57ad-175">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="c57ad-176">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="c57ad-176">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="c57ad-177">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="c57ad-177">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="c57ad-178">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-178">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="c57ad-179">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-179">Host configuration</span></span>

<span data-ttu-id="c57ad-180">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-180">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="c57ad-181">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-181">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>.</span></span> <span data-ttu-id="c57ad-182">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-182">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="c57ad-183">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-183">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="c57ad-184">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-184">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-185">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-185">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="c57ad-186">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-186">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c57ad-187">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-187">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="c57ad-188">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-188">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="c57ad-189">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-189">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="c57ad-190">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-190">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="c57ad-191">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-191">App configuration</span></span>

<span data-ttu-id="c57ad-192">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-192">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="c57ad-193">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-193">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-194">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-194">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="c57ad-195">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-195">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="c57ad-196">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-196">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="c57ad-197">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-197">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="c57ad-198">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-198">Settings for all app types</span></span>

<span data-ttu-id="c57ad-199">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-199">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="c57ad-200">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-200">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="c57ad-201">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="c57ad-201">ApplicationName</span></span>

<span data-ttu-id="c57ad-202">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-202">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="c57ad-203">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="c57ad-203">**Key**: `applicationName`</span></span>  
<span data-ttu-id="c57ad-204">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-204">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-205">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="c57ad-205">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="c57ad-206">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="c57ad-206">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="c57ad-207">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-207">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="c57ad-208">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-208">ContentRoot</span></span>

<span data-ttu-id="c57ad-209">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-209">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="c57ad-210">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-210">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="c57ad-211">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-211">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="c57ad-212">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-212">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-213">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="c57ad-213">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="c57ad-214">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-214">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="c57ad-215">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-215">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="c57ad-216">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="c57ad-216">For more information, see:</span></span>

* [<span data-ttu-id="c57ad-217">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="c57ad-217">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="c57ad-218">WebRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-218">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="c57ad-219">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="c57ad-219">EnvironmentName</span></span>

<span data-ttu-id="c57ad-220">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-220">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="c57ad-221">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-221">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="c57ad-222">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-222">Values aren't case-sensitive.</span></span>

<span data-ttu-id="c57ad-223">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="c57ad-223">**Key**: `environment`</span></span>  
<span data-ttu-id="c57ad-224">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-224">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-225">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="c57ad-225">**Default**: `Production`</span></span>  
<span data-ttu-id="c57ad-226">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-226">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="c57ad-227">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-227">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="c57ad-228">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="c57ad-228">ShutdownTimeout</span></span>

<span data-ttu-id="c57ad-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-229">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="c57ad-230">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-230">The default value is five seconds.</span></span>  <span data-ttu-id="c57ad-231">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-231">During the timeout period, the host:</span></span>

* <span data-ttu-id="c57ad-232">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-232">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="c57ad-233">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-233">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="c57ad-234">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-234">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="c57ad-235">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-235">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="c57ad-236">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-236">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="c57ad-237">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="c57ad-237">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="c57ad-238">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="c57ad-238">**Type**: `int`</span></span>  
<span data-ttu-id="c57ad-239">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="c57ad-239">**Default**: 5 seconds</span></span>  
<span data-ttu-id="c57ad-240">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-240">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="c57ad-241">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-241">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="c57ad-242">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-242">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a><span data-ttu-id="c57ad-243">変更時にアプリ構成の再度読み込みを無効にする</span><span class="sxs-lookup"><span data-stu-id="c57ad-243">Disable app configuration reload on change</span></span>

<span data-ttu-id="c57ad-244">[既定](xref:fundamentals/configuration/index#default)では、*appsettings.json* と *appsettings.{Environment}.json* は、ファイルの変更時に再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-244">By [default](xref:fundamentals/configuration/index#default), *appsettings.json* and *appsettings.{Environment}.json* are reloaded when the file changes.</span></span> <span data-ttu-id="c57ad-245">ASP.NET Core 5.0 以降でこの再度読み込み動作を無効にするには、`hostBuilder:reloadConfigOnChange` キーを `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-245">To disable this reload behavior in ASP.NET Core 5.0 or later, set the `hostBuilder:reloadConfigOnChange` key to `false`.</span></span>

<span data-ttu-id="c57ad-246">**キー**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="c57ad-246">**Key**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="c57ad-247">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-247">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-248">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="c57ad-248">**Default**: `true`</span></span>  
<span data-ttu-id="c57ad-249">**コマンドライン引数**: `hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="c57ad-249">**Command-line argument**: `hostBuilder:reloadConfigOnChange`</span></span>  
<span data-ttu-id="c57ad-250">**環境変数**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span><span class="sxs-lookup"><span data-stu-id="c57ad-250">**Environment variable**: `<PREFIX_>hostBuilder:reloadConfigOnChange`</span></span>

> [!WARNING]
> <span data-ttu-id="c57ad-251">コロン (`:`) の区切り記号は、すべてのプラットフォームの環境変数階層キーには対応していません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-251">The colon (`:`) separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="c57ad-252">詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-252">For more information, see [Environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span>

## <a name="settings-for-web-apps"></a><span data-ttu-id="c57ad-253">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-253">Settings for web apps</span></span>

<span data-ttu-id="c57ad-254">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-254">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="c57ad-255">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-255">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="c57ad-256">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-256">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="c57ad-257">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-257">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="c57ad-258">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="c57ad-258">CaptureStartupErrors</span></span>

<span data-ttu-id="c57ad-259">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-259">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="c57ad-260">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-260">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="c57ad-261">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="c57ad-261">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="c57ad-262">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-262">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-263">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-263">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="c57ad-264">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-264">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="c57ad-265">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-265">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="c57ad-266">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="c57ad-266">DetailedErrors</span></span>

<span data-ttu-id="c57ad-267">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-267">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="c57ad-268">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="c57ad-268">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="c57ad-269">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-269">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-270">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="c57ad-270">**Default**: `false`</span></span>  
<span data-ttu-id="c57ad-271">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-271">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="c57ad-272">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-272">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="c57ad-273">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="c57ad-273">HostingStartupAssemblies</span></span>

<span data-ttu-id="c57ad-274">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="c57ad-274">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="c57ad-275">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-275">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="c57ad-276">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-276">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="c57ad-277">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="c57ad-277">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="c57ad-278">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-278">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-279">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="c57ad-279">**Default**: Empty string</span></span>  
<span data-ttu-id="c57ad-280">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c57ad-280">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="c57ad-281">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-281">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="c57ad-282">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="c57ad-282">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="c57ad-283">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="c57ad-283">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="c57ad-284">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="c57ad-284">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="c57ad-285">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-285">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-286">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="c57ad-286">**Default**: Empty string</span></span>  
<span data-ttu-id="c57ad-287">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c57ad-287">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="c57ad-288">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-288">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="c57ad-289">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="c57ad-289">HTTPS_Port</span></span>

<span data-ttu-id="c57ad-290">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="c57ad-290">The HTTPS redirect port.</span></span> <span data-ttu-id="c57ad-291">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-291">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c57ad-292">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="c57ad-292">**Key**: `https_port`</span></span>  
<span data-ttu-id="c57ad-293">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-293">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-294">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-294">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="c57ad-295">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-295">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="c57ad-296">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-296">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="c57ad-297">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="c57ad-297">PreferHostingUrls</span></span>

<span data-ttu-id="c57ad-298">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-298">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="c57ad-299">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="c57ad-299">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="c57ad-300">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-300">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-301">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="c57ad-301">**Default**: `true`</span></span>  
<span data-ttu-id="c57ad-302">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-302">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="c57ad-303">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-303">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="c57ad-304">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="c57ad-304">PreventHostingStartup</span></span>

<span data-ttu-id="c57ad-305">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-305">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="c57ad-306">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-306">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="c57ad-307">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="c57ad-307">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="c57ad-308">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-308">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-309">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="c57ad-309">**Default**: `false`</span></span>  
<span data-ttu-id="c57ad-310">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="c57ad-310">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="c57ad-311">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-311">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="c57ad-312">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="c57ad-312">StartupAssembly</span></span>

<span data-ttu-id="c57ad-313">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="c57ad-313">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="c57ad-314">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="c57ad-314">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="c57ad-315">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-315">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-316">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="c57ad-316">**Default**: The app's assembly</span></span>  
<span data-ttu-id="c57ad-317">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="c57ad-317">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="c57ad-318">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-318">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="c57ad-319">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-319">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="c57ad-320">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-320">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="c57ad-321">URL</span><span class="sxs-lookup"><span data-stu-id="c57ad-321">URLs</span></span>

<span data-ttu-id="c57ad-322">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="c57ad-322">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="c57ad-323">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-323">For example, `http://localhost:123`.</span></span> <span data-ttu-id="c57ad-324">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-324">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="c57ad-325">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-325">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="c57ad-326">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-326">Supported formats vary among servers.</span></span>

<span data-ttu-id="c57ad-327">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="c57ad-327">**Key**: `urls`</span></span>  
<span data-ttu-id="c57ad-328">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-328">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-329">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="c57ad-329">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="c57ad-330">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-330">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="c57ad-331">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-331">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="c57ad-332">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-332">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="c57ad-333">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-333">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="c57ad-334">WebRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-334">WebRoot</span></span>

<span data-ttu-id="c57ad-335">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-335">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="c57ad-336">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-336">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="c57ad-337">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-337">**Key**: `webroot`</span></span>  
<span data-ttu-id="c57ad-338">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-338">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-339">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-339">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="c57ad-340">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-340">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="c57ad-341">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-341">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="c57ad-342">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-342">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="c57ad-343">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="c57ad-343">For more information, see:</span></span>

* [<span data-ttu-id="c57ad-344">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="c57ad-344">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="c57ad-345">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-345">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="c57ad-346">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="c57ad-346">Manage the host lifetime</span></span>

<span data-ttu-id="c57ad-347">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="c57ad-347">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="c57ad-348">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-348">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="c57ad-349">実行</span><span class="sxs-lookup"><span data-stu-id="c57ad-349">Run</span></span>

<span data-ttu-id="c57ad-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-350"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="c57ad-351">RunAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-351">RunAsync</span></span>

<span data-ttu-id="c57ad-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-352"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="c57ad-353">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-353">RunConsoleAsync</span></span>

<span data-ttu-id="c57ad-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-354"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="c57ad-355">[開始]</span><span class="sxs-lookup"><span data-stu-id="c57ad-355">Start</span></span>

<span data-ttu-id="c57ad-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-356"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="c57ad-357">StartAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-357">StartAsync</span></span>

<span data-ttu-id="c57ad-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-358"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="c57ad-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-359"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="c57ad-360">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-360">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="c57ad-361">StopAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-361">StopAsync</span></span>

<span data-ttu-id="c57ad-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-362"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="c57ad-363">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="c57ad-363">WaitForShutdown</span></span>

<span data-ttu-id="c57ad-364"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-364"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="c57ad-365">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-365">WaitForShutdownAsync</span></span>

<span data-ttu-id="c57ad-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-366"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="c57ad-367">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="c57ad-367">External control</span></span>

<span data-ttu-id="c57ad-368">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-368">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="c57ad-369">ASP.NET Core テンプレートでは、.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-369">The ASP.NET Core templates create a .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>).</span></span>

<span data-ttu-id="c57ad-370">このトピックでは、ASP.NET Core での .NET 汎用ホストの使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-370">This topic provides information on using .NET Generic Host in ASP.NET Core.</span></span> <span data-ttu-id="c57ad-371">コンソール アプリで .NET 汎用ホストを使用する方法の詳細については、「[.NET 汎用ホスト](/dotnet/core/extensions/generic-host)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-371">For information on using .NET Generic Host in console apps, see [.NET Generic Host](/dotnet/core/extensions/generic-host).</span></span>

## <a name="host-definition"></a><span data-ttu-id="c57ad-372">ホストの定義</span><span class="sxs-lookup"><span data-stu-id="c57ad-372">Host definition</span></span>

<span data-ttu-id="c57ad-373">"*ホスト*" とは、以下のようなアプリのリソースをカプセル化するオブジェクトです:</span><span class="sxs-lookup"><span data-stu-id="c57ad-373">A *host* is an object that encapsulates an app's resources, such as:</span></span>

* <span data-ttu-id="c57ad-374">依存関係の挿入 (DI)</span><span class="sxs-lookup"><span data-stu-id="c57ad-374">Dependency injection (DI)</span></span>
* <span data-ttu-id="c57ad-375">ログの記録</span><span class="sxs-lookup"><span data-stu-id="c57ad-375">Logging</span></span>
* <span data-ttu-id="c57ad-376">構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-376">Configuration</span></span>
* <span data-ttu-id="c57ad-377">`IHostedService` の実装</span><span class="sxs-lookup"><span data-stu-id="c57ad-377">`IHostedService` implementations</span></span>

<span data-ttu-id="c57ad-378">ホストが起動すると、サービス コンテナーのホステッド サービスのコレクションに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の各実装で <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-378">When a host starts, it calls <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync%2A?displayProperty=nameWithType> on each implementation of <xref:Microsoft.Extensions.Hosting.IHostedService> registered in the service container's collection of hosted services.</span></span> <span data-ttu-id="c57ad-379">Web アプリでは、`IHostedService` 実装の 1 つが [ HTTP サーバー実装](xref:fundamentals/index#servers)を起動する Web サービスとなります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-379">In a web app, one of the `IHostedService` implementations is a web service that starts an [HTTP server implementation](xref:fundamentals/index#servers).</span></span>

<span data-ttu-id="c57ad-380">アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-380">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="c57ad-381">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="c57ad-381">Set up a host</span></span>

<span data-ttu-id="c57ad-382">ホストは通常、`Program` クラス内のコードによって構成、ビルド、および実行されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-382">The host is typically configured, built, and run by code in the `Program` class.</span></span> <span data-ttu-id="c57ad-383">`Main` メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-383">The `Main` method:</span></span>

* <span data-ttu-id="c57ad-384">`CreateHostBuilder` メソッドを呼び出して、builder オブジェクトを作成および構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-384">Calls a `CreateHostBuilder` method to create and configure a builder object.</span></span>
* <span data-ttu-id="c57ad-385">builder オブジェクト上で `Build` メソッドと `Run` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-385">Calls `Build` and `Run` methods on the builder object.</span></span>

<span data-ttu-id="c57ad-386">ASP.NET Core の web テンプレートでは、汎用ホストを作成するために次のコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-386">The ASP.NET Core web templates generate the following code to create a Generic Host:</span></span>

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

<span data-ttu-id="c57ad-387">次のコードでは、HTTP 以外のワークロードを使用して、汎用ホストを作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-387">The following code creates a Generic Host using non-HTTP workload.</span></span> <span data-ttu-id="c57ad-388">次のコードでは、`IHostedService` の実装が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-388">The `IHostedService` implementation is added to the DI container:</span></span>

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

<span data-ttu-id="c57ad-389">HTTP ワークロードの場合、`Main` メソッドは同じですが、`CreateHostBuilder` によって `ConfigureWebHostDefaults` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-389">For an HTTP workload, the `Main` method is the same but `CreateHostBuilder` calls `ConfigureWebHostDefaults`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="c57ad-390">上記のコードは、ASP.NET Core テンプレートによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-390">The preceding code is generated by the ASP.NET Core templates.</span></span>

<span data-ttu-id="c57ad-391">Entity Framework Core がアプリで使用されている場合は、`CreateHostBuilder` メソッドの名前またはシグネチャを変更しないでください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-391">If the app uses Entity Framework Core, don't change the name or signature of the `CreateHostBuilder` method.</span></span> <span data-ttu-id="c57ad-392">[Entity Framework Core ツール](/ef/core/miscellaneous/cli/) では、アプリを実行することなくホストを構成する `CreateHostBuilder` メソッドを検出することが想定されています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-392">The [Entity Framework Core tools](/ef/core/miscellaneous/cli/) expect to find a `CreateHostBuilder` method that configures the host without running the app.</span></span> <span data-ttu-id="c57ad-393">詳細については、「[デザイン時 DbContext 作成](/ef/core/miscellaneous/cli/dbcontext-creation)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-393">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

## <a name="default-builder-settings"></a><span data-ttu-id="c57ad-394">既定の builder 設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-394">Default builder settings</span></span>

<span data-ttu-id="c57ad-395"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-395">The <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> method:</span></span>

* <span data-ttu-id="c57ad-396">[コンテンツ ルート](xref:fundamentals/index#content-root)を、<xref:System.IO.Directory.GetCurrentDirectory%2A> によって返されるパスに設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-396">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by <xref:System.IO.Directory.GetCurrentDirectory%2A>.</span></span>
* <span data-ttu-id="c57ad-397">次からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-397">Loads host configuration from:</span></span>
  * <span data-ttu-id="c57ad-398">プレフィックス `DOTNET_` が付いた環境変数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-398">Environment variables prefixed with `DOTNET_`.</span></span>
  * <span data-ttu-id="c57ad-399">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-399">Command-line arguments.</span></span>
* <span data-ttu-id="c57ad-400">次からアプリの構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-400">Loads app configuration from:</span></span>
  * <span data-ttu-id="c57ad-401">*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="c57ad-401">*appsettings.json*.</span></span>
  * <span data-ttu-id="c57ad-402">*appsettings.{Environment}.json*。</span><span class="sxs-lookup"><span data-stu-id="c57ad-402">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="c57ad-403">`Development` 環境でアプリが実行される場合に使用される[シークレット マネージャー](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="c57ad-403">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="c57ad-404">環境変数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-404">Environment variables.</span></span>
  * <span data-ttu-id="c57ad-405">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="c57ad-405">Command-line arguments.</span></span>
* <span data-ttu-id="c57ad-406">次の[ログ](xref:fundamentals/logging/index) プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-406">Adds the following [logging](xref:fundamentals/logging/index) providers:</span></span>
  * <span data-ttu-id="c57ad-407">コンソール</span><span class="sxs-lookup"><span data-stu-id="c57ad-407">Console</span></span>
  * <span data-ttu-id="c57ad-408">デバッグ</span><span class="sxs-lookup"><span data-stu-id="c57ad-408">Debug</span></span>
  * <span data-ttu-id="c57ad-409">EventSource</span><span class="sxs-lookup"><span data-stu-id="c57ad-409">EventSource</span></span>
  * <span data-ttu-id="c57ad-410">イベント ログ (Windows で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="c57ad-410">EventLog (only when running on Windows)</span></span>
* <span data-ttu-id="c57ad-411">環境が [開発] になっている場合は、[スコープの検証](xref:fundamentals/dependency-injection#scope-validation)と[依存関係の検証](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-411">Enables [scope validation](xref:fundamentals/dependency-injection#scope-validation) and [dependency validation](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild) when the environment is Development.</span></span>

<span data-ttu-id="c57ad-412">`ConfigureWebHostDefaults` メソッド:</span><span class="sxs-lookup"><span data-stu-id="c57ad-412">The `ConfigureWebHostDefaults` method:</span></span>

* <span data-ttu-id="c57ad-413">プレフィックス `ASPNETCORE_` が付いた環境変数からホスト構成を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-413">Loads host configuration from environment variables prefixed with `ASPNETCORE_`.</span></span>
* <span data-ttu-id="c57ad-414">[Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして設定し、アプリのホスティング構成プロバイダーを使用してそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-414">Sets [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and configures it using the app's hosting configuration providers.</span></span> <span data-ttu-id="c57ad-415">Kestrel サーバーの既定のオプションについては、<xref:fundamentals/servers/kestrel#kestrel-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-415">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="c57ad-416">[Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-416">Adds [Host Filtering middleware](xref:fundamentals/servers/kestrel#host-filtering).</span></span>
* <span data-ttu-id="c57ad-417">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` が `true` の場合、[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) を追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-417">Adds [Forwarded Headers middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) if `ASPNETCORE_FORWARDEDHEADERS_ENABLED` equals `true`.</span></span>
* <span data-ttu-id="c57ad-418">IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-418">Enables IIS integration.</span></span> <span data-ttu-id="c57ad-419">IIS の既定のオプションについては、<xref:host-and-deploy/iis/index#iis-options> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-419">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

<span data-ttu-id="c57ad-420">この記事で後述する「[すべての種類のアプリの設定](#settings-for-all-app-types)」および「[Web アプリの設定](#settings-for-web-apps)」セクションに、既定のビルダー設定をオーバーライドする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-420">The [Settings for all app types](#settings-for-all-app-types) and [Settings for web apps](#settings-for-web-apps) sections later in this article show how to override default builder settings.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c57ad-421">フレームワークが提供するサービス</span><span class="sxs-lookup"><span data-stu-id="c57ad-421">Framework-provided services</span></span>

<span data-ttu-id="c57ad-422">以下のサービスは、自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-422">The following services are registered automatically:</span></span>

* [<span data-ttu-id="c57ad-423">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-423">IHostApplicationLifetime</span></span>](#ihostapplicationlifetime)
* [<span data-ttu-id="c57ad-424">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-424">IHostLifetime</span></span>](#ihostlifetime)
* [<span data-ttu-id="c57ad-425">IHostEnvironment / IWebHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="c57ad-425">IHostEnvironment / IWebHostEnvironment</span></span>](#ihostenvironment)

<span data-ttu-id="c57ad-426">フレームワークによって提供されるサービスの詳細については、<xref:fundamentals/dependency-injection#framework-provided-services> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-426">For more information on framework-provided services, see <xref:fundamentals/dependency-injection#framework-provided-services>.</span></span>

## <a name="ihostapplicationlifetime"></a><span data-ttu-id="c57ad-427">IHostApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-427">IHostApplicationLifetime</span></span>

<span data-ttu-id="c57ad-428">起動後タスクとグレースフル シャットダウン タスクを処理するために <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (旧称 `IApplicationLifetime`) サービスを任意のクラスに注入します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-428">Inject the <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (formerly `IApplicationLifetime`) service into any class to handle post-startup and graceful shutdown tasks.</span></span> <span data-ttu-id="c57ad-429">インターフェイス上の 3 つのプロパティは、アプリの起動およびアプリの停止のイベント ハンドラー メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-429">Three properties on the interface are cancellation tokens used to register app start and app stop event handler methods.</span></span> <span data-ttu-id="c57ad-430">インターフェイスには `StopApplication` メソッドも含まれています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-430">The interface also includes a `StopApplication` method.</span></span>

<span data-ttu-id="c57ad-431">次の例は、`IHostApplicationLifetime` イベントを登録する `IHostedService` の実装です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-431">The following example is an `IHostedService` implementation that registers `IHostApplicationLifetime` events:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a><span data-ttu-id="c57ad-432">IHostLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-432">IHostLifetime</span></span>

<span data-ttu-id="c57ad-433"><xref:Microsoft.Extensions.Hosting.IHostLifetime> 実装では、ホストを開始および停止するタイミングが制御されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-433">The <xref:Microsoft.Extensions.Hosting.IHostLifetime> implementation controls when the host starts and when it stops.</span></span> <span data-ttu-id="c57ad-434">登録されている最後の実装が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-434">The last implementation registered is used.</span></span>

<span data-ttu-id="c57ad-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の `IHostLifetime` 実装です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-435">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is the default `IHostLifetime` implementation.</span></span> <span data-ttu-id="c57ad-436">`ConsoleLifetime`:</span><span class="sxs-lookup"><span data-stu-id="c57ad-436">`ConsoleLifetime`:</span></span>

* <span data-ttu-id="c57ad-437"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-437">Listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication%2A> to start the shutdown process.</span></span>
* <span data-ttu-id="c57ad-438">[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-438">Unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span>

## <a name="ihostenvironment"></a><span data-ttu-id="c57ad-439">IHostEnvironment</span><span class="sxs-lookup"><span data-stu-id="c57ad-439">IHostEnvironment</span></span>

<span data-ttu-id="c57ad-440">次の設定に関する情報を取得するため、クラスに <xref:Microsoft.Extensions.Hosting.IHostEnvironment> サービスを注入します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-440">Inject the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> service into a class to get information about the following settings:</span></span>

* [<span data-ttu-id="c57ad-441">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="c57ad-441">ApplicationName</span></span>](#applicationname)
* [<span data-ttu-id="c57ad-442">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="c57ad-442">EnvironmentName</span></span>](#environmentname)
* [<span data-ttu-id="c57ad-443">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="c57ad-443">ContentRootPath</span></span>](#contentroot)

<span data-ttu-id="c57ad-444">Web アプリで `IWebHostEnvironment` インターフェイスを実装します。これにより、`IHostEnvironment` が継承され、[WebRootPath](#webroot) が追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-444">Web apps implement the `IWebHostEnvironment` interface, which inherits `IHostEnvironment` and adds the [WebRootPath](#webroot).</span></span>

## <a name="host-configuration"></a><span data-ttu-id="c57ad-445">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-445">Host configuration</span></span>

<span data-ttu-id="c57ad-446">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostEnvironment> 実装のプロパティで使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-446">Host configuration is used for the properties of the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> implementation.</span></span>

<span data-ttu-id="c57ad-447">ホストの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> 内の [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) から使用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-447">Host configuration is available from [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) inside <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="c57ad-448">`ConfigureAppConfiguration` の後、`HostBuilderContext.Configuration` はアプリの構成に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-448">After `ConfigureAppConfiguration`, `HostBuilderContext.Configuration` is replaced with the app config.</span></span>

<span data-ttu-id="c57ad-449">ホストの構成を追加するには、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-449">To add host configuration, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration%2A> on `IHostBuilder`.</span></span> <span data-ttu-id="c57ad-450">`ConfigureHostConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-450">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-451">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-451">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="c57ad-452">プレフィックス `DOTNET_` を持つ環境変数プロバイダーとコマンドライン引数が、`CreateDefaultBuilder` によって組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-452">The environment variable provider with prefix `DOTNET_` and command-line arguments are included by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="c57ad-453">Web アプリの場合は、プレフィックス `ASPNETCORE_` を持つ環境変数プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-453">For web apps, the environment variable provider with prefix `ASPNETCORE_` is added.</span></span> <span data-ttu-id="c57ad-454">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-454">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="c57ad-455">たとえば、`ASPNETCORE_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-455">For example, the environment variable value for `ASPNETCORE_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="c57ad-456">次の例では、ホストの構成を作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-456">The following example creates host configuration:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a><span data-ttu-id="c57ad-457">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-457">App configuration</span></span>

<span data-ttu-id="c57ad-458">アプリの構成は、`IHostBuilder` 上で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-458">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on `IHostBuilder`.</span></span> <span data-ttu-id="c57ad-459">`ConfigureAppConfiguration` を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-459">`ConfigureAppConfiguration` can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-460">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-460">The app uses whichever option sets a value last on a given key.</span></span> 

<span data-ttu-id="c57ad-461">`ConfigureAppConfiguration` によって作成された構成は、[ HostBuilderContext.Configuration ](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) で、以降の操作のために、かつ DI からのサービスとして利用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-461">The configuration created by `ConfigureAppConfiguration` is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and as a service from DI.</span></span> <span data-ttu-id="c57ad-462">ホストの構成はアプリの構成にも追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-462">The host configuration is also added to the app configuration.</span></span>

<span data-ttu-id="c57ad-463">詳細については、「[ASP.NET Core の構成](xref:fundamentals/configuration/index#configureappconfiguration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-463">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index#configureappconfiguration).</span></span>

## <a name="settings-for-all-app-types"></a><span data-ttu-id="c57ad-464">すべての種類のアプリの設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-464">Settings for all app types</span></span>

<span data-ttu-id="c57ad-465">このセクションでは、HTTP のワークロードと HTTP 以外のワークロードの両方に適用されるホストの設定を一覧します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-465">This section lists host settings that apply to both HTTP and non-HTTP workloads.</span></span> <span data-ttu-id="c57ad-466">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-466">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a><span data-ttu-id="c57ad-467">ApplicationName</span><span class="sxs-lookup"><span data-stu-id="c57ad-467">ApplicationName</span></span>

<span data-ttu-id="c57ad-468">[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-468">The [IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span>

<span data-ttu-id="c57ad-469">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="c57ad-469">**Key**: `applicationName`</span></span>  
<span data-ttu-id="c57ad-470">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-470">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-471">**既定**: アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="c57ad-471">**Default**: The name of the assembly that contains the app's entry point.</span></span>  
<span data-ttu-id="c57ad-472">**環境変数**: `<PREFIX_>APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="c57ad-472">**Environment variable**: `<PREFIX_>APPLICATIONNAME`</span></span>

<span data-ttu-id="c57ad-473">この値を設定するには、環境変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-473">To set this value, use the environment variable.</span></span> 

### <a name="contentroot"></a><span data-ttu-id="c57ad-474">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-474">ContentRoot</span></span>

<span data-ttu-id="c57ad-475">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) プロパティでは、ホストがコンテンツ ファイルの検索を開始する位置が決定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-475">The [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) property determines where the host begins searching for content files.</span></span> <span data-ttu-id="c57ad-476">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-476">If the path doesn't exist, the host fails to start.</span></span>

<span data-ttu-id="c57ad-477">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-477">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="c57ad-478">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-478">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-479">**既定**: アプリ アセンブリが存在するフォルダー。</span><span class="sxs-lookup"><span data-stu-id="c57ad-479">**Default**: The folder where the app assembly resides.</span></span>  
<span data-ttu-id="c57ad-480">**環境変数**: `<PREFIX_>CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-480">**Environment variable**: `<PREFIX_>CONTENTROOT`</span></span>

<span data-ttu-id="c57ad-481">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseContentRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-481">To set this value, use the environment variable or call `UseContentRoot` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

<span data-ttu-id="c57ad-482">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="c57ad-482">For more information, see:</span></span>

* [<span data-ttu-id="c57ad-483">基礎: コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="c57ad-483">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="c57ad-484">WebRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-484">WebRoot</span></span>](#webroot)

### <a name="environmentname"></a><span data-ttu-id="c57ad-485">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="c57ad-485">EnvironmentName</span></span>

<span data-ttu-id="c57ad-486">[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) プロパティは、任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-486">The [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) property can be set to any value.</span></span> <span data-ttu-id="c57ad-487">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-487">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="c57ad-488">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-488">Values aren't case-sensitive.</span></span>

<span data-ttu-id="c57ad-489">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="c57ad-489">**Key**: `environment`</span></span>  
<span data-ttu-id="c57ad-490">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-490">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-491">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="c57ad-491">**Default**: `Production`</span></span>  
<span data-ttu-id="c57ad-492">**環境変数**: `<PREFIX_>ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-492">**Environment variable**: `<PREFIX_>ENVIRONMENT`</span></span>

<span data-ttu-id="c57ad-493">この値を設定するには、環境変数を使用するか、または `IHostBuilder` 上で `UseEnvironment` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-493">To set this value, use the environment variable or call `UseEnvironment` on `IHostBuilder`:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a><span data-ttu-id="c57ad-494">ShutdownTimeout</span><span class="sxs-lookup"><span data-stu-id="c57ad-494">ShutdownTimeout</span></span>

<span data-ttu-id="c57ad-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) では、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-495">[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="c57ad-496">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-496">The default value is five seconds.</span></span>  <span data-ttu-id="c57ad-497">タイムアウト期間中、ホストでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-497">During the timeout period, the host:</span></span>

* <span data-ttu-id="c57ad-498">[IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping) をトリガーします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-498">Triggers [IHostApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="c57ad-499">ホステッド サービスの停止を試み、停止に失敗したサービスのエラーをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-499">Attempts to stop hosted services, logging errors for services that fail to stop.</span></span>

<span data-ttu-id="c57ad-500">すべてのホステッド サービスが停止する前にタイムアウト時間が切れた場合、残っているアクティブなサービスはアプリのシャットダウン時に停止します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-500">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="c57ad-501">処理が完了していない場合でも、サービスは停止します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-501">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="c57ad-502">サービスが停止するまでにさらに時間が必要な場合は、タイムアウト値を増やします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-502">If services require additional time to stop, increase the timeout.</span></span>

<span data-ttu-id="c57ad-503">**キー**: `shutdownTimeoutSeconds`</span><span class="sxs-lookup"><span data-stu-id="c57ad-503">**Key**: `shutdownTimeoutSeconds`</span></span>  
<span data-ttu-id="c57ad-504">**型**: `int`</span><span class="sxs-lookup"><span data-stu-id="c57ad-504">**Type**: `int`</span></span>  
<span data-ttu-id="c57ad-505">**既定**:5 秒</span><span class="sxs-lookup"><span data-stu-id="c57ad-505">**Default**: 5 seconds</span></span>  
<span data-ttu-id="c57ad-506">**環境変数**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-506">**Environment variable**: `<PREFIX_>SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="c57ad-507">この値を設定するには、環境変数を使用するか、または `HostOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-507">To set this value, use the environment variable or configure `HostOptions`.</span></span> <span data-ttu-id="c57ad-508">次の例では、タイムアウトを 20 秒に設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-508">The following example sets the timeout to 20 seconds:</span></span>

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a><span data-ttu-id="c57ad-509">Web アプリの設定</span><span class="sxs-lookup"><span data-stu-id="c57ad-509">Settings for web apps</span></span>

<span data-ttu-id="c57ad-510">一部のホスト設定は、HTTP のワークロードにのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-510">Some host settings apply only to HTTP workloads.</span></span> <span data-ttu-id="c57ad-511">既定では、これらの設定を構成するのに使用する環境変数には、プレフィックスとして `DOTNET_` または `ASPNETCORE_` を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-511">By default, environment variables used to configure these settings can have a `DOTNET_` or `ASPNETCORE_` prefix.</span></span>

<span data-ttu-id="c57ad-512">`IWebHostBuilder` 上の拡張メソッドはこれらの設定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-512">Extension methods on `IWebHostBuilder` are available for these settings.</span></span> <span data-ttu-id="c57ad-513">次の例に示すように、拡張メソッドを呼び出す方法を示すコード サンプルでは `webBuilder` が `IWebHostBuilder` のインスタンスであると想定しています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-513">Code samples that show how to call the extension methods assume `webBuilder` is an instance of `IWebHostBuilder`, as in the following example:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a><span data-ttu-id="c57ad-514">CaptureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="c57ad-514">CaptureStartupErrors</span></span>

<span data-ttu-id="c57ad-515">`false` の場合、起動時にエラーが発生するとホストが終了します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-515">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="c57ad-516">`true` の場合、ホストは起動時に例外をキャプチャして、サーバーを起動しようとします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-516">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

<span data-ttu-id="c57ad-517">**キー**: `captureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="c57ad-517">**Key**: `captureStartupErrors`</span></span>  
<span data-ttu-id="c57ad-518">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-518">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-519">**既定**:アプリが IIS の背後で Kestrel を使用して実行されている場合 (既定値は `true`) を除き、既定では `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-519">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="c57ad-520">**環境変数**: `<PREFIX_>CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-520">**Environment variable**: `<PREFIX_>CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="c57ad-521">この値を設定するには、構成を使用するか、または `CaptureStartupErrors` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-521">To set this value, use configuration or call `CaptureStartupErrors`:</span></span>

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a><span data-ttu-id="c57ad-522">DetailedErrors</span><span class="sxs-lookup"><span data-stu-id="c57ad-522">DetailedErrors</span></span>

<span data-ttu-id="c57ad-523">有効にされている場合、または環境が `Development` である場合、アプリによって詳細なエラーがキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-523">When enabled, or when the environment is `Development`, the app captures detailed errors.</span></span>

<span data-ttu-id="c57ad-524">**キー**: `detailedErrors`</span><span class="sxs-lookup"><span data-stu-id="c57ad-524">**Key**: `detailedErrors`</span></span>  
<span data-ttu-id="c57ad-525">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-525">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-526">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="c57ad-526">**Default**: `false`</span></span>  
<span data-ttu-id="c57ad-527">**環境変数**: `<PREFIX_>_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-527">**Environment variable**: `<PREFIX_>_DETAILEDERRORS`</span></span>

<span data-ttu-id="c57ad-528">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-528">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a><span data-ttu-id="c57ad-529">HostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="c57ad-529">HostingStartupAssemblies</span></span>

<span data-ttu-id="c57ad-530">起動時に読み込むホスティング スタートアップ アセンブリのセミコロンで区切られた文字列。</span><span class="sxs-lookup"><span data-stu-id="c57ad-530">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="c57ad-531">構成値は既定で空の文字列に設定されますが、ホスティング スタートアップ アセンブリには常にアプリのアセンブリが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-531">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="c57ad-532">ホスティング スタートアップ アセンブリが提供されている場合、アプリが起動中に共通サービスをビルドしたときに読み込むためにアプリのアセンブリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-532">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

<span data-ttu-id="c57ad-533">**キー**: `hostingStartupAssemblies`</span><span class="sxs-lookup"><span data-stu-id="c57ad-533">**Key**: `hostingStartupAssemblies`</span></span>  
<span data-ttu-id="c57ad-534">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-534">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-535">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="c57ad-535">**Default**: Empty string</span></span>  
<span data-ttu-id="c57ad-536">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c57ad-536">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="c57ad-537">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-537">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a><span data-ttu-id="c57ad-538">HostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="c57ad-538">HostingStartupExcludeAssemblies</span></span>

<span data-ttu-id="c57ad-539">起動時に除外するホスティング スタートアップ アセンブリのセミコロン区切り文字列。</span><span class="sxs-lookup"><span data-stu-id="c57ad-539">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="c57ad-540">**キー**: `hostingStartupExcludeAssemblies`</span><span class="sxs-lookup"><span data-stu-id="c57ad-540">**Key**: `hostingStartupExcludeAssemblies`</span></span>  
<span data-ttu-id="c57ad-541">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-541">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-542">**既定**:空の文字列</span><span class="sxs-lookup"><span data-stu-id="c57ad-542">**Default**: Empty string</span></span>  
<span data-ttu-id="c57ad-543">**環境変数**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c57ad-543">**Environment variable**: `<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

<span data-ttu-id="c57ad-544">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-544">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a><span data-ttu-id="c57ad-545">HTTPS_Port</span><span class="sxs-lookup"><span data-stu-id="c57ad-545">HTTPS_Port</span></span>

<span data-ttu-id="c57ad-546">HTTPS リダイレクト ポート。</span><span class="sxs-lookup"><span data-stu-id="c57ad-546">The HTTPS redirect port.</span></span> <span data-ttu-id="c57ad-547">[HTTPS の適用](xref:security/enforcing-ssl)に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-547">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="c57ad-548">**キー**: `https_port`</span><span class="sxs-lookup"><span data-stu-id="c57ad-548">**Key**: `https_port`</span></span>  
<span data-ttu-id="c57ad-549">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-549">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-550">**既定**:既定値は設定されていません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-550">**Default**: A default value isn't set.</span></span>  
<span data-ttu-id="c57ad-551">**環境変数**: `<PREFIX_>HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-551">**Environment variable**: `<PREFIX_>HTTPS_PORT`</span></span>

<span data-ttu-id="c57ad-552">この値を設定するには、構成を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-552">To set this value, use configuration or call `UseSetting`:</span></span>

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a><span data-ttu-id="c57ad-553">PreferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="c57ad-553">PreferHostingUrls</span></span>

<span data-ttu-id="c57ad-554">`IServer` の実装で構成されている URL ではなく、`IWebHostBuilder` で構成されている URL でホストがリッスンするかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-554">Indicates whether the host should listen on the URLs configured with the `IWebHostBuilder` instead of those URLs configured with the `IServer` implementation.</span></span>

<span data-ttu-id="c57ad-555">**キー**: `preferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="c57ad-555">**Key**: `preferHostingUrls`</span></span>  
<span data-ttu-id="c57ad-556">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-556">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-557">**既定値**: `true`</span><span class="sxs-lookup"><span data-stu-id="c57ad-557">**Default**: `true`</span></span>  
<span data-ttu-id="c57ad-558">**環境変数**: `<PREFIX_>_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-558">**Environment variable**: `<PREFIX_>_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="c57ad-559">この値を設定するには、環境変数を使用するか、または `PreferHostingUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-559">To set this value, use the environment variable or call `PreferHostingUrls`:</span></span>

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a><span data-ttu-id="c57ad-560">PreventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="c57ad-560">PreventHostingStartup</span></span>

<span data-ttu-id="c57ad-561">アプリのアセンブリで構成されているホスティング スタートアップ アセンブリを含む、ホスティング スタートアップ アセンブリの自動読み込みを回避します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-561">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="c57ad-562">詳細については、「<xref:fundamentals/configuration/platform-specific-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-562">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="c57ad-563">**キー**: `preventHostingStartup`</span><span class="sxs-lookup"><span data-stu-id="c57ad-563">**Key**: `preventHostingStartup`</span></span>  
<span data-ttu-id="c57ad-564">**型**: `bool` (`true` または `1`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-564">**Type**: `bool` (`true` or `1`)</span></span>  
<span data-ttu-id="c57ad-565">**既定値**: `false`</span><span class="sxs-lookup"><span data-stu-id="c57ad-565">**Default**: `false`</span></span>  
<span data-ttu-id="c57ad-566">**環境変数**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="c57ad-566">**Environment variable**: `<PREFIX_>_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="c57ad-567">この値を設定するには、環境変数を使用するか、または `UseSetting` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-567">To set this value, use the environment variable or call `UseSetting` :</span></span>

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a><span data-ttu-id="c57ad-568">StartupAssembly</span><span class="sxs-lookup"><span data-stu-id="c57ad-568">StartupAssembly</span></span>

<span data-ttu-id="c57ad-569">`Startup` クラスを検索するアセンブリ。</span><span class="sxs-lookup"><span data-stu-id="c57ad-569">The assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="c57ad-570">**キー**: `startupAssembly`</span><span class="sxs-lookup"><span data-stu-id="c57ad-570">**Key**: `startupAssembly`</span></span>  
<span data-ttu-id="c57ad-571">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-571">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-572">**既定**:アプリのアセンブリ</span><span class="sxs-lookup"><span data-stu-id="c57ad-572">**Default**: The app's assembly</span></span>  
<span data-ttu-id="c57ad-573">**環境変数**: `<PREFIX_>STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="c57ad-573">**Environment variable**: `<PREFIX_>STARTUPASSEMBLY`</span></span>

<span data-ttu-id="c57ad-574">この値を設定するには、環境変数を使用するか、または `UseStartup` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-574">To set this value, use the environment variable or call `UseStartup`.</span></span> <span data-ttu-id="c57ad-575">`UseStartup` は、アセンブリ名 (`string`) または型 (`TStartup`) を取ることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-575">`UseStartup` can take an assembly name (`string`) or a type (`TStartup`).</span></span> <span data-ttu-id="c57ad-576">複数の `UseStartup` メソッドが呼び出された場合は、最後のメソッドが優先されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-576">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a><span data-ttu-id="c57ad-577">URL</span><span class="sxs-lookup"><span data-stu-id="c57ad-577">URLs</span></span>

<span data-ttu-id="c57ad-578">サーバーが要求をリッスンする必要があるポートとプロトコルを含む IP アドレスまたはホスト アドレスを示すセミコロンで区切られたリスト。</span><span class="sxs-lookup"><span data-stu-id="c57ad-578">A semicolon-delimited list of IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span> <span data-ttu-id="c57ad-579">たとえば、`http://localhost:123` のようにします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-579">For example, `http://localhost:123`.</span></span> <span data-ttu-id="c57ad-580">"\*" を使用し、サーバーが指定されたポートとプロトコル (`http://*:5000` など) を使用して IP アドレスまたはホスト名に関する要求をリッスンする必要があることを示します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-580">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="c57ad-581">プロトコル (`http://` または `https://`) は各 URL に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-581">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="c57ad-582">サポートされている形式はサーバー間で異なります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-582">Supported formats vary among servers.</span></span>

<span data-ttu-id="c57ad-583">**キー**: `urls`</span><span class="sxs-lookup"><span data-stu-id="c57ad-583">**Key**: `urls`</span></span>  
<span data-ttu-id="c57ad-584">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-584">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-585">**既定値**: `http://localhost:5000` および `https://localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="c57ad-585">**Default**: `http://localhost:5000` and `https://localhost:5001`</span></span>  
<span data-ttu-id="c57ad-586">**環境変数**: `<PREFIX_>URLS`</span><span class="sxs-lookup"><span data-stu-id="c57ad-586">**Environment variable**: `<PREFIX_>URLS`</span></span>

<span data-ttu-id="c57ad-587">この値を設定するには、環境変数を使用するか、または `UseUrls` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-587">To set this value, use the environment variable or call `UseUrls`:</span></span>

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

<span data-ttu-id="c57ad-588">Kestrel には独自のエンドポイント構成 API があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-588">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="c57ad-589">詳細については、「<xref:fundamentals/servers/kestrel#endpoint-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-589">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="webroot"></a><span data-ttu-id="c57ad-590">WebRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-590">WebRoot</span></span>

<span data-ttu-id="c57ad-591">[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) プロパティでは、アプリの静的アセットへの相対パスが決定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-591">The [IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) property determines the relative path to the app's static assets.</span></span> <span data-ttu-id="c57ad-592">パスが存在しない場合は、no-op ファイル プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-592">If the path doesn't exist, a no-op file provider is used.</span></span>  

<span data-ttu-id="c57ad-593">**キー**: `webroot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-593">**Key**: `webroot`</span></span>  
<span data-ttu-id="c57ad-594">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-594">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-595">**既定**:既定値は、`wwwroot` です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-595">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="c57ad-596">*{content root}/wwwroot* へのパスが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-596">The path to *{content root}/wwwroot* must exist.</span></span>  
<span data-ttu-id="c57ad-597">**環境変数**: `<PREFIX_>WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="c57ad-597">**Environment variable**: `<PREFIX_>WEBROOT`</span></span>

<span data-ttu-id="c57ad-598">この値を設定するには、環境変数を使用するか、または `IWebHostBuilder` 上で `UseWebRoot` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-598">To set this value, use the environment variable or call `UseWebRoot` on `IWebHostBuilder`:</span></span>

```csharp
webBuilder.UseWebRoot("public");
```

<span data-ttu-id="c57ad-599">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="c57ad-599">For more information, see:</span></span>

* [<span data-ttu-id="c57ad-600">基礎: Web ルート</span><span class="sxs-lookup"><span data-stu-id="c57ad-600">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="c57ad-601">ContentRoot</span><span class="sxs-lookup"><span data-stu-id="c57ad-601">ContentRoot</span></span>](#contentroot)

## <a name="manage-the-host-lifetime"></a><span data-ttu-id="c57ad-602">ホストの有効期間を管理する</span><span class="sxs-lookup"><span data-stu-id="c57ad-602">Manage the host lifetime</span></span>

<span data-ttu-id="c57ad-603">組み込みの <xref:Microsoft.Extensions.Hosting.IHost> 実装でメソッドを呼び出して、アプリの開始および停止を行います。</span><span class="sxs-lookup"><span data-stu-id="c57ad-603">Call methods on the built <xref:Microsoft.Extensions.Hosting.IHost> implementation to start and stop the app.</span></span> <span data-ttu-id="c57ad-604">これらのメソッドは、サービス コンテナーに登録されているすべての <xref:Microsoft.Extensions.Hosting.IHostedService> 実装に影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-604">These methods affect all  <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="c57ad-605">実行</span><span class="sxs-lookup"><span data-stu-id="c57ad-605">Run</span></span>

<span data-ttu-id="c57ad-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-606"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down.</span></span>

### <a name="runasync"></a><span data-ttu-id="c57ad-607">RunAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-607">RunAsync</span></span>

<span data-ttu-id="c57ad-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-608"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span>

### <a name="runconsoleasync"></a><span data-ttu-id="c57ad-609">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-609">RunConsoleAsync</span></span>

<span data-ttu-id="c57ad-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-610"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

### <a name="start"></a><span data-ttu-id="c57ad-611">[開始]</span><span class="sxs-lookup"><span data-stu-id="c57ad-611">Start</span></span>

<span data-ttu-id="c57ad-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-612"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

### <a name="startasync"></a><span data-ttu-id="c57ad-613">StartAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-613">StartAsync</span></span>

<span data-ttu-id="c57ad-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> ではホストが開始され、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> が返されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-614"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the host and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered.</span></span> 

<span data-ttu-id="c57ad-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は `StartAsync` の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-615"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of `StartAsync`, which waits until it's complete before continuing.</span></span> <span data-ttu-id="c57ad-616">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-616">This can be used to delay startup until signaled by an external event.</span></span>

### <a name="stopasync"></a><span data-ttu-id="c57ad-617">StopAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-617">StopAsync</span></span>

<span data-ttu-id="c57ad-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-618"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

### <a name="waitforshutdown"></a><span data-ttu-id="c57ad-619">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="c57ad-619">WaitForShutdown</span></span>

<span data-ttu-id="c57ad-620"><kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT や SIGTERM を介するなどして IHostLifetime によってシャットダウンがトリガーされるまで、<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> では呼び出し側スレッドがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-620"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> blocks the calling thread until shutdown is triggered by the IHostLifetime, such as via <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM.</span></span>

### <a name="waitforshutdownasync"></a><span data-ttu-id="c57ad-621">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-621">WaitForShutdownAsync</span></span>

<span data-ttu-id="c57ad-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-622"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

### <a name="external-control"></a><span data-ttu-id="c57ad-623">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="c57ad-623">External control</span></span>

<span data-ttu-id="c57ad-624">ホストの有効期間の直接コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-624">Direct control of the host lifetime can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="c57ad-625">ASP.NET Core アプリはホストを構成して起動します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-625">ASP.NET Core apps configure and launch a host.</span></span> <span data-ttu-id="c57ad-626">ホストはアプリの起動と有効期間の管理を担当します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-626">The host is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="c57ad-627">この記事では、HTTP 要求を処理しないアプリに使用される、ASP.NET Core の汎用ホスト (<xref:Microsoft.Extensions.Hosting.HostBuilder>) について説明します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-627">This article covers the ASP.NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>), which is used for apps that don't process HTTP requests.</span></span>

<span data-ttu-id="c57ad-628">汎用ホストの目的は、Web ホスト API から HTTP パイプラインを切り離して、多様なホスト シナリオを有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-628">The purpose of Generic Host is to decouple the HTTP pipeline from the Web Host API to enable a wider array of host scenarios.</span></span> <span data-ttu-id="c57ad-629">メッセージング、バックグラウンド タスク、汎用ホストに基づくその他の HTTP ワークロードに対して、構成、依存関係の挿入 (DI)、ログなどの横断的機能によるメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-629">Messaging, background tasks, and other non-HTTP workloads based on Generic Host benefit from cross-cutting capabilities, such as configuration, dependency injection (DI), and logging.</span></span>

<span data-ttu-id="c57ad-630">汎用ホストは ASP.NET Core 2.1 の新機能であり、Web ホスティングのシナリオには適していません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-630">Generic Host is new in ASP.NET Core 2.1 and isn't suitable for web hosting scenarios.</span></span> <span data-ttu-id="c57ad-631">Web ホスティングのシナリオの場合は、[Web ホスト](xref:fundamentals/host/web-host)を使ってください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-631">For web hosting scenarios, use the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="c57ad-632">汎用ホストは将来のリリースで Web ホストに代わるものであり、HTTP と非 HTTP 両方のシナリオのプライマリ ホスト API として機能するようになります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-632">Generic Host will replace Web Host in a future release and act as the primary host API in both HTTP and non-HTTP scenarios.</span></span>

<span data-ttu-id="c57ad-633">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c57ad-633">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c57ad-634">[Visual Studio Code](https://code.visualstudio.com/) でサンプル アプリを実行するときは、"*外部ターミナルまたは統合ターミナル*" を使います。</span><span class="sxs-lookup"><span data-stu-id="c57ad-634">When running the sample app in [Visual Studio Code](https://code.visualstudio.com/), use an *external or integrated terminal*.</span></span> <span data-ttu-id="c57ad-635">`internalConsole` ではサンプルを実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-635">Don't run the sample in an `internalConsole`.</span></span>

<span data-ttu-id="c57ad-636">Visual Studio Code でコンソールを設定するには:</span><span class="sxs-lookup"><span data-stu-id="c57ad-636">To set the console in Visual Studio Code:</span></span>

1. <span data-ttu-id="c57ad-637">*.vscode/launch.json* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-637">Open the *.vscode/launch.json* file.</span></span>
1. <span data-ttu-id="c57ad-638">**.NET Core Launch (console)** の構成で、**console** エントリを探します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-638">In the **.NET Core Launch (console)** configuration, locate the **console** entry.</span></span> <span data-ttu-id="c57ad-639">値を `externalTerminal` または `integratedTerminal` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-639">Set the value to either `externalTerminal` or `integratedTerminal`.</span></span>

## <a name="introduction"></a><span data-ttu-id="c57ad-640">はじめに</span><span class="sxs-lookup"><span data-stu-id="c57ad-640">Introduction</span></span>

<span data-ttu-id="c57ad-641">汎用ホスト ライブラリは、<xref:Microsoft.Extensions.Hosting> 名前空間で使用でき、[Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) パッケージで提供されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-641">The Generic Host library is available in the <xref:Microsoft.Extensions.Hosting> namespace and provided by the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) package.</span></span> <span data-ttu-id="c57ad-642">`Microsoft.Extensions.Hosting` パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれます (ASP.NET Core 2.1 以降)。</span><span class="sxs-lookup"><span data-stu-id="c57ad-642">The `Microsoft.Extensions.Hosting` package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="c57ad-643"><xref:Microsoft.Extensions.Hosting.IHostedService> がコード実行のエントリ ポイントです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-643"><xref:Microsoft.Extensions.Hosting.IHostedService> is the entry point to code execution.</span></span> <span data-ttu-id="c57ad-644"><xref:Microsoft.Extensions.Hosting.IHostedService> の各実装は、[ConfigureServices でのサービス登録](#configureservices)の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-644">Each <xref:Microsoft.Extensions.Hosting.IHostedService> implementation is executed in the order of [service registration in ConfigureServices](#configureservices).</span></span> <span data-ttu-id="c57ad-645">ホストが開始するときは <xref:Microsoft.Extensions.Hosting.IHostedService> ごとに <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> が呼び出され、ホストが正常に終了するときは登録と逆の順序で <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-645"><xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> is called on each <xref:Microsoft.Extensions.Hosting.IHostedService> when the host starts, and <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> is called in reverse registration order when the host shuts down gracefully.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="c57ad-646">ホストを設定する</span><span class="sxs-lookup"><span data-stu-id="c57ad-646">Set up a host</span></span>

<span data-ttu-id="c57ad-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> は、ライブラリとアプリがホストの初期化、ビルド、実行に使用するメイン コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-647"><xref:Microsoft.Extensions.Hosting.IHostBuilder> is the main component that libraries and apps use to initialize, build, and run the host:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a><span data-ttu-id="c57ad-648">オプション</span><span class="sxs-lookup"><span data-stu-id="c57ad-648">Options</span></span>

<span data-ttu-id="c57ad-649"><xref:Microsoft.Extensions.Hosting.IHost> の <xref:Microsoft.Extensions.Hosting.HostOptions> 構成オプションです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-649"><xref:Microsoft.Extensions.Hosting.HostOptions> configure options for the <xref:Microsoft.Extensions.Hosting.IHost>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="c57ad-650">シャットダウン タイムアウト</span><span class="sxs-lookup"><span data-stu-id="c57ad-650">Shutdown timeout</span></span>

<span data-ttu-id="c57ad-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> によって <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> のタイムアウトが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-651"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> sets the timeout for <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span> <span data-ttu-id="c57ad-652">既定値は 5 秒です。</span><span class="sxs-lookup"><span data-stu-id="c57ad-652">The default value is five seconds.</span></span>

<span data-ttu-id="c57ad-653">次に示す `Program.Main` のオプション構成では、既定の 5 秒のシャットダウン タイムアウトが 20 秒に延長されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-653">The following option configuration in `Program.Main` increases the default five-second shutdown timeout to 20 seconds:</span></span>

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

## <a name="default-services"></a><span data-ttu-id="c57ad-654">既定のサービス</span><span class="sxs-lookup"><span data-stu-id="c57ad-654">Default services</span></span>

<span data-ttu-id="c57ad-655">次のサービスは、ホストの初期化中に登録されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-655">The following services are registered during host initialization:</span></span>

* <span data-ttu-id="c57ad-656">[環境](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span><span class="sxs-lookup"><span data-stu-id="c57ad-656">[Environment](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)</span></span>
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* <span data-ttu-id="c57ad-657">[構成](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span><span class="sxs-lookup"><span data-stu-id="c57ad-657">[Configuration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)</span></span>
* <span data-ttu-id="c57ad-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-658"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)</span></span>
* <span data-ttu-id="c57ad-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span><span class="sxs-lookup"><span data-stu-id="c57ad-659"><xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)</span></span>
* <xref:Microsoft.Extensions.Hosting.IHost>
* <span data-ttu-id="c57ad-660">[オプション](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span><span class="sxs-lookup"><span data-stu-id="c57ad-660">[Options](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)</span></span>
* <span data-ttu-id="c57ad-661">[ログ](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span><span class="sxs-lookup"><span data-stu-id="c57ad-661">[Logging](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)</span></span>

## <a name="host-configuration"></a><span data-ttu-id="c57ad-662">ホストの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-662">Host configuration</span></span>

<span data-ttu-id="c57ad-663">ホストの構成は、次のようにして作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-663">Host configuration is created by:</span></span>

* <span data-ttu-id="c57ad-664"><xref:Microsoft.Extensions.Hosting.IHostBuilder> で拡張メソッドを呼び出して、[コンテンツ ルート](#content-root)と[環境](#environment)を設定する。</span><span class="sxs-lookup"><span data-stu-id="c57ad-664">Calling extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder> to set the [content root](#content-root) and [environment](#environment).</span></span>
* <span data-ttu-id="c57ad-665"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> の構成プロバイダーから構成を読み取る。</span><span class="sxs-lookup"><span data-stu-id="c57ad-665">Reading configuration from configuration providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>.</span></span>

### <a name="extension-methods"></a><span data-ttu-id="c57ad-666">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="c57ad-666">Extension methods</span></span>

### <a name="application-key-name"></a><span data-ttu-id="c57ad-667">アプリケーション キー (名前)</span><span class="sxs-lookup"><span data-stu-id="c57ad-667">Application key (name)</span></span>

<span data-ttu-id="c57ad-668">[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) プロパティは、ホストの構築時にホストの構成から設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-668">The [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) property is set from host configuration during host construction.</span></span> <span data-ttu-id="c57ad-669">値を明示的に設定するには、[HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey) を使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-669">To set the value explicitly, use the [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):</span></span>

<span data-ttu-id="c57ad-670">**キー**: `applicationName`</span><span class="sxs-lookup"><span data-stu-id="c57ad-670">**Key**: `applicationName`</span></span>  
<span data-ttu-id="c57ad-671">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-671">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-672">**既定**:アプリのエントリ ポイントを含むアセンブリの名前。</span><span class="sxs-lookup"><span data-stu-id="c57ad-672">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="c57ad-673">**次を使用して設定**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span><span class="sxs-lookup"><span data-stu-id="c57ad-673">**Set using**: `HostBuilderContext.HostingEnvironment.ApplicationName`</span></span>  
<span data-ttu-id="c57ad-674">**環境変数**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c57ad-674">**Environment variable**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

### <a name="content-root"></a><span data-ttu-id="c57ad-675">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="c57ad-675">Content root</span></span>

<span data-ttu-id="c57ad-676">この設定では、ホストがコンテンツ ファイルの検索を開始する場所を決定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-676">This setting determines where the host begins searching for content files.</span></span>

<span data-ttu-id="c57ad-677">**キー**: `contentRoot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-677">**Key**: `contentRoot`</span></span>  
<span data-ttu-id="c57ad-678">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-678">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-679">**既定**:既定でアプリ アセンブリが存在するフォルダーに設定されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-679">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="c57ad-680">**次を使用して設定**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="c57ad-680">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="c57ad-681">**環境変数**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c57ad-681">**Environment variable**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="c57ad-682">パスが存在しない場合は、ホストを起動できません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-682">If the path doesn't exist, the host fails to start.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

<span data-ttu-id="c57ad-683">詳細については、[基礎: コンテンツ ルート](xref:fundamentals/index#content-root)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-683">For more information, see [Fundamentals: Content root](xref:fundamentals/index#content-root).</span></span>

### <a name="environment"></a><span data-ttu-id="c57ad-684">環境</span><span class="sxs-lookup"><span data-stu-id="c57ad-684">Environment</span></span>

<span data-ttu-id="c57ad-685">アプリの[環境](xref:fundamentals/environments)を設定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-685">Sets the app's [environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="c57ad-686">**キー**: `environment`</span><span class="sxs-lookup"><span data-stu-id="c57ad-686">**Key**: `environment`</span></span>  
<span data-ttu-id="c57ad-687">**型**: `string`</span><span class="sxs-lookup"><span data-stu-id="c57ad-687">**Type**: `string`</span></span>  
<span data-ttu-id="c57ad-688">**既定値**: `Production`</span><span class="sxs-lookup"><span data-stu-id="c57ad-688">**Default**: `Production`</span></span>  
<span data-ttu-id="c57ad-689">**次を使用して設定**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="c57ad-689">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="c57ad-690">**環境変数**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` は[オプションであり、ユーザー定義です](#configurehostconfiguration))</span><span class="sxs-lookup"><span data-stu-id="c57ad-690">**Environment variable**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` is [optional and user-defined](#configurehostconfiguration))</span></span>

<span data-ttu-id="c57ad-691">環境は任意の値に設定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-691">The environment can be set to any value.</span></span> <span data-ttu-id="c57ad-692">フレームワークで定義された値には `Development`、`Staging`、`Production` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-692">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="c57ad-693">値は大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-693">Values aren't case-sensitive.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a><span data-ttu-id="c57ad-694">ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="c57ad-694">ConfigureHostConfiguration</span></span>

<span data-ttu-id="c57ad-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、ホストの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-695"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the host.</span></span> <span data-ttu-id="c57ad-696">ホストの構成は、<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> をアプリのビルド プロセスで使用するための初期化に使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-696">The host configuration is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> for use in the app's build process.</span></span>

<span data-ttu-id="c57ad-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-697"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-698">ホストは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-698">The host uses whichever option sets a value last on a given key.</span></span>

<span data-ttu-id="c57ad-699">既定ではプロバイダーが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="c57ad-699">No providers are included by default.</span></span> <span data-ttu-id="c57ad-700">次のような、アプリが <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> で必要とする構成プロバイダーを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-700">You must explicitly specify whatever configuration providers the app requires in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>, including:</span></span>

* <span data-ttu-id="c57ad-701">ファイルの構成 (*hostsettings.json* ファイルからなど)。</span><span class="sxs-lookup"><span data-stu-id="c57ad-701">File configuration (for example, from a *hostsettings.json* file).</span></span>
* <span data-ttu-id="c57ad-702">環境変数の構成。</span><span class="sxs-lookup"><span data-stu-id="c57ad-702">Environment variable configuration.</span></span>
* <span data-ttu-id="c57ad-703">コマンドライン引数の構成。</span><span class="sxs-lookup"><span data-stu-id="c57ad-703">Command-line argument configuration.</span></span>
* <span data-ttu-id="c57ad-704">その他に必要なすべての構成プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="c57ad-704">Any other required configuration providers.</span></span>

<span data-ttu-id="c57ad-705">ホストのファイル構成は、いずれかの[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)に対する呼び出しの前に `SetBasePath` のアプリのベース パスを指定することで有効になります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-705">File configuration of the host is enabled by specifying the app's base path with `SetBasePath` followed by a call to one of the [file configuration providers](xref:fundamentals/configuration/index#file-configuration-provider).</span></span> <span data-ttu-id="c57ad-706">サンプル アプリは、JSON ファイル (*hostsettings.json*) を使用し、<xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> を呼び出して、ファイルのホスト構成設定を使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-706">The sample app uses a JSON file, *hostsettings.json*, and calls <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> to consume the file's host configuration settings.</span></span>

<span data-ttu-id="c57ad-707">ホストの[環境変数の構成](xref:fundamentals/configuration/index#environment-variables-configuration-provider)を追加するには、ホスト ビルダーで <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-707">To add [environment variable configuration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) of the host, call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> on the host builder.</span></span> <span data-ttu-id="c57ad-708">`AddEnvironmentVariables` は、オプションのユーザー定義プレフィックスを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-708">`AddEnvironmentVariables` accepts an optional user-defined prefix.</span></span> <span data-ttu-id="c57ad-709">サンプル アプリは、`PREFIX_` のプレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-709">The sample app uses a prefix of `PREFIX_`.</span></span> <span data-ttu-id="c57ad-710">環境変数が読み取られると、プレフィックスは削除されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-710">The prefix is removed when the environment variables are read.</span></span> <span data-ttu-id="c57ad-711">サンプル アプリのホストが構成されると、`PREFIX_ENVIRONMENT` の環境変数の値が `environment` キーのホスト構成値になります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-711">When the sample app's host is configured, the environment variable value for `PREFIX_ENVIRONMENT` becomes the host configuration value for the `environment` key.</span></span>

<span data-ttu-id="c57ad-712">開発中に [Visual Studio](https://visualstudio.microsoft.com) を使用している、または `dotnet run` を使用してアプリを実行している場合は、環境変数を *Properties/launchSettings.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-712">During development when using [Visual Studio](https://visualstudio.microsoft.com) or running an app with `dotnet run`, environment variables may be set in the *Properties/launchSettings.json* file.</span></span> <span data-ttu-id="c57ad-713">[Visual Studio Code](https://code.visualstudio.com/) では、開発中に環境変数を *.vscode/launch.json* ファイルに設定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-713">In [Visual Studio Code](https://code.visualstudio.com/), environment variables may be set in the *.vscode/launch.json* file during development.</span></span> <span data-ttu-id="c57ad-714">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-714">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="c57ad-715">[コマンドラインの構成](xref:fundamentals/configuration/index#command-line-configuration-provider)は、<xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>を呼び出すことで追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-715">[Command-line configuration](xref:fundamentals/configuration/index#command-line-configuration-provider) is added by calling <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span> <span data-ttu-id="c57ad-716">コマンドラインの構成は、コマンドライン引数を許可して以前の構成プロバイダーから提供された構成をオーバーライドするために最後に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-716">Command-line configuration is added last to permit command-line arguments to override configuration provided by the earlier configuration providers.</span></span>

<span data-ttu-id="c57ad-717">*hostsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c57ad-717">*hostsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

<span data-ttu-id="c57ad-718">追加の構成は、[applicationName](#application-key-name) と [contentRoot](#content-root) キーで指定することができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-718">Additional configuration can be provided with the [applicationName](#application-key-name) and [contentRoot](#content-root) keys.</span></span>

<span data-ttu-id="c57ad-719"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> を使う `HostBuilder` の構成の例:</span><span class="sxs-lookup"><span data-stu-id="c57ad-719">Example `HostBuilder` configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a><span data-ttu-id="c57ad-720">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="c57ad-720">ConfigureAppConfiguration</span></span>

<span data-ttu-id="c57ad-721">アプリの構成は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装で <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を呼び出すことで作成されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-721">App configuration is created by calling <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> on the <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation.</span></span> <span data-ttu-id="c57ad-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> は <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> を使用して、アプリの <xref:Microsoft.Extensions.Configuration.IConfiguration> を作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-722"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uses an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to create an <xref:Microsoft.Extensions.Configuration.IConfiguration> for the app.</span></span> <span data-ttu-id="c57ad-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-723"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> can be called multiple times with additive results.</span></span> <span data-ttu-id="c57ad-724">アプリは、指定されたキーで最後に値を設定したオプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-724">The app uses whichever option sets a value last on a given key.</span></span> <span data-ttu-id="c57ad-725"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> によって作成された構成は、以降の操作に対する [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) において、および <xref:Microsoft.Extensions.Hosting.IHost.Services*>サービス内で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-725">The configuration created by <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available at [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) for subsequent operations and in <xref:Microsoft.Extensions.Hosting.IHost.Services*>.</span></span>

<span data-ttu-id="c57ad-726">アプリの構成は、[ConfigureHostConfiguration](#configurehostconfiguration) によって提供されたホストの構成を自動的に受信します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-726">App configuration automatically receives host configuration provided by [ConfigureHostConfiguration](#configurehostconfiguration).</span></span>

<span data-ttu-id="c57ad-727"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> を使うアプリ構成の例:</span><span class="sxs-lookup"><span data-stu-id="c57ad-727">Example app configuration using <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

<span data-ttu-id="c57ad-728">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c57ad-728">*appsettings.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

<span data-ttu-id="c57ad-729">*appsettings.Development.json*:</span><span class="sxs-lookup"><span data-stu-id="c57ad-729">*appsettings.Development.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

<span data-ttu-id="c57ad-730">*appsettings.Production.json*:</span><span class="sxs-lookup"><span data-stu-id="c57ad-730">*appsettings.Production.json*:</span></span>

[!code-json[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

<span data-ttu-id="c57ad-731">設定ファイルを出力ディレクトリに移動するには、プロジェクト ファイルの設定ファイルを [MSBuild プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items) として指定します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-731">To move settings files to the output directory, specify the settings files as [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) in the project file.</span></span> <span data-ttu-id="c57ad-732">サンプル アプリはその JSON アプリ設定ファイルと、次の `<Content>` 項目を含む *hostsettings.json* を移動します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-732">The sample app moves its JSON app settings files and *hostsettings.json* with the following `<Content>` item:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <span data-ttu-id="c57ad-733"><xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> や <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> などの構成拡張メソッドでは、[Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) や [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables) など、追加の NuGet パッケージを必要とします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-733">Configuration extension methods, such as <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> and <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> require additional NuGet packages, such as [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) and [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables).</span></span> <span data-ttu-id="c57ad-734">アプリが [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) を使用しない限り、中心となる [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) パッケージに加えて、これらのパッケージがプロジェクトに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-734">Unless the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), these packages must be added to the project in addition to the core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) package.</span></span> <span data-ttu-id="c57ad-735">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-735">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="configureservices"></a><span data-ttu-id="c57ad-736">ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="c57ad-736">ConfigureServices</span></span>

<span data-ttu-id="c57ad-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> は、アプリの[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーにサービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-737"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> adds services to the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="c57ad-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-738"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="c57ad-739">ホストされるサービスは、<xref:Microsoft.Extensions.Hosting.IHostedService> インターフェイスを実装するバックグラウンド タスク ロジックを持つクラスです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-739">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="c57ad-740">詳細については、「<xref:fundamentals/host/hosted-services>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-740">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

<span data-ttu-id="c57ad-741">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)では、`AddHostedService` 拡張メソッドを使って、有効期間イベント `LifetimeEventsHostedService` と時刻指定付きバックグラウンド タスク `TimedHostedService` のサービスをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-741">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses the `AddHostedService` extension method to add a service for lifetime events, `LifetimeEventsHostedService`, and a timed background task, `TimedHostedService`, to the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a><span data-ttu-id="c57ad-742">ConfigureLogging</span><span class="sxs-lookup"><span data-stu-id="c57ad-742">ConfigureLogging</span></span>

<span data-ttu-id="c57ad-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> は、指定された <xref:Microsoft.Extensions.Logging.ILoggingBuilder> を構成するためのデリゲートを追加します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-743"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> adds a delegate for configuring the provided <xref:Microsoft.Extensions.Logging.ILoggingBuilder>.</span></span> <span data-ttu-id="c57ad-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-744"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> may be called multiple times with additive results.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a><span data-ttu-id="c57ad-745">UseConsoleLifetime</span><span class="sxs-lookup"><span data-stu-id="c57ad-745">UseConsoleLifetime</span></span>

<span data-ttu-id="c57ad-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> では、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンし、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を呼び出して、シャットダウン プロセスを開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-746"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM and calls <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to start the shutdown process.</span></span> <span data-ttu-id="c57ad-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> は、[RunAsync](#runasync) や [WaitForShutdownAsync](#waitforshutdownasync) などの拡張機能のブロックを解除します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-747"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> unblocks extensions such as [RunAsync](#runasync) and [WaitForShutdownAsync](#waitforshutdownasync).</span></span> <span data-ttu-id="c57ad-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` は、既定の有効期間の実装として事前に登録されています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-748">`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` is pre-registered as the default lifetime implementation.</span></span> <span data-ttu-id="c57ad-749">最後に登録された有効期間が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-749">The last lifetime registered is used.</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a><span data-ttu-id="c57ad-750">コンテナーの構成</span><span class="sxs-lookup"><span data-stu-id="c57ad-750">Container configuration</span></span>

<span data-ttu-id="c57ad-751">他のコンテナーの接続をサポートするため、ホストは <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601> を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-751">To support plugging in other containers, the host can accept an <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>.</span></span> <span data-ttu-id="c57ad-752">ファクトリの提供は、DI コンテナーの登録の一部ではなく、具象 DI コンテナーの作成に使用されるホストの組み込みです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-752">Providing a factory isn't part of the DI container registration but is instead a host intrinsic used to create the concrete DI container.</span></span> <span data-ttu-id="c57ad-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) は、アプリのサービス プロバイダーの作成に使われる既定のファクトリをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-753">[UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) overrides the default factory used to create the app's service provider.</span></span>

<span data-ttu-id="c57ad-754">カスタム コンテナーの構成は、<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> メソッドによって管理されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-754">Custom container configuration is managed by the <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> method.</span></span> <span data-ttu-id="c57ad-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> は、基盤のホスト API を基にしてコンテナーを構成するための、厳密に型指定されたエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-755"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> provides a strongly-typed experience for configuring the container on top of the underlying host API.</span></span> <span data-ttu-id="c57ad-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> を複数回呼び出して結果を追加できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-756"><xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> can be called multiple times with additive results.</span></span>

<span data-ttu-id="c57ad-757">アプリのサービス コンテナーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-757">Create a service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

<span data-ttu-id="c57ad-758">サービス コンテナーのファクトリを提供します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-758">Provide a service container factory:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

<span data-ttu-id="c57ad-759">ファクトリを使って、アプリのカスタム サービス コンテナーを構成します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-759">Use the factory and configure the custom service container for the app:</span></span>

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a><span data-ttu-id="c57ad-760">機能拡張</span><span class="sxs-lookup"><span data-stu-id="c57ad-760">Extensibility</span></span>

<span data-ttu-id="c57ad-761">ホスト拡張機能は、<xref:Microsoft.Extensions.Hosting.IHostBuilder> 上の拡張メソッドによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-761">Host extensibility is performed with extension methods on <xref:Microsoft.Extensions.Hosting.IHostBuilder>.</span></span> <span data-ttu-id="c57ad-762">次の例では、拡張メソッドが <xref:fundamentals/host/hosted-services> で示される [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) の例を使って <xref:Microsoft.Extensions.Hosting.IHostBuilder> の実装を拡張する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c57ad-762">The following example shows how an extension method extends an <xref:Microsoft.Extensions.Hosting.IHostBuilder> implementation with the [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks) example demonstrated in <xref:fundamentals/host/hosted-services>.</span></span>

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

<span data-ttu-id="c57ad-763">アプリは `UseHostedService` 拡張メソッドを確率して `T` に渡されたホステッド サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-763">An app establishes the `UseHostedService` extension method to register the hosted service passed in `T`:</span></span>

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

## <a name="manage-the-host"></a><span data-ttu-id="c57ad-764">ホストを管理する</span><span class="sxs-lookup"><span data-stu-id="c57ad-764">Manage the host</span></span>

<span data-ttu-id="c57ad-765"><xref:Microsoft.Extensions.Hosting.IHost> の実装は、サービス コンテナーに登録されている <xref:Microsoft.Extensions.Hosting.IHostedService> の実装の開始と停止を行います。</span><span class="sxs-lookup"><span data-stu-id="c57ad-765">The <xref:Microsoft.Extensions.Hosting.IHost> implementation is responsible for starting and stopping the <xref:Microsoft.Extensions.Hosting.IHostedService> implementations that are registered in the service container.</span></span>

### <a name="run"></a><span data-ttu-id="c57ad-766">実行</span><span class="sxs-lookup"><span data-stu-id="c57ad-766">Run</span></span>

<span data-ttu-id="c57ad-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> はアプリを実行し、ホストがシャットダウンされるまで呼び出し元のスレッドをブロックします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-767"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> runs the app and blocks the calling thread until the host is shut down:</span></span>

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

### <a name="runasync"></a><span data-ttu-id="c57ad-768">RunAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-768">RunAsync</span></span>

<span data-ttu-id="c57ad-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> はアプリを実行し、キャンセル トークンまたはシャットダウンがトリガーされると完了する <xref:System.Threading.Tasks.Task> を返します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-769"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> runs the app and returns a <xref:System.Threading.Tasks.Task> that completes when the cancellation token or shutdown is triggered:</span></span>

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

### <a name="runconsoleasync"></a><span data-ttu-id="c57ad-770">RunConsoleAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-770">RunConsoleAsync</span></span>

<span data-ttu-id="c57ad-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> は、コンソールのサポートを有効にし、ホストをビルドして開始した後、<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM がシャットダウンするのを待機します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-771"><xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> enables console support, builds and starts the host, and waits for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM to shut down.</span></span>

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

### <a name="start-and-stopasync"></a><span data-ttu-id="c57ad-772">Start と StopAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-772">Start and StopAsync</span></span>

<span data-ttu-id="c57ad-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> は、ホストを同期的に開始します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-773"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> starts the host synchronously.</span></span>

<span data-ttu-id="c57ad-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> は、指定されたタイムアウト内でホストの停止を試みます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-774"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> attempts to stop the host within the provided timeout.</span></span>

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

### <a name="startasync-and-stopasync"></a><span data-ttu-id="c57ad-775">StartAsync と StopAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-775">StartAsync and StopAsync</span></span>

<span data-ttu-id="c57ad-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> がアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-776"><xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> starts the app.</span></span>

<span data-ttu-id="c57ad-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> がアプリを停止します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-777"><xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> stops the app.</span></span>

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

### <a name="waitforshutdown"></a><span data-ttu-id="c57ad-778">WaitForShutdown</span><span class="sxs-lookup"><span data-stu-id="c57ad-778">WaitForShutdown</span></span>

<span data-ttu-id="c57ad-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は、`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (<kbd>Ctrl</kbd> + <kbd>C</kbd>/SIGINT または SIGTERM をリッスンする) などの <xref:Microsoft.Extensions.Hosting.IHostLifetime> を介してトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-779"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> is triggered via the <xref:Microsoft.Extensions.Hosting.IHostLifetime>, such as `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` (listens for <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT or SIGTERM).</span></span> <span data-ttu-id="c57ad-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> は <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-780"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="waitforshutdownasync"></a><span data-ttu-id="c57ad-781">WaitForShutdownAsync</span><span class="sxs-lookup"><span data-stu-id="c57ad-781">WaitForShutdownAsync</span></span>

<span data-ttu-id="c57ad-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> が返す <xref:System.Threading.Tasks.Task> は、提供されたトークンによってシャットダウンがトリガーされると完了し、<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-782"><xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> returns a <xref:System.Threading.Tasks.Task> that completes when shutdown is triggered via the given token and calls <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>.</span></span>

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

### <a name="external-control"></a><span data-ttu-id="c57ad-783">外部コントロール</span><span class="sxs-lookup"><span data-stu-id="c57ad-783">External control</span></span>

<span data-ttu-id="c57ad-784">ホストの外部コントロールは、外部から呼び出すことができるメソッドを使って実現できます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-784">External control of the host can be achieved using methods that can be called externally:</span></span>

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

<span data-ttu-id="c57ad-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> は <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> の開始時に呼び出され、これが完了するまで待機してから続行します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-785"><xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> is called at the start of <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>, which waits until it's complete before continuing.</span></span> <span data-ttu-id="c57ad-786">これを使って、外部イベントによって通知されるまで開始を遅らせることができます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-786">This can be used to delay startup until signaled by an external event.</span></span>

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="c57ad-787">IHostingEnvironment インターフェイス</span><span class="sxs-lookup"><span data-stu-id="c57ad-787">IHostingEnvironment interface</span></span>

<span data-ttu-id="c57ad-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> は、アプリのホスティング環境に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-788"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> provides information about the app's hosting environment.</span></span> <span data-ttu-id="c57ad-789">プロパティと拡張メソッドを使用するには、[コンストラクターの挿入](xref:fundamentals/dependency-injection)を使用して <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> を取得します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-789">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> in order to use its properties and extension methods:</span></span>

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

<span data-ttu-id="c57ad-790">詳細については、「<xref:fundamentals/environments>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c57ad-790">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="c57ad-791">IApplicationLifetime インターフェイス</span><span class="sxs-lookup"><span data-stu-id="c57ad-791">IApplicationLifetime interface</span></span>

<span data-ttu-id="c57ad-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> は、正常なシャットダウンの要求など、起動後とシャットダウンのアクティビティに対応します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-792"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime> allows for post-startup and shutdown activities, including graceful shutdown requests.</span></span> <span data-ttu-id="c57ad-793">インターフェイスの 3 つのプロパティは、起動とシャットダウン イベントを定義する <xref:System.Action> メソッドを登録するために使用されるキャンセル トークンです。</span><span class="sxs-lookup"><span data-stu-id="c57ad-793">Three properties on the interface are cancellation tokens used to register <xref:System.Action> methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="c57ad-794">キャンセル トークン</span><span class="sxs-lookup"><span data-stu-id="c57ad-794">Cancellation Token</span></span> | <span data-ttu-id="c57ad-795">トリガーのタイミング</span><span class="sxs-lookup"><span data-stu-id="c57ad-795">Triggered when&#8230;</span></span> |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | <span data-ttu-id="c57ad-796">ホストが完全に起動されたとき。</span><span class="sxs-lookup"><span data-stu-id="c57ad-796">The host has fully started.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | <span data-ttu-id="c57ad-797">ホストが正常なシャットダウンを完了しているとき。</span><span class="sxs-lookup"><span data-stu-id="c57ad-797">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="c57ad-798">すべての要求を処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c57ad-798">All requests should be processed.</span></span> <span data-ttu-id="c57ad-799">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-799">Shutdown blocks until this event completes.</span></span> |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | <span data-ttu-id="c57ad-800">ホストが正常なシャットダウンを行っているとき。</span><span class="sxs-lookup"><span data-stu-id="c57ad-800">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="c57ad-801">要求がまだ処理されている可能性がある場合。</span><span class="sxs-lookup"><span data-stu-id="c57ad-801">Requests may still be processing.</span></span> <span data-ttu-id="c57ad-802">このイベントが完了するまで、シャットダウンはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="c57ad-802">Shutdown blocks until this event completes.</span></span> |

<span data-ttu-id="c57ad-803">コンストラクターは任意のクラスに <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-803">Constructor-inject the <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> service into any class.</span></span> <span data-ttu-id="c57ad-804">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/)は、`LifetimeEventsHostedService` クラス (<xref:Microsoft.Extensions.Hosting.IHostedService> の実装) へのコンストラクターの挿入を使って、イベントを登録します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-804">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) uses constructor injection into a `LifetimeEventsHostedService` class (an <xref:Microsoft.Extensions.Hosting.IHostedService> implementation) to register the events.</span></span>

<span data-ttu-id="c57ad-805">*LifetimeEventsHostedService.cs*:</span><span class="sxs-lookup"><span data-stu-id="c57ad-805">*LifetimeEventsHostedService.cs*:</span></span>

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<span data-ttu-id="c57ad-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> は、アプリの終了を要求します。</span><span class="sxs-lookup"><span data-stu-id="c57ad-806"><xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> requests termination of the app.</span></span> <span data-ttu-id="c57ad-807">以下のクラスは、クラスの `Shutdown` メソッドが呼び出されると、<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> を使ってアプリを正常にシャットダウンします。</span><span class="sxs-lookup"><span data-stu-id="c57ad-807">The following class uses <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c57ad-808">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c57ad-808">Additional resources</span></span>

* <xref:fundamentals/host/hosted-services>
