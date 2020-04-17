---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440741"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="15550-103">.NET Core および ASP.NET Core でのログ記録</span><span class="sxs-lookup"><span data-stu-id="15550-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="15550-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="15550-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="15550-105">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="15550-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="15550-106">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="15550-107">この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。</span><span class="sxs-lookup"><span data-stu-id="15550-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="15550-108">これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="15550-109">非 Web コンソール アプリで汎用ホストを使用する方法の例については、[バックグラウンド タスクのサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) の *Program.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="15550-110">汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="15550-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="15550-111">ホスト以外のコードの例については、記事のこれらのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="15550-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="15550-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="15550-113">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="15550-113">Add providers</span></span>

<span data-ttu-id="15550-114">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="15550-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="15550-115">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="15550-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="15550-116">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="15550-117">汎用ホストを使用するアプリにプロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="15550-118">ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="15550-119">`LoggerFactory` および `AddConsole` には、`Microsoft.Extensions.Logging` 用に `using` ステートメントが必要です。</span><span class="sxs-lookup"><span data-stu-id="15550-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="15550-120">既定の ASP.NET Core プロジェクト テンプレートからは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="15550-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="15550-121">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="15550-122">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="15550-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="15550-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="15550-124">[EventLog](#windows-eventlog-provider) (Windows 上で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="15550-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="15550-125">既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="15550-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="15550-127">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="15550-128">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-128">Create logs</span></span>

<span data-ttu-id="15550-129">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="15550-130">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="15550-131">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="15550-132">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="15550-133">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="15550-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="15550-134">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="15550-135">次のホスト コンソール以外のアプリの例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="15550-136">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="15550-137">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="15550-138">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="15550-139">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-139">Create logs in the Program class</span></span>

<span data-ttu-id="15550-140">ASP.NET Core アプリの `Program` クラスでログを書き込むには、ホストをビルドした後に DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="15550-141">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="15550-142">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-142">However, a separate logger can be used.</span></span> <span data-ttu-id="15550-143">次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="15550-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="15550-144">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="15550-145">Startup クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-145">Create logs in the Startup class</span></span>

<span data-ttu-id="15550-146">ASP.NET Core アプリの `Startup.Configure` メソッドでログを書き込むには、メソッド シグネチャに `ILogger` パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="15550-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="15550-147">`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="15550-148">`Startup` コンストラクターへのロガーの挿入はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="15550-149">`Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="15550-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="15550-150">この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。</span><span class="sxs-lookup"><span data-stu-id="15550-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="15550-151">DI コンテナーは、`ConfigureServices` が完了するまで設定されません。</span><span class="sxs-lookup"><span data-stu-id="15550-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="15550-152">ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。</span><span class="sxs-lookup"><span data-stu-id="15550-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="15550-153">汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="15550-154">`ILogger<T>` に依存するサービスを構成する必要がある場合でも、コンストラクターの挿入を使用するか、ファクトリ メソッドを用意して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="15550-155">ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="15550-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="15550-156">たとえば、DI のサービスを使用してプロパティを設定する必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="15550-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="15550-157">前の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスが初めて作成されるときに実行される `Func` です。</span><span class="sxs-lookup"><span data-stu-id="15550-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="15550-158">この方法では、任意の登録済みサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="15550-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="15550-159">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="15550-159">No asynchronous logger methods</span></span>

<span data-ttu-id="15550-160">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="15550-161">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="15550-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="15550-162">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="15550-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="15550-163">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="15550-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="15550-164">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="15550-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="15550-165">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="15550-166">構成</span><span class="sxs-lookup"><span data-stu-id="15550-166">Configuration</span></span>

<span data-ttu-id="15550-167">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="15550-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="15550-168">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="15550-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="15550-169">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="15550-169">Command-line arguments.</span></span>
* <span data-ttu-id="15550-170">環境変数。</span><span class="sxs-lookup"><span data-stu-id="15550-170">Environment variables.</span></span>
* <span data-ttu-id="15550-171">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="15550-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="15550-172">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="15550-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="15550-173">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="15550-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="15550-174">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="15550-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="15550-175">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="15550-176">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="15550-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="15550-177">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="15550-178">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="15550-179">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="15550-180">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="15550-181">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="15550-181">The example is for the Console provider.</span></span> <span data-ttu-id="15550-182">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="15550-183">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="15550-184">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="15550-185">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="15550-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="15550-186">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="15550-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="15550-187">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="15550-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="15550-188">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="15550-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="15550-189">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="15550-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="15550-190">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="15550-191">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="15550-191">Sample logging output</span></span>

<span data-ttu-id="15550-192">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15550-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="15550-193">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="15550-193">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

<span data-ttu-id="15550-194">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="15550-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="15550-195">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

<span data-ttu-id="15550-196">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApiSample" から始まります。</span><span class="sxs-lookup"><span data-stu-id="15550-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="15550-197">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="15550-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="15550-198">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="15550-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="15550-199">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="15550-200">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="15550-200">NuGet packages</span></span>

<span data-ttu-id="15550-201">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="15550-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="15550-202">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-202">Log category</span></span>

<span data-ttu-id="15550-203">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="15550-204">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="15550-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="15550-205">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="15550-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="15550-206">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="15550-207">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="15550-208">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="15550-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="15550-209">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="15550-209">Log level</span></span>

<span data-ttu-id="15550-210">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="15550-211">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="15550-212">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="15550-213">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="15550-214">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="15550-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="15550-215">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="15550-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="15550-216">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="15550-217">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="15550-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="15550-218">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="15550-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="15550-219">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="15550-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="15550-220">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="15550-221">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="15550-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="15550-222">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="15550-222">Trace = 0</span></span>

  <span data-ttu-id="15550-223">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="15550-224">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="15550-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="15550-225">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="15550-225">*Disabled by default.*</span></span>

* <span data-ttu-id="15550-226">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="15550-226">Debug = 1</span></span>

  <span data-ttu-id="15550-227">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="15550-228">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="15550-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="15550-229">Information = 2</span><span class="sxs-lookup"><span data-stu-id="15550-229">Information = 2</span></span>

  <span data-ttu-id="15550-230">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="15550-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="15550-231">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="15550-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="15550-232">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="15550-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="15550-233">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="15550-233">Warning = 3</span></span>

  <span data-ttu-id="15550-234">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="15550-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="15550-235">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="15550-236">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="15550-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="15550-237">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="15550-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="15550-238">Error = 4</span><span class="sxs-lookup"><span data-stu-id="15550-238">Error = 4</span></span>

  <span data-ttu-id="15550-239">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="15550-240">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="15550-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="15550-241">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="15550-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="15550-242">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="15550-242">Critical = 5</span></span>

  <span data-ttu-id="15550-243">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="15550-243">For failures that require immediate attention.</span></span> <span data-ttu-id="15550-244">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="15550-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="15550-245">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="15550-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="15550-246">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-246">For example:</span></span>

* <span data-ttu-id="15550-247">運用環境:</span><span class="sxs-lookup"><span data-stu-id="15550-247">In production:</span></span>
  * <span data-ttu-id="15550-248">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="15550-249">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="15550-250">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="15550-251">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="15550-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="15550-252">開発中:</span><span class="sxs-lookup"><span data-stu-id="15550-252">During development:</span></span>
  * <span data-ttu-id="15550-253">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="15550-254">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="15550-255">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="15550-256">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="15550-257">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="15550-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="15550-258">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="15550-259">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="15550-259">Log event ID</span></span>

<span data-ttu-id="15550-260">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="15550-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="15550-261">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="15550-262">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="15550-262">An event ID associates a set of events.</span></span> <span data-ttu-id="15550-263">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="15550-264">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="15550-265">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="15550-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="15550-266">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="15550-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="15550-267">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="15550-267">Log message template</span></span>

<span data-ttu-id="15550-268">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-268">Each log specifies a message template.</span></span> <span data-ttu-id="15550-269">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="15550-270">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="15550-271">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="15550-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="15550-272">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="15550-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="15550-273">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="15550-274">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="15550-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="15550-275">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="15550-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="15550-276">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="15550-277">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="15550-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="15550-278">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="15550-279">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="15550-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="15550-280">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="15550-280">Logging exceptions</span></span>

<span data-ttu-id="15550-281">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="15550-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="15550-282">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="15550-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="15550-283">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="15550-284">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="15550-284">Log filtering</span></span>

<span data-ttu-id="15550-285">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="15550-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="15550-286">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="15550-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="15550-287">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="15550-288">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="15550-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="15550-289">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="15550-289">Create filter rules in configuration</span></span>

<span data-ttu-id="15550-290">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="15550-291">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="15550-292">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="15550-293">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="15550-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="15550-294">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="15550-295">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="15550-295">Filter rules in code</span></span>

<span data-ttu-id="15550-296">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="15550-297">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="15550-298">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="15550-299">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="15550-299">How filtering rules are applied</span></span>

<span data-ttu-id="15550-300">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="15550-301">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="15550-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="15550-302">数値</span><span class="sxs-lookup"><span data-stu-id="15550-302">Number</span></span> | <span data-ttu-id="15550-303">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-303">Provider</span></span>      | <span data-ttu-id="15550-304">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-304">Categories that begin with ...</span></span>          | <span data-ttu-id="15550-305">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="15550-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="15550-306">1</span><span class="sxs-lookup"><span data-stu-id="15550-306">1</span></span>      | <span data-ttu-id="15550-307">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-307">Debug</span></span>         | <span data-ttu-id="15550-308">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-308">All categories</span></span>                          | <span data-ttu-id="15550-309">情報</span><span class="sxs-lookup"><span data-stu-id="15550-309">Information</span></span>       |
| <span data-ttu-id="15550-310">2</span><span class="sxs-lookup"><span data-stu-id="15550-310">2</span></span>      | <span data-ttu-id="15550-311">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-311">Console</span></span>       | <span data-ttu-id="15550-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="15550-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="15550-313">警告</span><span class="sxs-lookup"><span data-stu-id="15550-313">Warning</span></span>           |
| <span data-ttu-id="15550-314">3</span><span class="sxs-lookup"><span data-stu-id="15550-314">3</span></span>      | <span data-ttu-id="15550-315">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-315">Console</span></span>       | <span data-ttu-id="15550-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="15550-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="15550-317">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-317">Debug</span></span>             |
| <span data-ttu-id="15550-318">4</span><span class="sxs-lookup"><span data-stu-id="15550-318">4</span></span>      | <span data-ttu-id="15550-319">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-319">Console</span></span>       | <span data-ttu-id="15550-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="15550-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="15550-321">Error</span><span class="sxs-lookup"><span data-stu-id="15550-321">Error</span></span>             |
| <span data-ttu-id="15550-322">5</span><span class="sxs-lookup"><span data-stu-id="15550-322">5</span></span>      | <span data-ttu-id="15550-323">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-323">Console</span></span>       | <span data-ttu-id="15550-324">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-324">All categories</span></span>                          | <span data-ttu-id="15550-325">情報</span><span class="sxs-lookup"><span data-stu-id="15550-325">Information</span></span>       |
| <span data-ttu-id="15550-326">6</span><span class="sxs-lookup"><span data-stu-id="15550-326">6</span></span>      | <span data-ttu-id="15550-327">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-327">All providers</span></span> | <span data-ttu-id="15550-328">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-328">All categories</span></span>                          | <span data-ttu-id="15550-329">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-329">Debug</span></span>             |
| <span data-ttu-id="15550-330">7</span><span class="sxs-lookup"><span data-stu-id="15550-330">7</span></span>      | <span data-ttu-id="15550-331">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-331">All providers</span></span> | <span data-ttu-id="15550-332">システム</span><span class="sxs-lookup"><span data-stu-id="15550-332">System</span></span>                                  | <span data-ttu-id="15550-333">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-333">Debug</span></span>             |
| <span data-ttu-id="15550-334">8</span><span class="sxs-lookup"><span data-stu-id="15550-334">8</span></span>      | <span data-ttu-id="15550-335">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-335">Debug</span></span>         | <span data-ttu-id="15550-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="15550-336">Microsoft</span></span>                               | <span data-ttu-id="15550-337">トレース</span><span class="sxs-lookup"><span data-stu-id="15550-337">Trace</span></span>             |

<span data-ttu-id="15550-338">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="15550-339">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="15550-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="15550-340">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="15550-341">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="15550-342">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="15550-343">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="15550-344">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="15550-345">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="15550-346">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="15550-347">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="15550-348">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="15550-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="15550-349">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="15550-350">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="15550-351">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="15550-352">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="15550-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="15550-353">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="15550-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="15550-354">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="15550-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="15550-355">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="15550-355">Provider aliases</span></span>

<span data-ttu-id="15550-356">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="15550-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="15550-357">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="15550-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="15550-358">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-358">Console</span></span>
* <span data-ttu-id="15550-359">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-359">Debug</span></span>
* <span data-ttu-id="15550-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="15550-360">EventSource</span></span>
* <span data-ttu-id="15550-361">EventLog</span><span class="sxs-lookup"><span data-stu-id="15550-361">EventLog</span></span>
* <span data-ttu-id="15550-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="15550-362">TraceSource</span></span>
* <span data-ttu-id="15550-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="15550-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="15550-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="15550-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="15550-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="15550-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="15550-366">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="15550-366">Default minimum level</span></span>

<span data-ttu-id="15550-367">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="15550-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="15550-368">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="15550-369">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="15550-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="15550-370">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="15550-370">Filter functions</span></span>

<span data-ttu-id="15550-371">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="15550-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="15550-372">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="15550-373">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="15550-374">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="15550-374">System categories and levels</span></span>

<span data-ttu-id="15550-375">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="15550-376">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-376">Category</span></span>                            | <span data-ttu-id="15550-377">メモ</span><span class="sxs-lookup"><span data-stu-id="15550-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="15550-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="15550-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="15550-379">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="15550-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="15550-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="15550-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="15550-381">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="15550-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="15550-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="15550-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="15550-383">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="15550-383">Hosts allowed.</span></span> |
| <span data-ttu-id="15550-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="15550-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="15550-385">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="15550-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="15550-386">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="15550-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="15550-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="15550-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="15550-388">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="15550-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="15550-389">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="15550-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="15550-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="15550-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="15550-391">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="15550-391">Route matching information.</span></span> |
| <span data-ttu-id="15550-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="15550-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="15550-393">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="15550-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="15550-394">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="15550-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="15550-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="15550-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="15550-396">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="15550-396">Files served.</span></span> |
| <span data-ttu-id="15550-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="15550-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="15550-398">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="15550-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="15550-399">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="15550-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="15550-400">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="15550-400">Log scopes</span></span>

 <span data-ttu-id="15550-401">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="15550-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="15550-402">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="15550-403">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="15550-404">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="15550-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="15550-405">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="15550-406">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="15550-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="15550-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="15550-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="15550-408">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="15550-409">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="15550-410">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="15550-411">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-411">Built-in logging providers</span></span>

<span data-ttu-id="15550-412">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="15550-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="15550-413">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="15550-414">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="15550-415">EventSource</span><span class="sxs-lookup"><span data-stu-id="15550-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="15550-416">EventLog</span><span class="sxs-lookup"><span data-stu-id="15550-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="15550-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="15550-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="15550-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="15550-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="15550-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="15550-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="15550-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="15550-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="15550-421">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="15550-422">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-422">Console provider</span></span>

<span data-ttu-id="15550-423">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="15550-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="15550-424">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="15550-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="15550-425">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-425">Debug provider</span></span>

<span data-ttu-id="15550-426">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="15550-427">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="15550-428">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-428">Event Source provider</span></span>

<span data-ttu-id="15550-429">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="15550-430">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="15550-431">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="15550-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="15550-432">dotnet trace ツール</span><span class="sxs-lookup"><span data-stu-id="15550-432">dotnet trace tooling</span></span>

<span data-ttu-id="15550-433">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="15550-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="15550-434">このツールでは、<xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource> プロバイダー データを収集します。</span><span class="sxs-lookup"><span data-stu-id="15550-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="15550-435">次のコマンドを使用して、dotnet trace ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="15550-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="15550-436">dotnet trace ツールを使用して、アプリからトレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="15550-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="15550-437">アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="15550-438">`dotnet run` コマンドを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="15550-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="15550-439">.NET Core アプリのプロセス識別子 (PID) を決定します。</span><span class="sxs-lookup"><span data-stu-id="15550-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="15550-440">Windows では、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="15550-441">タスク マネージャー (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="15550-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="15550-442">tasklist コマンド</span><span class="sxs-lookup"><span data-stu-id="15550-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="15550-443">Get-Process PowerShell コマンド</span><span class="sxs-lookup"><span data-stu-id="15550-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="15550-444">Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="15550-445">アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。</span><span class="sxs-lookup"><span data-stu-id="15550-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="15550-446">`dotnet trace` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="15550-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="15550-447">一般的なコマンド構文</span><span class="sxs-lookup"><span data-stu-id="15550-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="15550-448">PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。</span><span class="sxs-lookup"><span data-stu-id="15550-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="15550-449">Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。</span><span class="sxs-lookup"><span data-stu-id="15550-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="15550-450">キーワード</span><span class="sxs-lookup"><span data-stu-id="15550-450">Keyword</span></span> | <span data-ttu-id="15550-451">説明</span><span class="sxs-lookup"><span data-stu-id="15550-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="15550-452">1</span><span class="sxs-lookup"><span data-stu-id="15550-452">1</span></span>       | <span data-ttu-id="15550-453">`LoggingEventSource` に関するメタ イベントをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="15550-454">`ILogger` からのイベントは記録されません。</span><span class="sxs-lookup"><span data-stu-id="15550-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="15550-455">2</span><span class="sxs-lookup"><span data-stu-id="15550-455">2</span></span>       | <span data-ttu-id="15550-456">`ILogger.Log()` が呼び出されたときに、`Message` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="15550-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="15550-457">プログラムで (書式設定されずに) 情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="15550-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="15550-458">4</span><span class="sxs-lookup"><span data-stu-id="15550-458">4</span></span>       | <span data-ttu-id="15550-459">`ILogger.Log()` が呼び出されたときに、`FormatMessage` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="15550-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="15550-460">書式設定された文字列バージョンの情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="15550-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="15550-461">8</span><span class="sxs-lookup"><span data-stu-id="15550-461">8</span></span>       | <span data-ttu-id="15550-462">`ILogger.Log()` が呼び出されたときに、`MessageJson` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="15550-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="15550-463">引数の JSON 表現が提供されます。</span><span class="sxs-lookup"><span data-stu-id="15550-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="15550-464">イベント レベル</span><span class="sxs-lookup"><span data-stu-id="15550-464">Event Level</span></span> | <span data-ttu-id="15550-465">説明</span><span class="sxs-lookup"><span data-stu-id="15550-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="15550-466">0</span><span class="sxs-lookup"><span data-stu-id="15550-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="15550-467">1</span><span class="sxs-lookup"><span data-stu-id="15550-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="15550-468">2</span><span class="sxs-lookup"><span data-stu-id="15550-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="15550-469">3</span><span class="sxs-lookup"><span data-stu-id="15550-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="15550-470">4</span><span class="sxs-lookup"><span data-stu-id="15550-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="15550-471">5</span><span class="sxs-lookup"><span data-stu-id="15550-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="15550-472">`{Logger Category}` と `{Event Level}` の `FilterSpecs` エントリは、追加のログ フィルター条件を表します。</span><span class="sxs-lookup"><span data-stu-id="15550-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="15550-473">セミコロン (`;`) で `FilterSpecs` エントリを区切ります。</span><span class="sxs-lookup"><span data-stu-id="15550-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="15550-474">Windows コマンド シェルを使用した例 (`--providers` 値を囲む単一引用符**なし**):</span><span class="sxs-lookup"><span data-stu-id="15550-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="15550-475">上記のコマンドにより次のことがアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="15550-475">The preceding command activates:</span></span>

   * <span data-ttu-id="15550-476">エラー (`2`) に対して書式設定された文字列 (`4`) を生成するイベント ソース ロガー。</span><span class="sxs-lookup"><span data-stu-id="15550-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="15550-477">`Informational` ログ レベル (`4`) での `Microsoft.AspNetCore.Hosting` のログ記録。</span><span class="sxs-lookup"><span data-stu-id="15550-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="15550-478">Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。</span><span class="sxs-lookup"><span data-stu-id="15550-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="15550-479">トレースは、`dotnet trace` コマンドが実行されたフォルダーに *trace.nettrace* という名前で保存されます。</span><span class="sxs-lookup"><span data-stu-id="15550-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="15550-480">[Perfview](#perfview) を使用してトレースを開きます。</span><span class="sxs-lookup"><span data-stu-id="15550-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="15550-481">*trace.nettrace* ファイルを開き、トレース イベントを調べます。</span><span class="sxs-lookup"><span data-stu-id="15550-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="15550-482">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="15550-482">For more information, see:</span></span>

* <span data-ttu-id="15550-483">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="15550-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="15550-484">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="15550-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="15550-485">[LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="15550-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="15550-486">[LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="15550-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="15550-487">[Perfview](#perfview) &ndash; イベント ソース トレースの表示に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="15550-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="15550-488">Perfview</span><span class="sxs-lookup"><span data-stu-id="15550-488">Perfview</span></span>

<span data-ttu-id="15550-489">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="15550-490">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="15550-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="15550-491">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="15550-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="15550-492">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="15550-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="15550-494">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-494">Windows EventLog provider</span></span>

<span data-ttu-id="15550-495">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="15550-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="15550-496">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="15550-497">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="15550-498">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="15550-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="15550-499">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="15550-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="15550-500">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="15550-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="15550-501">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="15550-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="15550-502">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="15550-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="15550-503">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="15550-504">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-504">TraceSource provider</span></span>

<span data-ttu-id="15550-505">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="15550-506">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="15550-507">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="15550-508">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="15550-509">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-509">Azure App Service provider</span></span>

<span data-ttu-id="15550-510">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="15550-511">プロバイダー パッケージは、共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="15550-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="15550-512">プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="15550-513">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="15550-514">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="15550-515">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="15550-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="15550-516">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="15550-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="15550-517">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="15550-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="15550-518">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="15550-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="15550-519">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="15550-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="15550-520">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="15550-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="15550-521">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="15550-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="15550-522">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="15550-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="15550-523">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="15550-523">Azure log streaming</span></span>

<span data-ttu-id="15550-524">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="15550-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="15550-525">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="15550-525">The app server</span></span>
* <span data-ttu-id="15550-526">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="15550-526">The web server</span></span>
* <span data-ttu-id="15550-527">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="15550-527">Failed request tracing</span></span>

<span data-ttu-id="15550-528">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="15550-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="15550-529">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="15550-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="15550-530">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="15550-531">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="15550-531">Choose the log **Level**.</span></span> <span data-ttu-id="15550-532">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="15550-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="15550-533">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="15550-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="15550-534">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="15550-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="15550-535">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="15550-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="15550-536">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="15550-537">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="15550-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="15550-538">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="15550-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="15550-539">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="15550-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="15550-540">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="15550-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="15550-541">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="15550-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="15550-542">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="15550-543">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="15550-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="15550-544">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="15550-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="15550-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="15550-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="15550-546">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="15550-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="15550-547">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="15550-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="15550-548">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-548">Third-party logging providers</span></span>

<span data-ttu-id="15550-549">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="15550-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="15550-550">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="15550-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="15550-552">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="15550-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="15550-553">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="15550-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="15550-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="15550-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="15550-555">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-556">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-557">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="15550-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="15550-558">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="15550-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="15550-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="15550-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="15550-560">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="15550-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="15550-561">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="15550-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="15550-562">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="15550-563">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="15550-564">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="15550-565">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15550-566">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="15550-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="15550-567">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="15550-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="15550-568">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="15550-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="15550-569">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="15550-570">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="15550-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="15550-571">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="15550-571">Add providers</span></span>

<span data-ttu-id="15550-572">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="15550-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="15550-573">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="15550-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="15550-574">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="15550-575">プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="15550-576">上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="15550-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="15550-577">既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="15550-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="15550-578">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-578">Console</span></span>
* <span data-ttu-id="15550-579">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-579">Debug</span></span>
* <span data-ttu-id="15550-580">EventSource (ASP.NET Core 2.2 以降)</span><span class="sxs-lookup"><span data-stu-id="15550-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="15550-581">`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="15550-582"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="15550-583">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="15550-584">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-584">Create logs</span></span>

<span data-ttu-id="15550-585">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="15550-586">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="15550-587">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="15550-588">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="15550-589">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="15550-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="15550-590">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="15550-591">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="15550-592">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="15550-593">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="15550-594">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-594">Create logs in Startup</span></span>

<span data-ttu-id="15550-595">`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="15550-596">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="15550-596">Create logs in the Program class</span></span>

<span data-ttu-id="15550-597">`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="15550-598">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="15550-599">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-599">However, a separate logger can be used.</span></span> <span data-ttu-id="15550-600">次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="15550-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="15550-601">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="15550-602">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="15550-602">No asynchronous logger methods</span></span>

<span data-ttu-id="15550-603">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="15550-604">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="15550-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="15550-605">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="15550-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="15550-606">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="15550-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="15550-607">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="15550-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="15550-608">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="15550-609">構成</span><span class="sxs-lookup"><span data-stu-id="15550-609">Configuration</span></span>

<span data-ttu-id="15550-610">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="15550-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="15550-611">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="15550-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="15550-612">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="15550-612">Command-line arguments.</span></span>
* <span data-ttu-id="15550-613">環境変数。</span><span class="sxs-lookup"><span data-stu-id="15550-613">Environment variables.</span></span>
* <span data-ttu-id="15550-614">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="15550-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="15550-615">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="15550-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="15550-616">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="15550-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="15550-617">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="15550-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="15550-618">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="15550-619">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="15550-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

<span data-ttu-id="15550-620">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="15550-621">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="15550-622">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="15550-623">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="15550-624">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="15550-624">The example is for the Console provider.</span></span> <span data-ttu-id="15550-625">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="15550-626">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="15550-627">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="15550-628">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="15550-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="15550-629">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="15550-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="15550-630">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="15550-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="15550-631">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="15550-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="15550-632">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="15550-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="15550-633">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="15550-634">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="15550-634">Sample logging output</span></span>

<span data-ttu-id="15550-635">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15550-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="15550-636">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="15550-636">Here's an example of console output:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

<span data-ttu-id="15550-637">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="15550-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="15550-638">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="15550-639">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。</span><span class="sxs-lookup"><span data-stu-id="15550-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="15550-640">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="15550-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="15550-641">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="15550-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="15550-642">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="15550-643">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="15550-643">NuGet packages</span></span>

<span data-ttu-id="15550-644">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="15550-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="15550-645">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-645">Log category</span></span>

<span data-ttu-id="15550-646">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="15550-647">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="15550-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="15550-648">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="15550-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="15550-649">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="15550-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="15550-650">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="15550-651">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="15550-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="15550-652">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="15550-652">Log level</span></span>

<span data-ttu-id="15550-653">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="15550-654">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="15550-655">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="15550-656">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="15550-657">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="15550-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="15550-658">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="15550-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="15550-659">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="15550-660">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="15550-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="15550-661">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="15550-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="15550-662">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="15550-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="15550-663">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="15550-664">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="15550-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="15550-665">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="15550-665">Trace = 0</span></span>

  <span data-ttu-id="15550-666">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="15550-667">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="15550-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="15550-668">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="15550-668">*Disabled by default.*</span></span>

* <span data-ttu-id="15550-669">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="15550-669">Debug = 1</span></span>

  <span data-ttu-id="15550-670">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="15550-671">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="15550-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="15550-672">Information = 2</span><span class="sxs-lookup"><span data-stu-id="15550-672">Information = 2</span></span>

  <span data-ttu-id="15550-673">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="15550-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="15550-674">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="15550-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="15550-675">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="15550-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="15550-676">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="15550-676">Warning = 3</span></span>

  <span data-ttu-id="15550-677">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="15550-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="15550-678">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="15550-679">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="15550-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="15550-680">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="15550-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="15550-681">Error = 4</span><span class="sxs-lookup"><span data-stu-id="15550-681">Error = 4</span></span>

  <span data-ttu-id="15550-682">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="15550-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="15550-683">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="15550-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="15550-684">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="15550-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="15550-685">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="15550-685">Critical = 5</span></span>

  <span data-ttu-id="15550-686">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="15550-686">For failures that require immediate attention.</span></span> <span data-ttu-id="15550-687">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="15550-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="15550-688">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="15550-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="15550-689">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-689">For example:</span></span>

* <span data-ttu-id="15550-690">運用環境:</span><span class="sxs-lookup"><span data-stu-id="15550-690">In production:</span></span>
  * <span data-ttu-id="15550-691">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="15550-692">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="15550-693">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="15550-694">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="15550-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="15550-695">開発中:</span><span class="sxs-lookup"><span data-stu-id="15550-695">During development:</span></span>
  * <span data-ttu-id="15550-696">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="15550-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="15550-697">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="15550-698">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="15550-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="15550-699">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="15550-700">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="15550-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="15550-701">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a><span data-ttu-id="15550-702">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="15550-702">Log event ID</span></span>

<span data-ttu-id="15550-703">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="15550-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="15550-704">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="15550-705">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="15550-705">An event ID associates a set of events.</span></span> <span data-ttu-id="15550-706">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="15550-707">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="15550-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="15550-708">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="15550-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="15550-709">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="15550-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="15550-710">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="15550-710">Log message template</span></span>

<span data-ttu-id="15550-711">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-711">Each log specifies a message template.</span></span> <span data-ttu-id="15550-712">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="15550-713">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="15550-714">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="15550-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="15550-715">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="15550-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="15550-716">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="15550-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="15550-717">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="15550-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="15550-718">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="15550-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="15550-719">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="15550-720">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="15550-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="15550-721">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="15550-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="15550-722">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="15550-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="15550-723">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="15550-723">Logging exceptions</span></span>

<span data-ttu-id="15550-724">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="15550-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="15550-725">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="15550-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="15550-726">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="15550-727">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="15550-727">Log filtering</span></span>

<span data-ttu-id="15550-728">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="15550-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="15550-729">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="15550-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="15550-730">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="15550-731">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="15550-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="15550-732">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="15550-732">Create filter rules in configuration</span></span>

<span data-ttu-id="15550-733">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="15550-734">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="15550-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="15550-735">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="15550-736">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="15550-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="15550-737">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="15550-738">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="15550-738">Filter rules in code</span></span>

<span data-ttu-id="15550-739">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="15550-740">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="15550-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="15550-741">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="15550-742">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="15550-742">How filtering rules are applied</span></span>

<span data-ttu-id="15550-743">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="15550-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="15550-744">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="15550-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="15550-745">数値</span><span class="sxs-lookup"><span data-stu-id="15550-745">Number</span></span> | <span data-ttu-id="15550-746">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-746">Provider</span></span>      | <span data-ttu-id="15550-747">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-747">Categories that begin with ...</span></span>          | <span data-ttu-id="15550-748">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="15550-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="15550-749">1</span><span class="sxs-lookup"><span data-stu-id="15550-749">1</span></span>      | <span data-ttu-id="15550-750">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-750">Debug</span></span>         | <span data-ttu-id="15550-751">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-751">All categories</span></span>                          | <span data-ttu-id="15550-752">情報</span><span class="sxs-lookup"><span data-stu-id="15550-752">Information</span></span>       |
| <span data-ttu-id="15550-753">2</span><span class="sxs-lookup"><span data-stu-id="15550-753">2</span></span>      | <span data-ttu-id="15550-754">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-754">Console</span></span>       | <span data-ttu-id="15550-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="15550-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="15550-756">警告</span><span class="sxs-lookup"><span data-stu-id="15550-756">Warning</span></span>           |
| <span data-ttu-id="15550-757">3</span><span class="sxs-lookup"><span data-stu-id="15550-757">3</span></span>      | <span data-ttu-id="15550-758">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-758">Console</span></span>       | <span data-ttu-id="15550-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="15550-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="15550-760">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-760">Debug</span></span>             |
| <span data-ttu-id="15550-761">4</span><span class="sxs-lookup"><span data-stu-id="15550-761">4</span></span>      | <span data-ttu-id="15550-762">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-762">Console</span></span>       | <span data-ttu-id="15550-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="15550-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="15550-764">Error</span><span class="sxs-lookup"><span data-stu-id="15550-764">Error</span></span>             |
| <span data-ttu-id="15550-765">5</span><span class="sxs-lookup"><span data-stu-id="15550-765">5</span></span>      | <span data-ttu-id="15550-766">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-766">Console</span></span>       | <span data-ttu-id="15550-767">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-767">All categories</span></span>                          | <span data-ttu-id="15550-768">情報</span><span class="sxs-lookup"><span data-stu-id="15550-768">Information</span></span>       |
| <span data-ttu-id="15550-769">6</span><span class="sxs-lookup"><span data-stu-id="15550-769">6</span></span>      | <span data-ttu-id="15550-770">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-770">All providers</span></span> | <span data-ttu-id="15550-771">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-771">All categories</span></span>                          | <span data-ttu-id="15550-772">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-772">Debug</span></span>             |
| <span data-ttu-id="15550-773">7</span><span class="sxs-lookup"><span data-stu-id="15550-773">7</span></span>      | <span data-ttu-id="15550-774">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-774">All providers</span></span> | <span data-ttu-id="15550-775">システム</span><span class="sxs-lookup"><span data-stu-id="15550-775">System</span></span>                                  | <span data-ttu-id="15550-776">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-776">Debug</span></span>             |
| <span data-ttu-id="15550-777">8</span><span class="sxs-lookup"><span data-stu-id="15550-777">8</span></span>      | <span data-ttu-id="15550-778">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-778">Debug</span></span>         | <span data-ttu-id="15550-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="15550-779">Microsoft</span></span>                               | <span data-ttu-id="15550-780">トレース</span><span class="sxs-lookup"><span data-stu-id="15550-780">Trace</span></span>             |

<span data-ttu-id="15550-781">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="15550-782">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="15550-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="15550-783">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="15550-784">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="15550-785">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="15550-786">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="15550-787">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="15550-788">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="15550-789">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="15550-790">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="15550-791">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="15550-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="15550-792">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="15550-793">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="15550-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="15550-794">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="15550-795">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="15550-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="15550-796">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="15550-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="15550-797">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="15550-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="15550-798">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="15550-798">Provider aliases</span></span>

<span data-ttu-id="15550-799">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="15550-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="15550-800">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="15550-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="15550-801">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-801">Console</span></span>
* <span data-ttu-id="15550-802">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-802">Debug</span></span>
* <span data-ttu-id="15550-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="15550-803">EventSource</span></span>
* <span data-ttu-id="15550-804">EventLog</span><span class="sxs-lookup"><span data-stu-id="15550-804">EventLog</span></span>
* <span data-ttu-id="15550-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="15550-805">TraceSource</span></span>
* <span data-ttu-id="15550-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="15550-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="15550-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="15550-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="15550-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="15550-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="15550-809">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="15550-809">Default minimum level</span></span>

<span data-ttu-id="15550-810">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="15550-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="15550-811">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="15550-812">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="15550-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="15550-813">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="15550-813">Filter functions</span></span>

<span data-ttu-id="15550-814">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="15550-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="15550-815">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="15550-816">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="15550-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="15550-817">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="15550-817">System categories and levels</span></span>

<span data-ttu-id="15550-818">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="15550-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="15550-819">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="15550-819">Category</span></span>                            | <span data-ttu-id="15550-820">メモ</span><span class="sxs-lookup"><span data-stu-id="15550-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="15550-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="15550-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="15550-822">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="15550-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="15550-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="15550-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="15550-824">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="15550-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="15550-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="15550-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="15550-826">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="15550-826">Hosts allowed.</span></span> |
| <span data-ttu-id="15550-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="15550-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="15550-828">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="15550-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="15550-829">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="15550-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="15550-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="15550-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="15550-831">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="15550-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="15550-832">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="15550-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="15550-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="15550-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="15550-834">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="15550-834">Route matching information.</span></span> |
| <span data-ttu-id="15550-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="15550-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="15550-836">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="15550-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="15550-837">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="15550-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="15550-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="15550-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="15550-839">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="15550-839">Files served.</span></span> |
| <span data-ttu-id="15550-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="15550-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="15550-841">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="15550-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="15550-842">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="15550-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="15550-843">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="15550-843">Log scopes</span></span>

 <span data-ttu-id="15550-844">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="15550-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="15550-845">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="15550-846">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="15550-847">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="15550-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="15550-848">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="15550-849">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="15550-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="15550-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="15550-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="15550-851">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="15550-852">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="15550-853">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="15550-854">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-854">Built-in logging providers</span></span>

<span data-ttu-id="15550-855">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="15550-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="15550-856">コンソール</span><span class="sxs-lookup"><span data-stu-id="15550-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="15550-857">デバッグ</span><span class="sxs-lookup"><span data-stu-id="15550-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="15550-858">EventSource</span><span class="sxs-lookup"><span data-stu-id="15550-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="15550-859">EventLog</span><span class="sxs-lookup"><span data-stu-id="15550-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="15550-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="15550-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="15550-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="15550-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="15550-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="15550-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="15550-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="15550-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="15550-864">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="15550-865">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-865">Console provider</span></span>

<span data-ttu-id="15550-866">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="15550-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="15550-867">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="15550-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="15550-868">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-868">Debug provider</span></span>

<span data-ttu-id="15550-869">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="15550-870">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="15550-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="15550-871">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-871">Event Source provider</span></span>

<span data-ttu-id="15550-872">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="15550-873">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="15550-874">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="15550-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="15550-875">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="15550-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="15550-876">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="15550-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="15550-877">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="15550-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="15550-878">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="15550-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="15550-880">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-880">Windows EventLog provider</span></span>

<span data-ttu-id="15550-881">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="15550-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="15550-882">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="15550-883">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="15550-884">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="15550-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="15550-885">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="15550-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="15550-886">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="15550-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="15550-887">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="15550-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="15550-888">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="15550-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="15550-889">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="15550-890">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-890">TraceSource provider</span></span>

<span data-ttu-id="15550-891">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="15550-892">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="15550-893">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15550-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="15550-894">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="15550-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="15550-895">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-895">Azure App Service provider</span></span>

<span data-ttu-id="15550-896">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="15550-897">プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。</span><span class="sxs-lookup"><span data-stu-id="15550-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="15550-898">.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="15550-899"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="15550-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="15550-900">設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。</span><span class="sxs-lookup"><span data-stu-id="15550-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="15550-901">("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)</span><span class="sxs-lookup"><span data-stu-id="15550-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="15550-902">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="15550-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="15550-903">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="15550-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="15550-904">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="15550-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="15550-905">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="15550-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="15550-906">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="15550-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="15550-907">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="15550-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="15550-908">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="15550-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="15550-909">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="15550-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="15550-910">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="15550-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="15550-911">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="15550-911">Azure log streaming</span></span>

<span data-ttu-id="15550-912">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="15550-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="15550-913">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="15550-913">The app server</span></span>
* <span data-ttu-id="15550-914">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="15550-914">The web server</span></span>
* <span data-ttu-id="15550-915">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="15550-915">Failed request tracing</span></span>

<span data-ttu-id="15550-916">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="15550-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="15550-917">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="15550-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="15550-918">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="15550-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="15550-919">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="15550-919">Choose the log **Level**.</span></span> <span data-ttu-id="15550-920">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="15550-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="15550-921">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="15550-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="15550-922">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="15550-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="15550-923">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="15550-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="15550-924">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="15550-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="15550-925">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="15550-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="15550-926">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="15550-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="15550-927">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="15550-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="15550-928">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="15550-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="15550-929">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="15550-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="15550-930">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="15550-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="15550-931">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="15550-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="15550-932">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="15550-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="15550-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="15550-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="15550-934">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="15550-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="15550-935">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="15550-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="15550-936">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="15550-936">Third-party logging providers</span></span>

<span data-ttu-id="15550-937">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="15550-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="15550-938">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="15550-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="15550-940">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="15550-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="15550-941">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="15550-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="15550-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="15550-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="15550-943">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-944">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="15550-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="15550-945">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="15550-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="15550-946">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="15550-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="15550-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="15550-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="15550-948">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="15550-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="15550-949">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="15550-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="15550-950">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="15550-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="15550-951">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="15550-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="15550-952">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="15550-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="15550-953">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15550-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15550-954">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="15550-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
