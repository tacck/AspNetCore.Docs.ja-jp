---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: b897d0d775da62a11f01a64f39b47b6c5abebc8b
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791559"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="1940b-103">.NET Core および ASP.NET Core でのログ記録</span><span class="sxs-lookup"><span data-stu-id="1940b-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1940b-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1940b-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1940b-105">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1940b-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1940b-106">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1940b-107">この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。</span><span class="sxs-lookup"><span data-stu-id="1940b-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="1940b-108">これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="1940b-109">非 Web コンソール アプリで汎用ホストを使用する方法の例については、[バックグラウンド タスクのサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) の *Program.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="1940b-110">汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="1940b-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="1940b-111">ホスト以外のコードの例については、記事のこれらのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="1940b-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1940b-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="1940b-113">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="1940b-113">Add providers</span></span>

<span data-ttu-id="1940b-114">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="1940b-115">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="1940b-116">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="1940b-117">汎用ホストを使用するアプリにプロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="1940b-118">ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="1940b-119">`LoggerFactory` および `AddConsole` には、`Microsoft.Extensions.Logging` 用に `using` ステートメントが必要です。</span><span class="sxs-lookup"><span data-stu-id="1940b-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="1940b-120">既定の ASP.NET Core プロジェクト テンプレートからは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="1940b-121">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="1940b-122">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1940b-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="1940b-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="1940b-124">[EventLog](#windows-eventlog-provider) (Windows 上で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="1940b-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="1940b-125">既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="1940b-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="1940b-127">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="1940b-128">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-128">Create logs</span></span>

<span data-ttu-id="1940b-129">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1940b-130">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="1940b-131">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1940b-132">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="1940b-133">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="1940b-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="1940b-134">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="1940b-135">次のホスト コンソール以外のアプリの例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="1940b-136">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1940b-137">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="1940b-138">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="1940b-139">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-139">Create logs in the Program class</span></span>

<span data-ttu-id="1940b-140">ASP.NET Core アプリの `Program` クラスでログを書き込むには、ホストをビルドした後に DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="1940b-141">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1940b-142">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-142">However, a separate logger can be used.</span></span> <span data-ttu-id="1940b-143">次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1940b-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="1940b-144">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="1940b-145">Startup クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-145">Create logs in the Startup class</span></span>

<span data-ttu-id="1940b-146">ASP.NET Core アプリの `Startup.Configure` メソッドでログを書き込むには、メソッド シグネチャに `ILogger` パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="1940b-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="1940b-147">`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="1940b-148">`Startup` コンストラクターへのロガーの挿入はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="1940b-149">`Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1940b-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="1940b-150">この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。</span><span class="sxs-lookup"><span data-stu-id="1940b-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="1940b-151">DI コンテナーは、`ConfigureServices` が完了するまで設定されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="1940b-152">ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。</span><span class="sxs-lookup"><span data-stu-id="1940b-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="1940b-153">汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="1940b-154">`ILogger<T>` に依存するサービスを構成する必要がある場合でも、コンストラクターの挿入を使用するか、ファクトリ メソッドを用意して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="1940b-155">ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1940b-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="1940b-156">たとえば、DI のサービスを使用してプロパティを設定する必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="1940b-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="1940b-157">前の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスが初めて作成されるときに実行される `Func` です。</span><span class="sxs-lookup"><span data-stu-id="1940b-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="1940b-158">この方法では、任意の登録済みサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="1940b-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor-webassembly"></a><span data-ttu-id="1940b-159">Blazor WebAssembly ログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-159">Create logs in Blazor WebAssembly</span></span>

<span data-ttu-id="1940b-160">`Program.Main` の `WebAssemblyHostBuilder.Logging` プロパティを使用して、Blazor WebAssembly でのログ記録を構成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-160">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="1940b-161">`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="1940b-161">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1940b-162">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="1940b-162">No asynchronous logger methods</span></span>

<span data-ttu-id="1940b-163">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-163">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1940b-164">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-164">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1940b-165">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-165">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="1940b-166">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="1940b-166">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1940b-167">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="1940b-167">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1940b-168">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-168">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="1940b-169">構成</span><span class="sxs-lookup"><span data-stu-id="1940b-169">Configuration</span></span>

<span data-ttu-id="1940b-170">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-170">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="1940b-171">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="1940b-171">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="1940b-172">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="1940b-172">Command-line arguments.</span></span>
* <span data-ttu-id="1940b-173">環境変数。</span><span class="sxs-lookup"><span data-stu-id="1940b-173">Environment variables.</span></span>
* <span data-ttu-id="1940b-174">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="1940b-174">In-memory .NET objects.</span></span>
* <span data-ttu-id="1940b-175">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="1940b-175">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="1940b-176">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="1940b-176">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="1940b-177">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="1940b-177">Custom providers (installed or created).</span></span>

<span data-ttu-id="1940b-178">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-178">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="1940b-179">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="1940b-179">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="1940b-180">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-180">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="1940b-181">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-181">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1940b-182">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-182">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="1940b-183">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-183">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="1940b-184">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="1940b-184">The example is for the Console provider.</span></span> <span data-ttu-id="1940b-185">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-185">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1940b-186">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-186">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="1940b-187">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-187">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="1940b-188">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="1940b-188">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="1940b-189">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-189">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1940b-190">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="1940b-190">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1940b-191">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="1940b-191">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="1940b-192">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-192">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="1940b-193">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-193">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="1940b-194">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="1940b-194">Sample logging output</span></span>

<span data-ttu-id="1940b-195">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-195">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="1940b-196">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1940b-196">Here's an example of console output:</span></span>

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

<span data-ttu-id="1940b-197">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="1940b-197">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="1940b-198">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-198">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="1940b-199">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApiSample" から始まります。</span><span class="sxs-lookup"><span data-stu-id="1940b-199">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="1940b-200">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="1940b-200">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1940b-201">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1940b-201">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="1940b-202">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-202">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="1940b-203">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="1940b-203">NuGet packages</span></span>

<span data-ttu-id="1940b-204">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="1940b-204">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="1940b-205">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-205">Log category</span></span>

<span data-ttu-id="1940b-206">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-206">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="1940b-207">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="1940b-207">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1940b-208">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="1940b-208">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="1940b-209">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-209">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="1940b-210">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-210">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="1940b-211">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="1940b-211">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="1940b-212">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-212">Log level</span></span>

<span data-ttu-id="1940b-213">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-213">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="1940b-214">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-214">The log level indicates the severity or importance.</span></span> <span data-ttu-id="1940b-215">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-215">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="1940b-216">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-216">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1940b-217">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="1940b-217">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="1940b-218">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="1940b-218">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1940b-219">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-219">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="1940b-220">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="1940b-220">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="1940b-221">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-221">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="1940b-222">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="1940b-222">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="1940b-223">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-223">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="1940b-224">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="1940b-224">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="1940b-225">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="1940b-225">Trace = 0</span></span>

  <span data-ttu-id="1940b-226">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-226">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="1940b-227">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1940b-227">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="1940b-228">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="1940b-228">*Disabled by default.*</span></span>

* <span data-ttu-id="1940b-229">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="1940b-229">Debug = 1</span></span>

  <span data-ttu-id="1940b-230">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-230">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="1940b-231">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="1940b-231">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="1940b-232">Information = 2</span><span class="sxs-lookup"><span data-stu-id="1940b-232">Information = 2</span></span>

  <span data-ttu-id="1940b-233">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-233">For tracking the general flow of the app.</span></span> <span data-ttu-id="1940b-234">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-234">These logs typically have some long-term value.</span></span> <span data-ttu-id="1940b-235">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="1940b-235">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="1940b-236">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="1940b-236">Warning = 3</span></span>

  <span data-ttu-id="1940b-237">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-237">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="1940b-238">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-238">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="1940b-239">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-239">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="1940b-240">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="1940b-240">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="1940b-241">Error = 4</span><span class="sxs-lookup"><span data-stu-id="1940b-241">Error = 4</span></span>

  <span data-ttu-id="1940b-242">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-242">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1940b-243">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-243">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="1940b-244">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="1940b-244">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="1940b-245">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="1940b-245">Critical = 5</span></span>

  <span data-ttu-id="1940b-246">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-246">For failures that require immediate attention.</span></span> <span data-ttu-id="1940b-247">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="1940b-247">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="1940b-248">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="1940b-248">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="1940b-249">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-249">For example:</span></span>

* <span data-ttu-id="1940b-250">運用環境:</span><span class="sxs-lookup"><span data-stu-id="1940b-250">In production:</span></span>
  * <span data-ttu-id="1940b-251">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-251">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1940b-252">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-252">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="1940b-253">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-253">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="1940b-254">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="1940b-254">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="1940b-255">開発中:</span><span class="sxs-lookup"><span data-stu-id="1940b-255">During development:</span></span>
  * <span data-ttu-id="1940b-256">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-256">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="1940b-257">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-257">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="1940b-258">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-258">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="1940b-259">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-259">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1940b-260">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="1940b-260">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="1940b-261">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-261">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="1940b-262">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="1940b-262">Log event ID</span></span>

<span data-ttu-id="1940b-263">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-263">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1940b-264">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-264">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="1940b-265">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1940b-265">An event ID associates a set of events.</span></span> <span data-ttu-id="1940b-266">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-266">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1940b-267">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-267">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1940b-268">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-268">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1940b-269">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-269">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="1940b-270">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="1940b-270">Log message template</span></span>

<span data-ttu-id="1940b-271">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-271">Each log specifies a message template.</span></span> <span data-ttu-id="1940b-272">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-272">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1940b-273">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-273">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1940b-274">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="1940b-274">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1940b-275">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-275">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1940b-276">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-276">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="1940b-277">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="1940b-277">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="1940b-278">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-278">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1940b-279">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-279">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1940b-280">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1940b-280">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1940b-281">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-281">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="1940b-282">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-282">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="1940b-283">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="1940b-283">Logging exceptions</span></span>

<span data-ttu-id="1940b-284">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="1940b-284">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="1940b-285">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="1940b-285">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="1940b-286">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-286">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="1940b-287">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="1940b-287">Log filtering</span></span>

<span data-ttu-id="1940b-288">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-288">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="1940b-289">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-289">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="1940b-290">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-290">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="1940b-291">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="1940b-291">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="1940b-292">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-292">Create filter rules in configuration</span></span>

<span data-ttu-id="1940b-293">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-293">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="1940b-294">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-294">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="1940b-295">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-295">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="1940b-296">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="1940b-296">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="1940b-297">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-297">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="1940b-298">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="1940b-298">Filter rules in code</span></span>

<span data-ttu-id="1940b-299">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-299">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="1940b-300">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-300">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="1940b-301">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-301">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1940b-302">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="1940b-302">How filtering rules are applied</span></span>

<span data-ttu-id="1940b-303">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-303">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="1940b-304">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="1940b-304">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="1940b-305">数値</span><span class="sxs-lookup"><span data-stu-id="1940b-305">Number</span></span> | <span data-ttu-id="1940b-306">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-306">Provider</span></span>      | <span data-ttu-id="1940b-307">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-307">Categories that begin with ...</span></span>          | <span data-ttu-id="1940b-308">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-308">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="1940b-309">1</span><span class="sxs-lookup"><span data-stu-id="1940b-309">1</span></span>      | <span data-ttu-id="1940b-310">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-310">Debug</span></span>         | <span data-ttu-id="1940b-311">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-311">All categories</span></span>                          | <span data-ttu-id="1940b-312">情報</span><span class="sxs-lookup"><span data-stu-id="1940b-312">Information</span></span>       |
| <span data-ttu-id="1940b-313">2</span><span class="sxs-lookup"><span data-stu-id="1940b-313">2</span></span>      | <span data-ttu-id="1940b-314">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-314">Console</span></span>       | <span data-ttu-id="1940b-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="1940b-315">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="1940b-316">警告</span><span class="sxs-lookup"><span data-stu-id="1940b-316">Warning</span></span>           |
| <span data-ttu-id="1940b-317">3</span><span class="sxs-lookup"><span data-stu-id="1940b-317">3</span></span>      | <span data-ttu-id="1940b-318">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-318">Console</span></span>       | <span data-ttu-id="1940b-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="1940b-319">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="1940b-320">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-320">Debug</span></span>             |
| <span data-ttu-id="1940b-321">4</span><span class="sxs-lookup"><span data-stu-id="1940b-321">4</span></span>      | <span data-ttu-id="1940b-322">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-322">Console</span></span>       | <span data-ttu-id="1940b-323">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="1940b-323">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="1940b-324">Error</span><span class="sxs-lookup"><span data-stu-id="1940b-324">Error</span></span>             |
| <span data-ttu-id="1940b-325">5</span><span class="sxs-lookup"><span data-stu-id="1940b-325">5</span></span>      | <span data-ttu-id="1940b-326">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-326">Console</span></span>       | <span data-ttu-id="1940b-327">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-327">All categories</span></span>                          | <span data-ttu-id="1940b-328">情報</span><span class="sxs-lookup"><span data-stu-id="1940b-328">Information</span></span>       |
| <span data-ttu-id="1940b-329">6</span><span class="sxs-lookup"><span data-stu-id="1940b-329">6</span></span>      | <span data-ttu-id="1940b-330">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-330">All providers</span></span> | <span data-ttu-id="1940b-331">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-331">All categories</span></span>                          | <span data-ttu-id="1940b-332">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-332">Debug</span></span>             |
| <span data-ttu-id="1940b-333">7</span><span class="sxs-lookup"><span data-stu-id="1940b-333">7</span></span>      | <span data-ttu-id="1940b-334">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-334">All providers</span></span> | <span data-ttu-id="1940b-335">システム</span><span class="sxs-lookup"><span data-stu-id="1940b-335">System</span></span>                                  | <span data-ttu-id="1940b-336">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-336">Debug</span></span>             |
| <span data-ttu-id="1940b-337">8</span><span class="sxs-lookup"><span data-stu-id="1940b-337">8</span></span>      | <span data-ttu-id="1940b-338">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-338">Debug</span></span>         | <span data-ttu-id="1940b-339">Microsoft</span><span class="sxs-lookup"><span data-stu-id="1940b-339">Microsoft</span></span>                               | <span data-ttu-id="1940b-340">トレース</span><span class="sxs-lookup"><span data-stu-id="1940b-340">Trace</span></span>             |

<span data-ttu-id="1940b-341">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-341">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1940b-342">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-342">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1940b-343">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-343">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1940b-344">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-344">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1940b-345">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-345">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1940b-346">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-346">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1940b-347">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-347">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1940b-348">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-348">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1940b-349">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-349">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1940b-350">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-350">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="1940b-351">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="1940b-351">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="1940b-352">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-352">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="1940b-353">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-353">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="1940b-354">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-354">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="1940b-355">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="1940b-355">Rule 3 is most specific.</span></span>

<span data-ttu-id="1940b-356">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-356">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="1940b-357">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-357">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="1940b-358">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="1940b-358">Provider aliases</span></span>

<span data-ttu-id="1940b-359">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-359">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="1940b-360">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-360">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="1940b-361">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-361">Console</span></span>
* <span data-ttu-id="1940b-362">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-362">Debug</span></span>
* <span data-ttu-id="1940b-363">EventSource</span><span class="sxs-lookup"><span data-stu-id="1940b-363">EventSource</span></span>
* <span data-ttu-id="1940b-364">EventLog</span><span class="sxs-lookup"><span data-stu-id="1940b-364">EventLog</span></span>
* <span data-ttu-id="1940b-365">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1940b-365">TraceSource</span></span>
* <span data-ttu-id="1940b-366">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1940b-366">AzureAppServicesFile</span></span>
* <span data-ttu-id="1940b-367">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1940b-367">AzureAppServicesBlob</span></span>
* <span data-ttu-id="1940b-368">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1940b-368">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="1940b-369">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-369">Default minimum level</span></span>

<span data-ttu-id="1940b-370">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-370">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="1940b-371">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-371">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="1940b-372">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1940b-372">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="1940b-373">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="1940b-373">Filter functions</span></span>

<span data-ttu-id="1940b-374">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-374">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="1940b-375">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-375">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="1940b-376">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-376">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="1940b-377">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="1940b-377">System categories and levels</span></span>

<span data-ttu-id="1940b-378">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-378">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="1940b-379">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-379">Category</span></span>                            | <span data-ttu-id="1940b-380">メモ</span><span class="sxs-lookup"><span data-stu-id="1940b-380">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1940b-381">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1940b-381">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1940b-382">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-382">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1940b-383">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1940b-383">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1940b-384">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="1940b-384">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1940b-385">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1940b-385">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1940b-386">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="1940b-386">Hosts allowed.</span></span> |
| <span data-ttu-id="1940b-387">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1940b-387">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1940b-388">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="1940b-388">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1940b-389">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="1940b-389">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1940b-390">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1940b-390">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1940b-391">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-391">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1940b-392">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="1940b-392">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1940b-393">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1940b-393">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1940b-394">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="1940b-394">Route matching information.</span></span> |
| <span data-ttu-id="1940b-395">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1940b-395">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1940b-396">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="1940b-396">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1940b-397">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="1940b-397">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1940b-398">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1940b-398">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1940b-399">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="1940b-399">Files served.</span></span> |
| <span data-ttu-id="1940b-400">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1940b-400">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1940b-401">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-401">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1940b-402">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="1940b-402">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="1940b-403">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="1940b-403">Log scopes</span></span>

 <span data-ttu-id="1940b-404">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-404">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1940b-405">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-405">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1940b-406">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-406">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1940b-407">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="1940b-407">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="1940b-408">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-408">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="1940b-409">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="1940b-409">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="1940b-410">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1940b-410">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="1940b-411">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-411">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="1940b-412">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-412">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="1940b-413">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-413">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="1940b-414">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-414">Built-in logging providers</span></span>

<span data-ttu-id="1940b-415">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="1940b-415">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="1940b-416">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-416">Console</span></span>](#console-provider)
* [<span data-ttu-id="1940b-417">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-417">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1940b-418">EventSource</span><span class="sxs-lookup"><span data-stu-id="1940b-418">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="1940b-419">EventLog</span><span class="sxs-lookup"><span data-stu-id="1940b-419">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="1940b-420">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1940b-420">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="1940b-421">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1940b-421">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1940b-422">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1940b-422">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1940b-423">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1940b-423">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="1940b-424">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-424">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="1940b-425">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-425">Console provider</span></span>

<span data-ttu-id="1940b-426">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="1940b-426">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="1940b-427">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1940b-427">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="1940b-428">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-428">Debug provider</span></span>

<span data-ttu-id="1940b-429">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-429">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="1940b-430">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-430">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="1940b-431">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-431">Event Source provider</span></span>

<span data-ttu-id="1940b-432">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-432">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1940b-433">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-433">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="1940b-434">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-434">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="1940b-435">dotnet trace ツール</span><span class="sxs-lookup"><span data-stu-id="1940b-435">dotnet trace tooling</span></span>

<span data-ttu-id="1940b-436">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="1940b-436">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="1940b-437">このツールでは、<xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource> プロバイダー データを収集します。</span><span class="sxs-lookup"><span data-stu-id="1940b-437">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="1940b-438">次のコマンドを使用して、dotnet trace ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1940b-438">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="1940b-439">dotnet trace ツールを使用して、アプリからトレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="1940b-439">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="1940b-440">アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-440">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="1940b-441">`dotnet run` コマンドを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="1940b-441">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="1940b-442">.NET Core アプリのプロセス識別子 (PID) を決定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-442">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="1940b-443">Windows では、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-443">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="1940b-444">タスク マネージャー (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="1940b-444">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="1940b-445">tasklist コマンド</span><span class="sxs-lookup"><span data-stu-id="1940b-445">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="1940b-446">Get-Process PowerShell コマンド</span><span class="sxs-lookup"><span data-stu-id="1940b-446">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="1940b-447">Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-447">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="1940b-448">アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。</span><span class="sxs-lookup"><span data-stu-id="1940b-448">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="1940b-449">`dotnet trace` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1940b-449">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="1940b-450">一般的なコマンド構文</span><span class="sxs-lookup"><span data-stu-id="1940b-450">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="1940b-451">PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-451">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="1940b-452">Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。</span><span class="sxs-lookup"><span data-stu-id="1940b-452">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="1940b-453">キーワード</span><span class="sxs-lookup"><span data-stu-id="1940b-453">Keyword</span></span> | <span data-ttu-id="1940b-454">説明</span><span class="sxs-lookup"><span data-stu-id="1940b-454">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="1940b-455">1</span><span class="sxs-lookup"><span data-stu-id="1940b-455">1</span></span>       | <span data-ttu-id="1940b-456">`LoggingEventSource` に関するメタ イベントをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-456">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="1940b-457">`ILogger` からのイベントは記録されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-457">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="1940b-458">2</span><span class="sxs-lookup"><span data-stu-id="1940b-458">2</span></span>       | <span data-ttu-id="1940b-459">`ILogger.Log()` が呼び出されたときに、`Message` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="1940b-459">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1940b-460">プログラムで (書式設定されずに) 情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-460">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="1940b-461">4</span><span class="sxs-lookup"><span data-stu-id="1940b-461">4</span></span>       | <span data-ttu-id="1940b-462">`ILogger.Log()` が呼び出されたときに、`FormatMessage` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="1940b-462">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1940b-463">書式設定された文字列バージョンの情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-463">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="1940b-464">8</span><span class="sxs-lookup"><span data-stu-id="1940b-464">8</span></span>       | <span data-ttu-id="1940b-465">`ILogger.Log()` が呼び出されたときに、`MessageJson` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="1940b-465">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="1940b-466">引数の JSON 表現が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-466">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="1940b-467">イベント レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-467">Event Level</span></span> | <span data-ttu-id="1940b-468">説明</span><span class="sxs-lookup"><span data-stu-id="1940b-468">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="1940b-469">0</span><span class="sxs-lookup"><span data-stu-id="1940b-469">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="1940b-470">1</span><span class="sxs-lookup"><span data-stu-id="1940b-470">1</span></span>           | `Critical`      |
   | <span data-ttu-id="1940b-471">2</span><span class="sxs-lookup"><span data-stu-id="1940b-471">2</span></span>           | `Error`         |
   | <span data-ttu-id="1940b-472">3</span><span class="sxs-lookup"><span data-stu-id="1940b-472">3</span></span>           | `Warning`       |
   | <span data-ttu-id="1940b-473">4</span><span class="sxs-lookup"><span data-stu-id="1940b-473">4</span></span>           | `Informational` |
   | <span data-ttu-id="1940b-474">5</span><span class="sxs-lookup"><span data-stu-id="1940b-474">5</span></span>           | `Verbose`       |

   <span data-ttu-id="1940b-475">`{Logger Category}` と `{Event Level}` の `FilterSpecs` エントリは、追加のログ フィルター条件を表します。</span><span class="sxs-lookup"><span data-stu-id="1940b-475">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="1940b-476">セミコロン (`;`) で `FilterSpecs` エントリを区切ります。</span><span class="sxs-lookup"><span data-stu-id="1940b-476">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="1940b-477">Windows コマンド シェルを使用した例 (`--providers` 値を囲む単一引用符**なし**):</span><span class="sxs-lookup"><span data-stu-id="1940b-477">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="1940b-478">上記のコマンドにより次のことがアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="1940b-478">The preceding command activates:</span></span>

   * <span data-ttu-id="1940b-479">エラー (`2`) に対して書式設定された文字列 (`4`) を生成するイベント ソース ロガー。</span><span class="sxs-lookup"><span data-stu-id="1940b-479">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="1940b-480">`Informational` ログ レベル (`4`) での `Microsoft.AspNetCore.Hosting` のログ記録。</span><span class="sxs-lookup"><span data-stu-id="1940b-480">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="1940b-481">Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。</span><span class="sxs-lookup"><span data-stu-id="1940b-481">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="1940b-482">トレースは、`dotnet trace` コマンドが実行されたフォルダーに *trace.nettrace* という名前で保存されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-482">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="1940b-483">[Perfview](#perfview) を使用してトレースを開きます。</span><span class="sxs-lookup"><span data-stu-id="1940b-483">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="1940b-484">*trace.nettrace* ファイルを開き、トレース イベントを調べます。</span><span class="sxs-lookup"><span data-stu-id="1940b-484">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="1940b-485">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="1940b-485">For more information, see:</span></span>

* <span data-ttu-id="1940b-486">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="1940b-486">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="1940b-487">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="1940b-487">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="1940b-488">[LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="1940b-488">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="1940b-489">[LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="1940b-489">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="1940b-490">[Perfview](#perfview) &ndash; イベント ソース トレースの表示に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1940b-490">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="1940b-491">Perfview</span><span class="sxs-lookup"><span data-stu-id="1940b-491">Perfview</span></span>

<span data-ttu-id="1940b-492">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-492">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1940b-493">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1940b-493">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1940b-494">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="1940b-494">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1940b-495">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="1940b-495">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="1940b-497">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-497">Windows EventLog provider</span></span>

<span data-ttu-id="1940b-498">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="1940b-498">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="1940b-499">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-499">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1940b-500">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-500">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1940b-501">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="1940b-501">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="1940b-502">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="1940b-502">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="1940b-503">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="1940b-503">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="1940b-504">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-504">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="1940b-505">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-505">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="1940b-506">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-506">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="1940b-507">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-507">TraceSource provider</span></span>

<span data-ttu-id="1940b-508">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-508">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="1940b-509">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-509">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="1940b-510">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-510">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="1940b-511">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-511">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="1940b-512">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-512">Azure App Service provider</span></span>

<span data-ttu-id="1940b-513">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-513">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="1940b-514">プロバイダー パッケージは、共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-514">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="1940b-515">プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-515">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="1940b-516">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-516">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="1940b-517">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-517">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1940b-518">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="1940b-518">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1940b-519">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="1940b-519">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1940b-520">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="1940b-520">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1940b-521">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="1940b-521">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1940b-522">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="1940b-522">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1940b-523">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="1940b-523">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1940b-524">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1940b-524">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="1940b-525">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="1940b-525">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1940b-526">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="1940b-526">Azure log streaming</span></span>

<span data-ttu-id="1940b-527">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-527">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="1940b-528">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="1940b-528">The app server</span></span>
* <span data-ttu-id="1940b-529">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="1940b-529">The web server</span></span>
* <span data-ttu-id="1940b-530">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="1940b-530">Failed request tracing</span></span>

<span data-ttu-id="1940b-531">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="1940b-531">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1940b-532">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="1940b-532">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="1940b-533">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-533">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1940b-534">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1940b-534">Choose the log **Level**.</span></span> <span data-ttu-id="1940b-535">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-535">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="1940b-536">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="1940b-536">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="1940b-537">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-537">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="1940b-538">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="1940b-538">Azure Application Insights trace logging</span></span>

<span data-ttu-id="1940b-539">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-539">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="1940b-540">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="1940b-540">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1940b-541">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-541">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1940b-542">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="1940b-542">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1940b-543">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-543">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1940b-544">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1940b-544">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="1940b-545">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-545">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1940b-546">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="1940b-546">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1940b-547">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="1940b-547">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1940b-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="1940b-548">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1940b-549">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="1940b-549">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1940b-550">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="1940b-550">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1940b-551">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-551">Third-party logging providers</span></span>

<span data-ttu-id="1940b-552">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="1940b-552">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1940b-553">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-553">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-554">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1940b-555">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="1940b-555">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1940b-556">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1940b-556">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1940b-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1940b-557">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1940b-558">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-558">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-559">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-559">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-560">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1940b-560">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1940b-561">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="1940b-561">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1940b-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1940b-562">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1940b-563">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-563">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1940b-564">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="1940b-564">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1940b-565">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-565">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1940b-566">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-566">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1940b-567">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-567">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1940b-568">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-568">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1940b-569">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1940b-569">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1940b-570">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1940b-570">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1940b-571">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1940b-571">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1940b-572">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-572">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="1940b-573">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="1940b-573">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="1940b-574">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="1940b-574">Add providers</span></span>

<span data-ttu-id="1940b-575">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-575">A logging provider displays or stores logs.</span></span> <span data-ttu-id="1940b-576">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-576">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="1940b-577">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-577">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="1940b-578">プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-578">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="1940b-579">上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="1940b-579">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="1940b-580">既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-580">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="1940b-581">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-581">Console</span></span>
* <span data-ttu-id="1940b-582">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-582">Debug</span></span>
* <span data-ttu-id="1940b-583">EventSource (ASP.NET Core 2.2 以降)</span><span class="sxs-lookup"><span data-stu-id="1940b-583">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="1940b-584">`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-584">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="1940b-585"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-585">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="1940b-586">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-586">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="1940b-587">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-587">Create logs</span></span>

<span data-ttu-id="1940b-588">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-588">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="1940b-589">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-589">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="1940b-590">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-590">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="1940b-591">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-591">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="1940b-592">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="1940b-592">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="1940b-593">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-593">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="1940b-594">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-594">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="1940b-595">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-595">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="1940b-596">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-596">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="1940b-597">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-597">Create logs in Startup</span></span>

<span data-ttu-id="1940b-598">`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-598">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="1940b-599">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-599">Create logs in the Program class</span></span>

<span data-ttu-id="1940b-600">`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-600">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="1940b-601">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-601">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="1940b-602">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-602">However, a separate logger can be used.</span></span> <span data-ttu-id="1940b-603">次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1940b-603">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="1940b-604">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-604">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="1940b-605">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="1940b-605">No asynchronous logger methods</span></span>

<span data-ttu-id="1940b-606">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-606">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="1940b-607">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-607">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="1940b-608">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-608">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="1940b-609">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="1940b-609">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="1940b-610">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="1940b-610">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="1940b-611">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-611">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="1940b-612">構成</span><span class="sxs-lookup"><span data-stu-id="1940b-612">Configuration</span></span>

<span data-ttu-id="1940b-613">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-613">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="1940b-614">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="1940b-614">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="1940b-615">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="1940b-615">Command-line arguments.</span></span>
* <span data-ttu-id="1940b-616">環境変数。</span><span class="sxs-lookup"><span data-stu-id="1940b-616">Environment variables.</span></span>
* <span data-ttu-id="1940b-617">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="1940b-617">In-memory .NET objects.</span></span>
* <span data-ttu-id="1940b-618">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="1940b-618">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="1940b-619">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="1940b-619">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="1940b-620">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="1940b-620">Custom providers (installed or created).</span></span>

<span data-ttu-id="1940b-621">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-621">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="1940b-622">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="1940b-622">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="1940b-623">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-623">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="1940b-624">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-624">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="1940b-625">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-625">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="1940b-626">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-626">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="1940b-627">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="1940b-627">The example is for the Console provider.</span></span> <span data-ttu-id="1940b-628">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-628">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="1940b-629">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-629">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="1940b-630">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-630">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="1940b-631">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="1940b-631">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="1940b-632">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-632">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="1940b-633">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="1940b-633">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="1940b-634">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="1940b-634">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="1940b-635">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-635">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="1940b-636">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-636">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="1940b-637">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="1940b-637">Sample logging output</span></span>

<span data-ttu-id="1940b-638">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-638">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="1940b-639">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1940b-639">Here's an example of console output:</span></span>

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

<span data-ttu-id="1940b-640">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="1940b-640">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="1940b-641">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-641">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="1940b-642">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。</span><span class="sxs-lookup"><span data-stu-id="1940b-642">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="1940b-643">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="1940b-643">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="1940b-644">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="1940b-644">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="1940b-645">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-645">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="1940b-646">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="1940b-646">NuGet packages</span></span>

<span data-ttu-id="1940b-647">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="1940b-647">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="1940b-648">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-648">Log category</span></span>

<span data-ttu-id="1940b-649">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-649">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="1940b-650">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="1940b-650">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="1940b-651">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="1940b-651">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="1940b-652">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="1940b-652">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="1940b-653">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-653">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="1940b-654">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="1940b-654">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="1940b-655">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-655">Log level</span></span>

<span data-ttu-id="1940b-656">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-656">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="1940b-657">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-657">The log level indicates the severity or importance.</span></span> <span data-ttu-id="1940b-658">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-658">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="1940b-659">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-659">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1940b-660">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="1940b-660">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="1940b-661">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="1940b-661">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="1940b-662">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-662">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="1940b-663">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="1940b-663">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="1940b-664">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-664">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="1940b-665">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="1940b-665">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="1940b-666">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-666">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="1940b-667">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="1940b-667">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="1940b-668">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="1940b-668">Trace = 0</span></span>

  <span data-ttu-id="1940b-669">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-669">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="1940b-670">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1940b-670">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="1940b-671">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="1940b-671">*Disabled by default.*</span></span>

* <span data-ttu-id="1940b-672">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="1940b-672">Debug = 1</span></span>

  <span data-ttu-id="1940b-673">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-673">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="1940b-674">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="1940b-674">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="1940b-675">Information = 2</span><span class="sxs-lookup"><span data-stu-id="1940b-675">Information = 2</span></span>

  <span data-ttu-id="1940b-676">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-676">For tracking the general flow of the app.</span></span> <span data-ttu-id="1940b-677">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-677">These logs typically have some long-term value.</span></span> <span data-ttu-id="1940b-678">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="1940b-678">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="1940b-679">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="1940b-679">Warning = 3</span></span>

  <span data-ttu-id="1940b-680">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-680">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="1940b-681">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-681">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="1940b-682">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-682">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="1940b-683">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="1940b-683">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="1940b-684">Error = 4</span><span class="sxs-lookup"><span data-stu-id="1940b-684">Error = 4</span></span>

  <span data-ttu-id="1940b-685">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-685">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="1940b-686">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-686">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="1940b-687">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="1940b-687">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="1940b-688">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="1940b-688">Critical = 5</span></span>

  <span data-ttu-id="1940b-689">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="1940b-689">For failures that require immediate attention.</span></span> <span data-ttu-id="1940b-690">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="1940b-690">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="1940b-691">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="1940b-691">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="1940b-692">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-692">For example:</span></span>

* <span data-ttu-id="1940b-693">運用環境:</span><span class="sxs-lookup"><span data-stu-id="1940b-693">In production:</span></span>
  * <span data-ttu-id="1940b-694">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-694">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="1940b-695">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-695">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="1940b-696">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-696">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="1940b-697">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="1940b-697">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="1940b-698">開発中:</span><span class="sxs-lookup"><span data-stu-id="1940b-698">During development:</span></span>
  * <span data-ttu-id="1940b-699">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="1940b-699">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="1940b-700">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-700">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="1940b-701">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1940b-701">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="1940b-702">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-702">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="1940b-703">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="1940b-703">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="1940b-704">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-704">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="1940b-705">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="1940b-705">Log event ID</span></span>

<span data-ttu-id="1940b-706">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-706">Each log can specify an *event ID*.</span></span> <span data-ttu-id="1940b-707">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-707">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="1940b-708">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="1940b-708">An event ID associates a set of events.</span></span> <span data-ttu-id="1940b-709">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-709">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="1940b-710">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-710">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="1940b-711">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-711">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="1940b-712">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-712">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="1940b-713">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="1940b-713">Log message template</span></span>

<span data-ttu-id="1940b-714">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-714">Each log specifies a message template.</span></span> <span data-ttu-id="1940b-715">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-715">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="1940b-716">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-716">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="1940b-717">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="1940b-717">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="1940b-718">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-718">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="1940b-719">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-719">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="1940b-720">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="1940b-720">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="1940b-721">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-721">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="1940b-722">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-722">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="1940b-723">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="1940b-723">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="1940b-724">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-724">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="1940b-725">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-725">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="1940b-726">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="1940b-726">Logging exceptions</span></span>

<span data-ttu-id="1940b-727">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="1940b-727">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="1940b-728">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="1940b-728">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="1940b-729">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-729">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="1940b-730">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="1940b-730">Log filtering</span></span>

<span data-ttu-id="1940b-731">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-731">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="1940b-732">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-732">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="1940b-733">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-733">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="1940b-734">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="1940b-734">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="1940b-735">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="1940b-735">Create filter rules in configuration</span></span>

<span data-ttu-id="1940b-736">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-736">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="1940b-737">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-737">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="1940b-738">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-738">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="1940b-739">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="1940b-739">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="1940b-740">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-740">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="1940b-741">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="1940b-741">Filter rules in code</span></span>

<span data-ttu-id="1940b-742">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-742">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="1940b-743">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-743">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="1940b-744">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-744">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="1940b-745">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="1940b-745">How filtering rules are applied</span></span>

<span data-ttu-id="1940b-746">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="1940b-746">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="1940b-747">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="1940b-747">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="1940b-748">数値</span><span class="sxs-lookup"><span data-stu-id="1940b-748">Number</span></span> | <span data-ttu-id="1940b-749">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-749">Provider</span></span>      | <span data-ttu-id="1940b-750">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-750">Categories that begin with ...</span></span>          | <span data-ttu-id="1940b-751">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-751">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="1940b-752">1</span><span class="sxs-lookup"><span data-stu-id="1940b-752">1</span></span>      | <span data-ttu-id="1940b-753">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-753">Debug</span></span>         | <span data-ttu-id="1940b-754">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-754">All categories</span></span>                          | <span data-ttu-id="1940b-755">情報</span><span class="sxs-lookup"><span data-stu-id="1940b-755">Information</span></span>       |
| <span data-ttu-id="1940b-756">2</span><span class="sxs-lookup"><span data-stu-id="1940b-756">2</span></span>      | <span data-ttu-id="1940b-757">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-757">Console</span></span>       | <span data-ttu-id="1940b-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="1940b-758">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="1940b-759">警告</span><span class="sxs-lookup"><span data-stu-id="1940b-759">Warning</span></span>           |
| <span data-ttu-id="1940b-760">3</span><span class="sxs-lookup"><span data-stu-id="1940b-760">3</span></span>      | <span data-ttu-id="1940b-761">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-761">Console</span></span>       | <span data-ttu-id="1940b-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="1940b-762">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="1940b-763">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-763">Debug</span></span>             |
| <span data-ttu-id="1940b-764">4</span><span class="sxs-lookup"><span data-stu-id="1940b-764">4</span></span>      | <span data-ttu-id="1940b-765">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-765">Console</span></span>       | <span data-ttu-id="1940b-766">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="1940b-766">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="1940b-767">Error</span><span class="sxs-lookup"><span data-stu-id="1940b-767">Error</span></span>             |
| <span data-ttu-id="1940b-768">5</span><span class="sxs-lookup"><span data-stu-id="1940b-768">5</span></span>      | <span data-ttu-id="1940b-769">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-769">Console</span></span>       | <span data-ttu-id="1940b-770">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-770">All categories</span></span>                          | <span data-ttu-id="1940b-771">情報</span><span class="sxs-lookup"><span data-stu-id="1940b-771">Information</span></span>       |
| <span data-ttu-id="1940b-772">6</span><span class="sxs-lookup"><span data-stu-id="1940b-772">6</span></span>      | <span data-ttu-id="1940b-773">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-773">All providers</span></span> | <span data-ttu-id="1940b-774">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-774">All categories</span></span>                          | <span data-ttu-id="1940b-775">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-775">Debug</span></span>             |
| <span data-ttu-id="1940b-776">7</span><span class="sxs-lookup"><span data-stu-id="1940b-776">7</span></span>      | <span data-ttu-id="1940b-777">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-777">All providers</span></span> | <span data-ttu-id="1940b-778">システム</span><span class="sxs-lookup"><span data-stu-id="1940b-778">System</span></span>                                  | <span data-ttu-id="1940b-779">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-779">Debug</span></span>             |
| <span data-ttu-id="1940b-780">8</span><span class="sxs-lookup"><span data-stu-id="1940b-780">8</span></span>      | <span data-ttu-id="1940b-781">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-781">Debug</span></span>         | <span data-ttu-id="1940b-782">Microsoft</span><span class="sxs-lookup"><span data-stu-id="1940b-782">Microsoft</span></span>                               | <span data-ttu-id="1940b-783">トレース</span><span class="sxs-lookup"><span data-stu-id="1940b-783">Trace</span></span>             |

<span data-ttu-id="1940b-784">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-784">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="1940b-785">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-785">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="1940b-786">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-786">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="1940b-787">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-787">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="1940b-788">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-788">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="1940b-789">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-789">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="1940b-790">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-790">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="1940b-791">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-791">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="1940b-792">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-792">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="1940b-793">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-793">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="1940b-794">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="1940b-794">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="1940b-795">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-795">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="1940b-796">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-796">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="1940b-797">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-797">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="1940b-798">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="1940b-798">Rule 3 is most specific.</span></span>

<span data-ttu-id="1940b-799">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-799">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="1940b-800">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-800">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="1940b-801">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="1940b-801">Provider aliases</span></span>

<span data-ttu-id="1940b-802">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-802">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="1940b-803">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-803">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="1940b-804">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-804">Console</span></span>
* <span data-ttu-id="1940b-805">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-805">Debug</span></span>
* <span data-ttu-id="1940b-806">EventSource</span><span class="sxs-lookup"><span data-stu-id="1940b-806">EventSource</span></span>
* <span data-ttu-id="1940b-807">EventLog</span><span class="sxs-lookup"><span data-stu-id="1940b-807">EventLog</span></span>
* <span data-ttu-id="1940b-808">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1940b-808">TraceSource</span></span>
* <span data-ttu-id="1940b-809">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1940b-809">AzureAppServicesFile</span></span>
* <span data-ttu-id="1940b-810">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1940b-810">AzureAppServicesBlob</span></span>
* <span data-ttu-id="1940b-811">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1940b-811">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="1940b-812">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="1940b-812">Default minimum level</span></span>

<span data-ttu-id="1940b-813">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-813">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="1940b-814">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-814">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="1940b-815">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1940b-815">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="1940b-816">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="1940b-816">Filter functions</span></span>

<span data-ttu-id="1940b-817">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-817">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="1940b-818">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-818">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="1940b-819">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-819">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="1940b-820">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="1940b-820">System categories and levels</span></span>

<span data-ttu-id="1940b-821">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="1940b-821">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="1940b-822">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="1940b-822">Category</span></span>                            | <span data-ttu-id="1940b-823">メモ</span><span class="sxs-lookup"><span data-stu-id="1940b-823">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="1940b-824">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="1940b-824">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="1940b-825">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-825">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="1940b-826">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="1940b-826">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="1940b-827">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="1940b-827">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="1940b-828">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="1940b-828">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="1940b-829">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="1940b-829">Hosts allowed.</span></span> |
| <span data-ttu-id="1940b-830">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="1940b-830">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="1940b-831">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="1940b-831">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="1940b-832">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="1940b-832">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="1940b-833">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="1940b-833">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="1940b-834">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-834">MVC and Razor diagnostics.</span></span> <span data-ttu-id="1940b-835">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="1940b-835">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="1940b-836">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="1940b-836">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="1940b-837">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="1940b-837">Route matching information.</span></span> |
| <span data-ttu-id="1940b-838">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1940b-838">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="1940b-839">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="1940b-839">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="1940b-840">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="1940b-840">HTTPS certificate information.</span></span> |
| <span data-ttu-id="1940b-841">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="1940b-841">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="1940b-842">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="1940b-842">Files served.</span></span> |
| <span data-ttu-id="1940b-843">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="1940b-843">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="1940b-844">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="1940b-844">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="1940b-845">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="1940b-845">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="1940b-846">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="1940b-846">Log scopes</span></span>

 <span data-ttu-id="1940b-847">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-847">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="1940b-848">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-848">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="1940b-849">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-849">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="1940b-850">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="1940b-850">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="1940b-851">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-851">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="1940b-852">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="1940b-852">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="1940b-853">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1940b-853">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="1940b-854">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-854">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="1940b-855">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-855">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="1940b-856">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-856">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="1940b-857">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-857">Built-in logging providers</span></span>

<span data-ttu-id="1940b-858">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="1940b-858">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="1940b-859">コンソール</span><span class="sxs-lookup"><span data-stu-id="1940b-859">Console</span></span>](#console-provider)
* [<span data-ttu-id="1940b-860">デバッグ</span><span class="sxs-lookup"><span data-stu-id="1940b-860">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="1940b-861">EventSource</span><span class="sxs-lookup"><span data-stu-id="1940b-861">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="1940b-862">EventLog</span><span class="sxs-lookup"><span data-stu-id="1940b-862">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="1940b-863">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1940b-863">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="1940b-864">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="1940b-864">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1940b-865">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="1940b-865">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="1940b-866">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="1940b-866">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="1940b-867">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-867">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="1940b-868">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-868">Console provider</span></span>

<span data-ttu-id="1940b-869">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="1940b-869">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="1940b-870">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1940b-870">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="1940b-871">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-871">Debug provider</span></span>

<span data-ttu-id="1940b-872">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-872">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="1940b-873">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="1940b-873">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="1940b-874">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-874">Event Source provider</span></span>

<span data-ttu-id="1940b-875">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-875">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="1940b-876">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-876">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="1940b-877">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-877">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="1940b-878">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="1940b-878">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="1940b-879">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="1940b-879">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="1940b-880">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="1940b-880">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="1940b-881">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="1940b-881">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="1940b-883">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-883">Windows EventLog provider</span></span>

<span data-ttu-id="1940b-884">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="1940b-884">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="1940b-885">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-885">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="1940b-886">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-886">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="1940b-887">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="1940b-887">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="1940b-888">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="1940b-888">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="1940b-889">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="1940b-889">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="1940b-890">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-890">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="1940b-891">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-891">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="1940b-892">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-892">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="1940b-893">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-893">TraceSource provider</span></span>

<span data-ttu-id="1940b-894">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-894">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="1940b-895">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-895">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="1940b-896">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1940b-896">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="1940b-897">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-897">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="1940b-898">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-898">Azure App Service provider</span></span>

<span data-ttu-id="1940b-899">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-899">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="1940b-900">プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。</span><span class="sxs-lookup"><span data-stu-id="1940b-900">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="1940b-901">.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-901">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="1940b-902"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="1940b-902">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="1940b-903">設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。</span><span class="sxs-lookup"><span data-stu-id="1940b-903">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="1940b-904">("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)</span><span class="sxs-lookup"><span data-stu-id="1940b-904">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="1940b-905">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-905">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="1940b-906">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="1940b-906">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="1940b-907">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="1940b-907">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="1940b-908">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="1940b-908">**Application Logging (Blob)**</span></span>

<span data-ttu-id="1940b-909">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="1940b-909">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="1940b-910">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="1940b-910">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="1940b-911">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="1940b-911">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="1940b-912">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="1940b-912">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="1940b-913">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="1940b-913">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="1940b-914">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="1940b-914">Azure log streaming</span></span>

<span data-ttu-id="1940b-915">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-915">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="1940b-916">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="1940b-916">The app server</span></span>
* <span data-ttu-id="1940b-917">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="1940b-917">The web server</span></span>
* <span data-ttu-id="1940b-918">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="1940b-918">Failed request tracing</span></span>

<span data-ttu-id="1940b-919">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="1940b-919">To configure Azure log streaming:</span></span>

* <span data-ttu-id="1940b-920">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="1940b-920">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="1940b-921">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="1940b-921">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="1940b-922">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1940b-922">Choose the log **Level**.</span></span> <span data-ttu-id="1940b-923">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="1940b-923">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="1940b-924">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="1940b-924">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="1940b-925">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="1940b-925">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="1940b-926">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="1940b-926">Azure Application Insights trace logging</span></span>

<span data-ttu-id="1940b-927">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="1940b-927">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="1940b-928">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="1940b-928">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="1940b-929">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-929">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="1940b-930">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="1940b-930">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="1940b-931">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1940b-931">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="1940b-932">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1940b-932">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="1940b-933">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1940b-933">For more information, see the following resources:</span></span>

* [<span data-ttu-id="1940b-934">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="1940b-934">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="1940b-935">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="1940b-935">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="1940b-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="1940b-936">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="1940b-937">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="1940b-937">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="1940b-938">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="1940b-938">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="1940b-939">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="1940b-939">Third-party logging providers</span></span>

<span data-ttu-id="1940b-940">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="1940b-940">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="1940b-941">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-941">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-942">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="1940b-943">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="1940b-943">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="1940b-944">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="1940b-944">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="1940b-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="1940b-945">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="1940b-946">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-946">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-947">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="1940b-947">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="1940b-948">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1940b-948">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="1940b-949">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="1940b-949">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="1940b-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="1940b-950">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="1940b-951">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="1940b-951">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1940b-952">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="1940b-952">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="1940b-953">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="1940b-953">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="1940b-954">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1940b-954">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="1940b-955">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1940b-955">For more information, see each provider's documentation.</span></span> <span data-ttu-id="1940b-956">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1940b-956">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1940b-957">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="1940b-957">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
