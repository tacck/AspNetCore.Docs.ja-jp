---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110916"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="d4c20-103">.NET Core および ASP.NET Core でのログ記録</span><span class="sxs-lookup"><span data-stu-id="d4c20-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4c20-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d4c20-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d4c20-105">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d4c20-106">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d4c20-107">この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="d4c20-108">これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="d4c20-109">非 Web コンソール アプリで汎用ホストを使用する方法の例については、[バックグラウンド タスクのサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) の *Program.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="d4c20-110">汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="d4c20-111">ホスト以外のコードの例については、記事のこれらのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="d4c20-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d4c20-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="d4c20-113">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="d4c20-113">Add providers</span></span>

<span data-ttu-id="d4c20-114">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="d4c20-115">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="d4c20-116">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="d4c20-117">汎用ホストを使用するアプリにプロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="d4c20-118">ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="d4c20-119">`LoggerFactory` および `AddConsole` には、`Microsoft.Extensions.Logging` 用に `using` ステートメントが必要です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="d4c20-120">既定の ASP.NET Core プロジェクト テンプレートからは <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="d4c20-121">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="d4c20-122">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="d4c20-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="d4c20-124">[EventLog](#windows-eventlog-provider) (Windows 上で実行されている場合のみ)</span><span class="sxs-lookup"><span data-stu-id="d4c20-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="d4c20-125">既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="d4c20-126"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="d4c20-127">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="d4c20-128">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-128">Create logs</span></span>

<span data-ttu-id="d4c20-129">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d4c20-130">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="d4c20-131">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d4c20-132">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="d4c20-133">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="d4c20-134">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="d4c20-135">次のホスト コンソール以外のアプリの例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="d4c20-136">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d4c20-137">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="d4c20-138">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="d4c20-139">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-139">Create logs in the Program class</span></span>

<span data-ttu-id="d4c20-140">ASP.NET Core アプリの `Program` クラスでログを書き込むには、ホストをビルドした後に DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="d4c20-141">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d4c20-142">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-142">However, a separate logger can be used.</span></span> <span data-ttu-id="d4c20-143">次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="d4c20-144">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="d4c20-145">Startup クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-145">Create logs in the Startup class</span></span>

<span data-ttu-id="d4c20-146">ASP.NET Core アプリの `Startup.Configure` メソッドでログを書き込むには、メソッド シグネチャに `ILogger` パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="d4c20-147">`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="d4c20-148">`Startup` コンストラクターへのロガーの挿入はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="d4c20-149">`Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="d4c20-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="d4c20-150">この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="d4c20-151">DI コンテナーは、`ConfigureServices` が完了するまで設定されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="d4c20-152">ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="d4c20-153">汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="d4c20-154">`ILogger<T>` に依存するサービスを構成する必要がある場合でも、コンストラクターの挿入を使用するか、ファクトリ メソッドを用意して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="d4c20-155">ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="d4c20-156">たとえば、DI のサービスを使用してプロパティを設定する必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="d4c20-157">前の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスが初めて作成されるときに実行される `Func` です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="d4c20-158">この方法では、任意の登録済みサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="d4c20-159">Blazor でログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="d4c20-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d4c20-160">Blazor WebAssembly</span></span>

<span data-ttu-id="d4c20-161">`Program.Main` の `WebAssemblyHostBuilder.Logging` プロパティを使用して、Blazor WebAssembly でのログ記録を構成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="d4c20-162">`Logging` プロパティは型 <xref:Microsoft.Extensions.Logging.ILoggingBuilder> であるため、<xref:Microsoft.Extensions.Logging.ILoggingBuilder> で使用できるすべての拡張メソッドを `Logging` で使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="d4c20-163">Razor コンポーネントのログイン</span><span class="sxs-lookup"><span data-stu-id="d4c20-163">Log in Razor components</span></span>

<span data-ttu-id="d4c20-164">ロガーは、アプリのスタートアップ構成を尊重します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="d4c20-165"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> や <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> など、API の Intellisense 入力候補をサポートするには、<xref:Microsoft.Extensions.Logging> の `using` ディレクティブが必要です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="d4c20-166">次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILogger> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="d4c20-167">次の例は、Razor コンポーネントでの <xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用したログ記録を示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d4c20-168">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="d4c20-168">No asynchronous logger methods</span></span>

<span data-ttu-id="d4c20-169">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d4c20-170">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d4c20-171">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="d4c20-172">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d4c20-173">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d4c20-174">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="d4c20-175">構成</span><span class="sxs-lookup"><span data-stu-id="d4c20-175">Configuration</span></span>

<span data-ttu-id="d4c20-176">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="d4c20-177">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="d4c20-178">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d4c20-178">Command-line arguments.</span></span>
* <span data-ttu-id="d4c20-179">環境変数。</span><span class="sxs-lookup"><span data-stu-id="d4c20-179">Environment variables.</span></span>
* <span data-ttu-id="d4c20-180">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="d4c20-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="d4c20-181">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="d4c20-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="d4c20-182">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="d4c20-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="d4c20-183">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="d4c20-184">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="d4c20-185">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="d4c20-186">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="d4c20-187">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d4c20-188">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="d4c20-189">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="d4c20-190">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-190">The example is for the Console provider.</span></span> <span data-ttu-id="d4c20-191">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d4c20-192">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="d4c20-193">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="d4c20-194">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="d4c20-195">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d4c20-196">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d4c20-197">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="d4c20-198">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="d4c20-199">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="d4c20-200">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="d4c20-200">Sample logging output</span></span>

<span data-ttu-id="d4c20-201">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="d4c20-202">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-202">Here's an example of console output:</span></span>

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

<span data-ttu-id="d4c20-203">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="d4c20-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="d4c20-204">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="d4c20-205">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApiSample" から始まります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="d4c20-206">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d4c20-207">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="d4c20-208">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="d4c20-209">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="d4c20-209">NuGet packages</span></span>

<span data-ttu-id="d4c20-210">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="d4c20-211">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-211">Log category</span></span>

<span data-ttu-id="d4c20-212">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="d4c20-213">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d4c20-214">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="d4c20-215">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="d4c20-216">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="d4c20-217">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="d4c20-218">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-218">Log level</span></span>

<span data-ttu-id="d4c20-219">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="d4c20-220">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="d4c20-221">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="d4c20-222">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d4c20-223">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="d4c20-224">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d4c20-225">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="d4c20-226">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="d4c20-227">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="d4c20-228">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="d4c20-229">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="d4c20-230">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="d4c20-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="d4c20-231">Trace = 0</span></span>

  <span data-ttu-id="d4c20-232">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="d4c20-233">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="d4c20-234">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="d4c20-234">*Disabled by default.*</span></span>

* <span data-ttu-id="d4c20-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="d4c20-235">Debug = 1</span></span>

  <span data-ttu-id="d4c20-236">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="d4c20-237">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="d4c20-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="d4c20-238">Information = 2</span></span>

  <span data-ttu-id="d4c20-239">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="d4c20-240">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="d4c20-241">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="d4c20-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="d4c20-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="d4c20-242">Warning = 3</span></span>

  <span data-ttu-id="d4c20-243">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="d4c20-244">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="d4c20-245">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="d4c20-246">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="d4c20-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="d4c20-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="d4c20-247">Error = 4</span></span>

  <span data-ttu-id="d4c20-248">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d4c20-249">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="d4c20-250">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="d4c20-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="d4c20-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="d4c20-251">Critical = 5</span></span>

  <span data-ttu-id="d4c20-252">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-252">For failures that require immediate attention.</span></span> <span data-ttu-id="d4c20-253">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="d4c20-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="d4c20-254">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="d4c20-255">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-255">For example:</span></span>

* <span data-ttu-id="d4c20-256">運用環境:</span><span class="sxs-lookup"><span data-stu-id="d4c20-256">In production:</span></span>
  * <span data-ttu-id="d4c20-257">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d4c20-258">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="d4c20-259">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="d4c20-260">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="d4c20-261">開発中:</span><span class="sxs-lookup"><span data-stu-id="d4c20-261">During development:</span></span>
  * <span data-ttu-id="d4c20-262">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="d4c20-263">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="d4c20-264">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="d4c20-265">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d4c20-266">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="d4c20-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="d4c20-267">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="d4c20-268">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="d4c20-268">Log event ID</span></span>

<span data-ttu-id="d4c20-269">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d4c20-270">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="d4c20-271">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-271">An event ID associates a set of events.</span></span> <span data-ttu-id="d4c20-272">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d4c20-273">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d4c20-274">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d4c20-275">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="d4c20-276">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="d4c20-276">Log message template</span></span>

<span data-ttu-id="d4c20-277">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-277">Each log specifies a message template.</span></span> <span data-ttu-id="d4c20-278">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d4c20-279">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d4c20-280">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d4c20-281">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d4c20-282">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="d4c20-283">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="d4c20-284">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d4c20-285">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d4c20-286">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d4c20-287">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="d4c20-288">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="d4c20-289">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="d4c20-289">Logging exceptions</span></span>

<span data-ttu-id="d4c20-290">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="d4c20-291">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="d4c20-292">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="d4c20-293">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="d4c20-293">Log filtering</span></span>

<span data-ttu-id="d4c20-294">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="d4c20-295">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="d4c20-296">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="d4c20-297">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="d4c20-298">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-298">Create filter rules in configuration</span></span>

<span data-ttu-id="d4c20-299">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="d4c20-300">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="d4c20-301">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="d4c20-302">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="d4c20-303">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="d4c20-304">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="d4c20-304">Filter rules in code</span></span>

<span data-ttu-id="d4c20-305">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="d4c20-306">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="d4c20-307">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d4c20-308">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="d4c20-308">How filtering rules are applied</span></span>

<span data-ttu-id="d4c20-309">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="d4c20-310">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="d4c20-311">数値</span><span class="sxs-lookup"><span data-stu-id="d4c20-311">Number</span></span> | <span data-ttu-id="d4c20-312">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-312">Provider</span></span>      | <span data-ttu-id="d4c20-313">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-313">Categories that begin with ...</span></span>          | <span data-ttu-id="d4c20-314">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="d4c20-315">1</span><span class="sxs-lookup"><span data-stu-id="d4c20-315">1</span></span>      | <span data-ttu-id="d4c20-316">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-316">Debug</span></span>         | <span data-ttu-id="d4c20-317">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-317">All categories</span></span>                          | <span data-ttu-id="d4c20-318">情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-318">Information</span></span>       |
| <span data-ttu-id="d4c20-319">2</span><span class="sxs-lookup"><span data-stu-id="d4c20-319">2</span></span>      | <span data-ttu-id="d4c20-320">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-320">Console</span></span>       | <span data-ttu-id="d4c20-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="d4c20-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="d4c20-322">警告</span><span class="sxs-lookup"><span data-stu-id="d4c20-322">Warning</span></span>           |
| <span data-ttu-id="d4c20-323">3</span><span class="sxs-lookup"><span data-stu-id="d4c20-323">3</span></span>      | <span data-ttu-id="d4c20-324">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-324">Console</span></span>       | <span data-ttu-id="d4c20-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="d4c20-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="d4c20-326">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-326">Debug</span></span>             |
| <span data-ttu-id="d4c20-327">4</span><span class="sxs-lookup"><span data-stu-id="d4c20-327">4</span></span>      | <span data-ttu-id="d4c20-328">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-328">Console</span></span>       | <span data-ttu-id="d4c20-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="d4c20-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="d4c20-330">Error</span><span class="sxs-lookup"><span data-stu-id="d4c20-330">Error</span></span>             |
| <span data-ttu-id="d4c20-331">5</span><span class="sxs-lookup"><span data-stu-id="d4c20-331">5</span></span>      | <span data-ttu-id="d4c20-332">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-332">Console</span></span>       | <span data-ttu-id="d4c20-333">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-333">All categories</span></span>                          | <span data-ttu-id="d4c20-334">情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-334">Information</span></span>       |
| <span data-ttu-id="d4c20-335">6</span><span class="sxs-lookup"><span data-stu-id="d4c20-335">6</span></span>      | <span data-ttu-id="d4c20-336">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-336">All providers</span></span> | <span data-ttu-id="d4c20-337">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-337">All categories</span></span>                          | <span data-ttu-id="d4c20-338">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-338">Debug</span></span>             |
| <span data-ttu-id="d4c20-339">7</span><span class="sxs-lookup"><span data-stu-id="d4c20-339">7</span></span>      | <span data-ttu-id="d4c20-340">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-340">All providers</span></span> | <span data-ttu-id="d4c20-341">システム</span><span class="sxs-lookup"><span data-stu-id="d4c20-341">System</span></span>                                  | <span data-ttu-id="d4c20-342">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-342">Debug</span></span>             |
| <span data-ttu-id="d4c20-343">8</span><span class="sxs-lookup"><span data-stu-id="d4c20-343">8</span></span>      | <span data-ttu-id="d4c20-344">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-344">Debug</span></span>         | <span data-ttu-id="d4c20-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="d4c20-345">Microsoft</span></span>                               | <span data-ttu-id="d4c20-346">トレース</span><span class="sxs-lookup"><span data-stu-id="d4c20-346">Trace</span></span>             |

<span data-ttu-id="d4c20-347">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d4c20-348">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d4c20-349">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d4c20-350">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d4c20-351">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d4c20-352">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d4c20-353">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d4c20-354">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d4c20-355">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d4c20-356">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="d4c20-357">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="d4c20-358">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="d4c20-359">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="d4c20-360">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="d4c20-361">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="d4c20-362">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="d4c20-363">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="d4c20-364">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="d4c20-364">Provider aliases</span></span>

<span data-ttu-id="d4c20-365">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="d4c20-366">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="d4c20-367">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-367">Console</span></span>
* <span data-ttu-id="d4c20-368">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-368">Debug</span></span>
* <span data-ttu-id="d4c20-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-369">EventSource</span></span>
* <span data-ttu-id="d4c20-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="d4c20-370">EventLog</span></span>
* <span data-ttu-id="d4c20-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-371">TraceSource</span></span>
* <span data-ttu-id="d4c20-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d4c20-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="d4c20-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d4c20-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="d4c20-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d4c20-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="d4c20-375">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-375">Default minimum level</span></span>

<span data-ttu-id="d4c20-376">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="d4c20-377">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="d4c20-378">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="d4c20-379">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="d4c20-379">Filter functions</span></span>

<span data-ttu-id="d4c20-380">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="d4c20-381">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="d4c20-382">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="d4c20-383">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-383">System categories and levels</span></span>

<span data-ttu-id="d4c20-384">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="d4c20-385">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-385">Category</span></span>                            | <span data-ttu-id="d4c20-386">メモ</span><span class="sxs-lookup"><span data-stu-id="d4c20-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d4c20-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d4c20-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d4c20-388">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d4c20-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d4c20-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d4c20-390">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="d4c20-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d4c20-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d4c20-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d4c20-392">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="d4c20-392">Hosts allowed.</span></span> |
| <span data-ttu-id="d4c20-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d4c20-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d4c20-394">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="d4c20-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d4c20-395">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="d4c20-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d4c20-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d4c20-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d4c20-397">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d4c20-398">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="d4c20-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d4c20-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d4c20-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d4c20-400">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-400">Route matching information.</span></span> |
| <span data-ttu-id="d4c20-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="d4c20-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d4c20-402">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="d4c20-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d4c20-403">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="d4c20-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d4c20-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d4c20-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d4c20-405">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="d4c20-405">Files served.</span></span> |
| <span data-ttu-id="d4c20-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d4c20-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d4c20-407">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d4c20-408">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="d4c20-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="d4c20-409">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="d4c20-409">Log scopes</span></span>

 <span data-ttu-id="d4c20-410">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d4c20-411">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d4c20-412">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d4c20-413">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="d4c20-414">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="d4c20-415">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="d4c20-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c20-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="d4c20-417">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="d4c20-418">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="d4c20-419">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="d4c20-420">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-420">Built-in logging providers</span></span>

<span data-ttu-id="d4c20-421">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="d4c20-422">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="d4c20-423">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="d4c20-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="d4c20-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="d4c20-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="d4c20-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="d4c20-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d4c20-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d4c20-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d4c20-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d4c20-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d4c20-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="d4c20-430">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="d4c20-431">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-431">Console provider</span></span>

<span data-ttu-id="d4c20-432">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="d4c20-433">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="d4c20-434">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-434">Debug provider</span></span>

<span data-ttu-id="d4c20-435">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="d4c20-436">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="d4c20-437">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-437">Event Source provider</span></span>

<span data-ttu-id="d4c20-438">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d4c20-439">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="d4c20-440">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="d4c20-441">dotnet trace ツール</span><span class="sxs-lookup"><span data-stu-id="d4c20-441">dotnet trace tooling</span></span>

<span data-ttu-id="d4c20-442">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="d4c20-443">このツールでは、<xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource> プロバイダー データを収集します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="d4c20-444">次のコマンドを使用して、dotnet trace ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="d4c20-445">dotnet trace ツールを使用して、アプリからトレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="d4c20-446">アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="d4c20-447">`dotnet run` コマンドを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="d4c20-448">.NET Core アプリのプロセス識別子 (PID) を決定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="d4c20-449">Windows では、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="d4c20-450">タスク マネージャー (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="d4c20-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="d4c20-451">tasklist コマンド</span><span class="sxs-lookup"><span data-stu-id="d4c20-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="d4c20-452">Get-Process PowerShell コマンド</span><span class="sxs-lookup"><span data-stu-id="d4c20-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="d4c20-453">Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="d4c20-454">アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="d4c20-455">`dotnet trace` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="d4c20-456">一般的なコマンド構文</span><span class="sxs-lookup"><span data-stu-id="d4c20-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="d4c20-457">PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="d4c20-458">Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="d4c20-459">キーワード</span><span class="sxs-lookup"><span data-stu-id="d4c20-459">Keyword</span></span> | <span data-ttu-id="d4c20-460">説明</span><span class="sxs-lookup"><span data-stu-id="d4c20-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="d4c20-461">1</span><span class="sxs-lookup"><span data-stu-id="d4c20-461">1</span></span>       | <span data-ttu-id="d4c20-462">`LoggingEventSource` に関するメタ イベントをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="d4c20-463">`ILogger` からのイベントは記録されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="d4c20-464">2</span><span class="sxs-lookup"><span data-stu-id="d4c20-464">2</span></span>       | <span data-ttu-id="d4c20-465">`ILogger.Log()` が呼び出されたときに、`Message` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d4c20-466">プログラムで (書式設定されずに) 情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="d4c20-467">4</span><span class="sxs-lookup"><span data-stu-id="d4c20-467">4</span></span>       | <span data-ttu-id="d4c20-468">`ILogger.Log()` が呼び出されたときに、`FormatMessage` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d4c20-469">書式設定された文字列バージョンの情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="d4c20-470">8</span><span class="sxs-lookup"><span data-stu-id="d4c20-470">8</span></span>       | <span data-ttu-id="d4c20-471">`ILogger.Log()` が呼び出されたときに、`MessageJson` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="d4c20-472">引数の JSON 表現が提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="d4c20-473">イベント レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-473">Event Level</span></span> | <span data-ttu-id="d4c20-474">説明</span><span class="sxs-lookup"><span data-stu-id="d4c20-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="d4c20-475">0</span><span class="sxs-lookup"><span data-stu-id="d4c20-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="d4c20-476">1</span><span class="sxs-lookup"><span data-stu-id="d4c20-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="d4c20-477">2</span><span class="sxs-lookup"><span data-stu-id="d4c20-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="d4c20-478">3</span><span class="sxs-lookup"><span data-stu-id="d4c20-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="d4c20-479">4</span><span class="sxs-lookup"><span data-stu-id="d4c20-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="d4c20-480">5</span><span class="sxs-lookup"><span data-stu-id="d4c20-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="d4c20-481">`{Logger Category}` と `{Event Level}` の `FilterSpecs` エントリは、追加のログ フィルター条件を表します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="d4c20-482">セミコロン (`;`) で `FilterSpecs` エントリを区切ります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="d4c20-483">Windows コマンド シェルを使用した例 (`--providers` 値を囲む単一引用符**なし**):</span><span class="sxs-lookup"><span data-stu-id="d4c20-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="d4c20-484">上記のコマンドにより次のことがアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-484">The preceding command activates:</span></span>

   * <span data-ttu-id="d4c20-485">エラー (`2`) に対して書式設定された文字列 (`4`) を生成するイベント ソース ロガー。</span><span class="sxs-lookup"><span data-stu-id="d4c20-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="d4c20-486">`Informational` ログ レベル (`4`) での `Microsoft.AspNetCore.Hosting` のログ記録。</span><span class="sxs-lookup"><span data-stu-id="d4c20-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="d4c20-487">Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="d4c20-488">トレースは、`dotnet trace` コマンドが実行されたフォルダーに *trace.nettrace* という名前で保存されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="d4c20-489">[Perfview](#perfview) を使用してトレースを開きます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="d4c20-490">*trace.nettrace* ファイルを開き、トレース イベントを調べます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="d4c20-491">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="d4c20-491">For more information, see:</span></span>

* <span data-ttu-id="d4c20-492">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="d4c20-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="d4c20-493">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="d4c20-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="d4c20-494">[LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="d4c20-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="d4c20-495">[LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="d4c20-496">[Perfview](#perfview) &ndash; イベント ソース トレースの表示に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="d4c20-497">Perfview</span><span class="sxs-lookup"><span data-stu-id="d4c20-497">Perfview</span></span>

<span data-ttu-id="d4c20-498">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d4c20-499">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d4c20-500">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="d4c20-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d4c20-501">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="d4c20-503">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-503">Windows EventLog provider</span></span>

<span data-ttu-id="d4c20-504">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="d4c20-505">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d4c20-506">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d4c20-507">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="d4c20-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="d4c20-508">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="d4c20-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="d4c20-509">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="d4c20-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="d4c20-510">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="d4c20-511">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="d4c20-512">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="d4c20-513">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-513">TraceSource provider</span></span>

<span data-ttu-id="d4c20-514">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="d4c20-515">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="d4c20-516">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="d4c20-517">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="d4c20-518">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-518">Azure App Service provider</span></span>

<span data-ttu-id="d4c20-519">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="d4c20-520">プロバイダー パッケージは、共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="d4c20-521">プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="d4c20-522">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="d4c20-523">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d4c20-524">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d4c20-525">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="d4c20-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d4c20-526">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="d4c20-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d4c20-527">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d4c20-528">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d4c20-529">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d4c20-530">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="d4c20-531">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d4c20-532">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="d4c20-532">Azure log streaming</span></span>

<span data-ttu-id="d4c20-533">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="d4c20-534">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="d4c20-534">The app server</span></span>
* <span data-ttu-id="d4c20-535">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="d4c20-535">The web server</span></span>
* <span data-ttu-id="d4c20-536">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="d4c20-536">Failed request tracing</span></span>

<span data-ttu-id="d4c20-537">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="d4c20-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d4c20-538">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="d4c20-539">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d4c20-540">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-540">Choose the log **Level**.</span></span> <span data-ttu-id="d4c20-541">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="d4c20-542">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="d4c20-543">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="d4c20-544">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="d4c20-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="d4c20-545">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="d4c20-546">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d4c20-547">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d4c20-548">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d4c20-549">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d4c20-550">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="d4c20-551">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d4c20-552">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="d4c20-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d4c20-553">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d4c20-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d4c20-555">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d4c20-556">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d4c20-557">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-557">Third-party logging providers</span></span>

<span data-ttu-id="d4c20-558">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d4c20-559">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d4c20-561">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d4c20-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d4c20-562">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d4c20-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d4c20-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d4c20-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d4c20-564">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-565">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-566">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d4c20-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d4c20-567">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d4c20-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d4c20-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d4c20-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d4c20-569">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d4c20-570">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d4c20-571">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d4c20-572">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d4c20-573">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d4c20-574">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4c20-575">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4c20-576">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d4c20-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d4c20-577">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="d4c20-578">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="d4c20-579">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="d4c20-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="d4c20-580">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="d4c20-580">Add providers</span></span>

<span data-ttu-id="d4c20-581">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="d4c20-582">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="d4c20-583">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="d4c20-584">プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="d4c20-585">上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="d4c20-586">既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="d4c20-587">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-587">Console</span></span>
* <span data-ttu-id="d4c20-588">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-588">Debug</span></span>
* <span data-ttu-id="d4c20-589">EventSource (ASP.NET Core 2.2 以降)</span><span class="sxs-lookup"><span data-stu-id="d4c20-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="d4c20-590">`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="d4c20-591"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="d4c20-592">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="d4c20-593">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-593">Create logs</span></span>

<span data-ttu-id="d4c20-594">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="d4c20-595">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="d4c20-596">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="d4c20-597">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="d4c20-598">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="d4c20-599">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="d4c20-600">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="d4c20-601">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="d4c20-602">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="d4c20-603">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-603">Create logs in Startup</span></span>

<span data-ttu-id="d4c20-604">`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="d4c20-605">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-605">Create logs in the Program class</span></span>

<span data-ttu-id="d4c20-606">`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="d4c20-607">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="d4c20-608">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-608">However, a separate logger can be used.</span></span> <span data-ttu-id="d4c20-609">次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="d4c20-610">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="d4c20-611">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="d4c20-611">No asynchronous logger methods</span></span>

<span data-ttu-id="d4c20-612">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="d4c20-613">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="d4c20-614">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="d4c20-615">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="d4c20-616">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="d4c20-617">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="d4c20-618">構成</span><span class="sxs-lookup"><span data-stu-id="d4c20-618">Configuration</span></span>

<span data-ttu-id="d4c20-619">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="d4c20-620">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="d4c20-621">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="d4c20-621">Command-line arguments.</span></span>
* <span data-ttu-id="d4c20-622">環境変数。</span><span class="sxs-lookup"><span data-stu-id="d4c20-622">Environment variables.</span></span>
* <span data-ttu-id="d4c20-623">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="d4c20-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="d4c20-624">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="d4c20-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="d4c20-625">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="d4c20-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="d4c20-626">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="d4c20-627">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="d4c20-628">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="d4c20-629">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="d4c20-630">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="d4c20-631">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="d4c20-632">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="d4c20-633">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-633">The example is for the Console provider.</span></span> <span data-ttu-id="d4c20-634">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="d4c20-635">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="d4c20-636">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="d4c20-637">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="d4c20-638">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="d4c20-639">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="d4c20-640">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="d4c20-641">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="d4c20-642">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="d4c20-643">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="d4c20-643">Sample logging output</span></span>

<span data-ttu-id="d4c20-644">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="d4c20-645">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="d4c20-646">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="d4c20-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="d4c20-647">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="d4c20-648">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="d4c20-649">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="d4c20-650">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="d4c20-651">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="d4c20-652">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="d4c20-652">NuGet packages</span></span>

<span data-ttu-id="d4c20-653">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="d4c20-654">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-654">Log category</span></span>

<span data-ttu-id="d4c20-655">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="d4c20-656">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="d4c20-657">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="d4c20-658">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="d4c20-659">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="d4c20-660">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="d4c20-661">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-661">Log level</span></span>

<span data-ttu-id="d4c20-662">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="d4c20-663">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="d4c20-664">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="d4c20-665">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d4c20-666">前のコードでは、最初のパラメーターは[ログ イベント ID](#log-event-id) です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="d4c20-667">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="d4c20-668">メソッド パラメーターについては、この記事の後半の[メッセージ テンプレートのセクション](#log-message-template)で説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="d4c20-669">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="d4c20-670">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="d4c20-671">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="d4c20-672">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="d4c20-673">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="d4c20-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="d4c20-674">Trace = 0</span></span>

  <span data-ttu-id="d4c20-675">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="d4c20-676">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="d4c20-677">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="d4c20-677">*Disabled by default.*</span></span>

* <span data-ttu-id="d4c20-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="d4c20-678">Debug = 1</span></span>

  <span data-ttu-id="d4c20-679">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="d4c20-680">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="d4c20-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="d4c20-681">Information = 2</span></span>

  <span data-ttu-id="d4c20-682">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="d4c20-683">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="d4c20-684">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="d4c20-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="d4c20-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="d4c20-685">Warning = 3</span></span>

  <span data-ttu-id="d4c20-686">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="d4c20-687">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="d4c20-688">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="d4c20-689">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="d4c20-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="d4c20-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="d4c20-690">Error = 4</span></span>

  <span data-ttu-id="d4c20-691">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="d4c20-692">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="d4c20-693">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="d4c20-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="d4c20-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="d4c20-694">Critical = 5</span></span>

  <span data-ttu-id="d4c20-695">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="d4c20-695">For failures that require immediate attention.</span></span> <span data-ttu-id="d4c20-696">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="d4c20-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="d4c20-697">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="d4c20-698">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-698">For example:</span></span>

* <span data-ttu-id="d4c20-699">運用環境:</span><span class="sxs-lookup"><span data-stu-id="d4c20-699">In production:</span></span>
  * <span data-ttu-id="d4c20-700">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="d4c20-701">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="d4c20-702">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="d4c20-703">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="d4c20-704">開発中:</span><span class="sxs-lookup"><span data-stu-id="d4c20-704">During development:</span></span>
  * <span data-ttu-id="d4c20-705">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="d4c20-706">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="d4c20-707">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="d4c20-708">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="d4c20-709">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="d4c20-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="d4c20-710">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="d4c20-711">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="d4c20-711">Log event ID</span></span>

<span data-ttu-id="d4c20-712">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="d4c20-713">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="d4c20-714">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-714">An event ID associates a set of events.</span></span> <span data-ttu-id="d4c20-715">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="d4c20-716">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="d4c20-717">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="d4c20-718">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="d4c20-719">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="d4c20-719">Log message template</span></span>

<span data-ttu-id="d4c20-720">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-720">Each log specifies a message template.</span></span> <span data-ttu-id="d4c20-721">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="d4c20-722">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="d4c20-723">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="d4c20-724">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="d4c20-725">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="d4c20-726">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="d4c20-727">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="d4c20-728">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="d4c20-729">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="d4c20-730">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="d4c20-731">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="d4c20-732">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="d4c20-732">Logging exceptions</span></span>

<span data-ttu-id="d4c20-733">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="d4c20-734">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="d4c20-735">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="d4c20-736">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="d4c20-736">Log filtering</span></span>

<span data-ttu-id="d4c20-737">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="d4c20-738">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="d4c20-739">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="d4c20-740">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="d4c20-741">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="d4c20-741">Create filter rules in configuration</span></span>

<span data-ttu-id="d4c20-742">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="d4c20-743">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="d4c20-744">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="d4c20-745">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="d4c20-746">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="d4c20-747">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="d4c20-747">Filter rules in code</span></span>

<span data-ttu-id="d4c20-748">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="d4c20-749">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="d4c20-750">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="d4c20-751">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="d4c20-751">How filtering rules are applied</span></span>

<span data-ttu-id="d4c20-752">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="d4c20-753">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="d4c20-754">数値</span><span class="sxs-lookup"><span data-stu-id="d4c20-754">Number</span></span> | <span data-ttu-id="d4c20-755">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-755">Provider</span></span>      | <span data-ttu-id="d4c20-756">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-756">Categories that begin with ...</span></span>          | <span data-ttu-id="d4c20-757">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="d4c20-758">1</span><span class="sxs-lookup"><span data-stu-id="d4c20-758">1</span></span>      | <span data-ttu-id="d4c20-759">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-759">Debug</span></span>         | <span data-ttu-id="d4c20-760">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-760">All categories</span></span>                          | <span data-ttu-id="d4c20-761">情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-761">Information</span></span>       |
| <span data-ttu-id="d4c20-762">2</span><span class="sxs-lookup"><span data-stu-id="d4c20-762">2</span></span>      | <span data-ttu-id="d4c20-763">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-763">Console</span></span>       | <span data-ttu-id="d4c20-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="d4c20-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="d4c20-765">警告</span><span class="sxs-lookup"><span data-stu-id="d4c20-765">Warning</span></span>           |
| <span data-ttu-id="d4c20-766">3</span><span class="sxs-lookup"><span data-stu-id="d4c20-766">3</span></span>      | <span data-ttu-id="d4c20-767">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-767">Console</span></span>       | <span data-ttu-id="d4c20-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="d4c20-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="d4c20-769">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-769">Debug</span></span>             |
| <span data-ttu-id="d4c20-770">4</span><span class="sxs-lookup"><span data-stu-id="d4c20-770">4</span></span>      | <span data-ttu-id="d4c20-771">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-771">Console</span></span>       | <span data-ttu-id="d4c20-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="d4c20-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="d4c20-773">Error</span><span class="sxs-lookup"><span data-stu-id="d4c20-773">Error</span></span>             |
| <span data-ttu-id="d4c20-774">5</span><span class="sxs-lookup"><span data-stu-id="d4c20-774">5</span></span>      | <span data-ttu-id="d4c20-775">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-775">Console</span></span>       | <span data-ttu-id="d4c20-776">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-776">All categories</span></span>                          | <span data-ttu-id="d4c20-777">情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-777">Information</span></span>       |
| <span data-ttu-id="d4c20-778">6</span><span class="sxs-lookup"><span data-stu-id="d4c20-778">6</span></span>      | <span data-ttu-id="d4c20-779">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-779">All providers</span></span> | <span data-ttu-id="d4c20-780">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-780">All categories</span></span>                          | <span data-ttu-id="d4c20-781">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-781">Debug</span></span>             |
| <span data-ttu-id="d4c20-782">7</span><span class="sxs-lookup"><span data-stu-id="d4c20-782">7</span></span>      | <span data-ttu-id="d4c20-783">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-783">All providers</span></span> | <span data-ttu-id="d4c20-784">システム</span><span class="sxs-lookup"><span data-stu-id="d4c20-784">System</span></span>                                  | <span data-ttu-id="d4c20-785">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-785">Debug</span></span>             |
| <span data-ttu-id="d4c20-786">8</span><span class="sxs-lookup"><span data-stu-id="d4c20-786">8</span></span>      | <span data-ttu-id="d4c20-787">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-787">Debug</span></span>         | <span data-ttu-id="d4c20-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="d4c20-788">Microsoft</span></span>                               | <span data-ttu-id="d4c20-789">トレース</span><span class="sxs-lookup"><span data-stu-id="d4c20-789">Trace</span></span>             |

<span data-ttu-id="d4c20-790">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="d4c20-791">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="d4c20-792">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="d4c20-793">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="d4c20-794">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="d4c20-795">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="d4c20-796">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="d4c20-797">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="d4c20-798">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="d4c20-799">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="d4c20-800">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="d4c20-801">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="d4c20-802">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="d4c20-803">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="d4c20-804">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="d4c20-805">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="d4c20-806">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="d4c20-807">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="d4c20-807">Provider aliases</span></span>

<span data-ttu-id="d4c20-808">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="d4c20-809">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="d4c20-810">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-810">Console</span></span>
* <span data-ttu-id="d4c20-811">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-811">Debug</span></span>
* <span data-ttu-id="d4c20-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-812">EventSource</span></span>
* <span data-ttu-id="d4c20-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="d4c20-813">EventLog</span></span>
* <span data-ttu-id="d4c20-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-814">TraceSource</span></span>
* <span data-ttu-id="d4c20-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d4c20-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="d4c20-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d4c20-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="d4c20-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d4c20-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="d4c20-818">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-818">Default minimum level</span></span>

<span data-ttu-id="d4c20-819">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="d4c20-820">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="d4c20-821">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="d4c20-822">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="d4c20-822">Filter functions</span></span>

<span data-ttu-id="d4c20-823">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="d4c20-824">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="d4c20-825">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="d4c20-826">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="d4c20-826">System categories and levels</span></span>

<span data-ttu-id="d4c20-827">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="d4c20-828">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="d4c20-828">Category</span></span>                            | <span data-ttu-id="d4c20-829">メモ</span><span class="sxs-lookup"><span data-stu-id="d4c20-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="d4c20-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="d4c20-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="d4c20-831">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="d4c20-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="d4c20-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="d4c20-833">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="d4c20-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="d4c20-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="d4c20-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="d4c20-835">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="d4c20-835">Hosts allowed.</span></span> |
| <span data-ttu-id="d4c20-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="d4c20-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="d4c20-837">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="d4c20-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="d4c20-838">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="d4c20-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="d4c20-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="d4c20-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="d4c20-840">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="d4c20-841">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="d4c20-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="d4c20-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="d4c20-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="d4c20-843">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="d4c20-843">Route matching information.</span></span> |
| <span data-ttu-id="d4c20-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="d4c20-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="d4c20-845">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="d4c20-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="d4c20-846">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="d4c20-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="d4c20-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="d4c20-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="d4c20-848">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="d4c20-848">Files served.</span></span> |
| <span data-ttu-id="d4c20-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="d4c20-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="d4c20-850">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="d4c20-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="d4c20-851">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="d4c20-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="d4c20-852">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="d4c20-852">Log scopes</span></span>

 <span data-ttu-id="d4c20-853">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="d4c20-854">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="d4c20-855">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="d4c20-856">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="d4c20-857">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="d4c20-858">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="d4c20-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4c20-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="d4c20-860">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="d4c20-861">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="d4c20-862">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="d4c20-863">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-863">Built-in logging providers</span></span>

<span data-ttu-id="d4c20-864">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="d4c20-865">コンソール</span><span class="sxs-lookup"><span data-stu-id="d4c20-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="d4c20-866">デバッグ</span><span class="sxs-lookup"><span data-stu-id="d4c20-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="d4c20-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="d4c20-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="d4c20-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="d4c20-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="d4c20-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="d4c20-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="d4c20-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d4c20-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="d4c20-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="d4c20-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="d4c20-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="d4c20-873">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="d4c20-874">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-874">Console provider</span></span>

<span data-ttu-id="d4c20-875">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="d4c20-876">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="d4c20-877">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-877">Debug provider</span></span>

<span data-ttu-id="d4c20-878">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="d4c20-879">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="d4c20-880">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-880">Event Source provider</span></span>

<span data-ttu-id="d4c20-881">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="d4c20-882">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="d4c20-883">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="d4c20-884">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="d4c20-885">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="d4c20-886">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="d4c20-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="d4c20-887">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="d4c20-889">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-889">Windows EventLog provider</span></span>

<span data-ttu-id="d4c20-890">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="d4c20-891">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="d4c20-892">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="d4c20-893">`LogName` &ndash; "アプリケーション"</span><span class="sxs-lookup"><span data-stu-id="d4c20-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="d4c20-894">`SourceName` &ndash; ".NET ランタイム"</span><span class="sxs-lookup"><span data-stu-id="d4c20-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="d4c20-895">`MachineName` &ndash; ローカル コンピューター</span><span class="sxs-lookup"><span data-stu-id="d4c20-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="d4c20-896">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="d4c20-897">`Warning` より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="d4c20-898">たとえば、*appsettings.json* ファイルに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="d4c20-899">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-899">TraceSource provider</span></span>

<span data-ttu-id="d4c20-900">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="d4c20-901">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="d4c20-902">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="d4c20-903">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="d4c20-904">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-904">Azure App Service provider</span></span>

<span data-ttu-id="d4c20-905">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="d4c20-906">プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="d4c20-907">.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="d4c20-908"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="d4c20-909">設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="d4c20-910">("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)</span><span class="sxs-lookup"><span data-stu-id="d4c20-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="d4c20-911">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="d4c20-912">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="d4c20-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="d4c20-913">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="d4c20-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="d4c20-914">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="d4c20-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="d4c20-915">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="d4c20-916">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="d4c20-917">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="d4c20-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="d4c20-918">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="d4c20-919">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="d4c20-920">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="d4c20-920">Azure log streaming</span></span>

<span data-ttu-id="d4c20-921">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="d4c20-922">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="d4c20-922">The app server</span></span>
* <span data-ttu-id="d4c20-923">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="d4c20-923">The web server</span></span>
* <span data-ttu-id="d4c20-924">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="d4c20-924">Failed request tracing</span></span>

<span data-ttu-id="d4c20-925">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="d4c20-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="d4c20-926">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="d4c20-927">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="d4c20-928">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-928">Choose the log **Level**.</span></span> <span data-ttu-id="d4c20-929">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="d4c20-930">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="d4c20-931">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="d4c20-932">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="d4c20-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="d4c20-933">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="d4c20-934">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="d4c20-935">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="d4c20-936">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="d4c20-937">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="d4c20-938">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="d4c20-939">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="d4c20-940">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="d4c20-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="d4c20-941">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="d4c20-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="d4c20-943">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="d4c20-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="d4c20-944">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="d4c20-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="d4c20-945">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="d4c20-945">Third-party logging providers</span></span>

<span data-ttu-id="d4c20-946">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="d4c20-947">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="d4c20-949">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="d4c20-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="d4c20-950">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="d4c20-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="d4c20-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="d4c20-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="d4c20-952">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-953">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="d4c20-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="d4c20-954">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d4c20-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="d4c20-955">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="d4c20-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="d4c20-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="d4c20-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="d4c20-957">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="d4c20-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="d4c20-958">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="d4c20-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="d4c20-959">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="d4c20-960">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="d4c20-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="d4c20-961">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4c20-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="d4c20-962">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d4c20-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4c20-963">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d4c20-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
