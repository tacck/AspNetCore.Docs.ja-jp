---
title: .NET Core および ASP.NET Core でのログ記録
author: rick-anderson
description: Microsoft.Extensions.Logging NuGet パッケージで提供されるログ記録フレームワークの使用方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330774"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="4e90b-103">.NET Core および ASP.NET Core でのログ記録</span><span class="sxs-lookup"><span data-stu-id="4e90b-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e90b-104">作成者: [Kirk Larkin](https://twitter.com/serpent5)、[Juergen Gutsch](https://github.com/JuergenGutsch)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e90b-104">By [Kirk Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4e90b-105">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="4e90b-106">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="4e90b-107">この記事に記載されているほとんどのコード例は、ASP.NET Core アプリのものです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="4e90b-108">これらのコード スニペットのログ記録固有の部分は、[汎用ホスト](xref:fundamentals/host/generic-host)を使用するすべての .NET Core アプリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="4e90b-109">ASP.NET Core Web アプリ テンプレートでは、汎用ホストが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-109">The ASP.NET Core web app templates use the Generic Host.</span></span>

<span data-ttu-id="4e90b-110">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4e90b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="lp"></a>

## <a name="logging-providers"></a><span data-ttu-id="4e90b-111">ログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-111">Logging providers</span></span>

<span data-ttu-id="4e90b-112">ログを表示する `Console` プロバイダーを除き、ログ プロバイダーはログを保存します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-112">Logging providers store logs, except for the `Console` provider which displays logs.</span></span> <span data-ttu-id="4e90b-113">たとえば、Azure Application Insights プロバイダーでは、Azure Application Insights にログが保存されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-113">For example, the Azure Application Insights provider stores logs in Azure Application Insights.</span></span> <span data-ttu-id="4e90b-114">複数のプロバイダーを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-114">Multiple providers can be enabled.</span></span>

<span data-ttu-id="4e90b-115">既定の ASP.NET Core Web アプリ テンプレートでは:</span><span class="sxs-lookup"><span data-stu-id="4e90b-115">The default ASP.NET Core web app templates:</span></span>

* <span data-ttu-id="4e90b-116">[汎用ホスト](xref:fundamentals/host/generic-host)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-116">Use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>
* <span data-ttu-id="4e90b-117"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> が呼び出されます。これにより、次のログ プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-117">Call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>
  * [<span data-ttu-id="4e90b-118">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-118">Console</span></span>](#console)
  * [<span data-ttu-id="4e90b-119">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-119">Debug</span></span>](#debug)
  * [<span data-ttu-id="4e90b-120">EventSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-120">EventSource</span></span>](#event-source)
  * <span data-ttu-id="4e90b-121">[EventLog](#welog):Windows のみ</span><span class="sxs-lookup"><span data-stu-id="4e90b-121">[EventLog](#welog): Windows only</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

<span data-ttu-id="4e90b-122">上記のコードは、ASP.NET Core Web アプリ テンプレートを使用して作成された `Program` クラスを示しています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-122">The preceding code shows the `Program` class created with the ASP.NET Core web app templates.</span></span> <span data-ttu-id="4e90b-123">以降のいくつかのセクションでは、汎用ホストを使用する ASP.NET Core Web アプリ テンプレートに基づくサンプルを提供します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-123">The next several sections provide samples based on the ASP.NET Core web app templates, which use the Generic Host.</span></span> <span data-ttu-id="4e90b-124">[ホスト コンソール以外のアプリ](#nhca)については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-124">[Non-host console apps](#nhca) are discussed later in this document.</span></span>

<span data-ttu-id="4e90b-125">`Host.CreateDefaultBuilder` によって追加されたログ プロバイダーの既定のセットをオーバーライドするには、`ClearProviders` を呼び出し、必要なログ プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-125">To override the default set of logging providers added by `Host.CreateDefaultBuilder`, call `ClearProviders` and add the required logging providers.</span></span> <span data-ttu-id="4e90b-126">コード例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-126">For example, the following code:</span></span>

* <span data-ttu-id="4e90b-127"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出して、ビルダーからすべての <xref:Microsoft.Extensions.Logging.ILoggerProvider> インスタンスを削除します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-127">Calls <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> to remove all the <xref:Microsoft.Extensions.Logging.ILoggerProvider> instances from the builder.</span></span>
* <span data-ttu-id="4e90b-128">[Console](#console) ログ プロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-128">Adds the [Console](#console) logging provider.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

<span data-ttu-id="4e90b-129">その他のプロバイダーについては、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-129">For additional providers, see:</span></span>

* [<span data-ttu-id="4e90b-130">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-130">Built-in logging providers</span></span>](#bilp)
* <span data-ttu-id="4e90b-131">[サードパーティ製のログ プロバイダー](#third-party-logging-providers)</span><span class="sxs-lookup"><span data-stu-id="4e90b-131">[Third-party logging providers](#third-party-logging-providers).</span></span>

## <a name="create-logs"></a><span data-ttu-id="4e90b-132">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-132">Create logs</span></span>

<span data-ttu-id="4e90b-133">ログを作成するには、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) から <xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-133">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object from [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="4e90b-134">次のような例です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-134">The following example:</span></span>

* <span data-ttu-id="4e90b-135">`AboutModel` 型の完全修飾名のログ "*カテゴリ*" を使用する、ロガー `ILogger<AboutModel>` を作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-135">Creates a logger, `ILogger<AboutModel>`, which uses a log *category* of the fully qualified name of the type `AboutModel`.</span></span> <span data-ttu-id="4e90b-136">ログのカテゴリは、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-136">The log category is a string that is associated with each log.</span></span>
* <span data-ttu-id="4e90b-137"><xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> を呼び出して、`Information` レベルでログを記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-137">Calls <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> to log at the `Information` level.</span></span> <span data-ttu-id="4e90b-138">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-138">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

<span data-ttu-id="4e90b-139">[レベル](#log-level)と[カテゴリ](#log-category)については、このドキュメントで後ほど詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-139">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this document.</span></span>

<span data-ttu-id="4e90b-140">Blazor の詳細については、このドキュメントの[ Blazor および Blazor WebAssembly でのログの作成](#clib)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-140">For information on Blazor, see [Create logs in Blazor and Blazor WebAssembly](#clib) in this document.</span></span>

<span data-ttu-id="4e90b-141">[Main および Startup でログを作成](#clms)に関するセクションには、`Main` と `Startup` でログを作成する方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-141">[Create logs in Main and Startup](#clms) shows how to create logs in `Main` and `Startup`.</span></span>

## <a name="configure-logging"></a><span data-ttu-id="4e90b-142">ログの構成</span><span class="sxs-lookup"><span data-stu-id="4e90b-142">Configure logging</span></span>

<span data-ttu-id="4e90b-143">一般的に、ログの構成は *appsettings*.`{Environment}` *.json* ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-143">Logging configuration is commonly provided by the `Logging` section of *appsettings*.`{Environment}`*.json* files.</span></span> <span data-ttu-id="4e90b-144">次の*appsettings.Development.json* ファイルは、ASP.NET Core Web アプリ テンプレートによって生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-144">The following *appsettings.Development.json* file is generated by the ASP.NET Core web app templates:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

<span data-ttu-id="4e90b-145">上記の JSON では、次のように指定されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-145">In the preceding JSON:</span></span>

* <span data-ttu-id="4e90b-146">`"Default"`、`"Microsoft"`、および `"Microsoft.Hosting.Lifetime"` の各カテゴリが指定されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-146">The `"Default"`, `"Microsoft"`, and `"Microsoft.Hosting.Lifetime"` categories are specified.</span></span>
* <span data-ttu-id="4e90b-147">`"Microsoft"` カテゴリは、`"Microsoft"` で始まるすべてのカテゴリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-147">The `"Microsoft"` category applies to all categories that start with `"Microsoft"`.</span></span> <span data-ttu-id="4e90b-148">たとえば、この設定は `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` カテゴリに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-148">For example, this setting applies to the `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` category.</span></span>
* <span data-ttu-id="4e90b-149">`"Microsoft"` カテゴリでは、ログ レベルが `Warning` 以上のログが記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-149">The `"Microsoft"` category logs at log level `Warning` and higher.</span></span>
* <span data-ttu-id="4e90b-150">`"Microsoft.Hosting.Lifetime"` カテゴリは `"Microsoft"` カテゴリよりも詳細なので、`"Microsoft.Hosting.Lifetime"` カテゴリではログ レベルが "情報" 以上のログが記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-150">The `"Microsoft.Hosting.Lifetime"` category is more specific than the `"Microsoft"` category, so the `"Microsoft.Hosting.Lifetime"` category logs at log level "Information" and higher.</span></span>
* <span data-ttu-id="4e90b-151">特定のログ プロバイダーが指定されていないため、`LogLevel` は、[Windows EventLog](#welog) を除くすべての有効なログ プロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-151">A specific log provider is not specified, so `LogLevel` applies to all the enabled logging providers except for the [Windows EventLog](#welog).</span></span>

<span data-ttu-id="4e90b-152">`Logging` プロパティには <xref:Microsoft.Extensions.Logging.LogLevel> およびログ プロバイダーのプロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-152">The `Logging` property can have <xref:Microsoft.Extensions.Logging.LogLevel> and log provider properties.</span></span> <span data-ttu-id="4e90b-153">`LogLevel` により、選択したカテゴリに対するログの最小[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-153">The `LogLevel` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="4e90b-154">上記の JSON では、`Information` と `Warning` のログ レベルが指定されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-154">In the preceding JSON, `Information` and `Warning` log levels are specified.</span></span> <span data-ttu-id="4e90b-155">`LogLevel` はログの重大度を 0 - 6 の範囲で示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-155">`LogLevel` indicates the severity of the log and ranges from 0 to 6:</span></span>

<span data-ttu-id="4e90b-156">`Trace` = 0、`Debug` = 1、`Information` = 2、`Warning` = 3、`Error` = 4、`Critical` = 5、`None` = 6。</span><span class="sxs-lookup"><span data-stu-id="4e90b-156">`Trace` = 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5, and `None` = 6.</span></span>

<span data-ttu-id="4e90b-157">`LogLevel` を指定すると、指定したレベル以上のメッセージに対してログが有効になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-157">When a `LogLevel` is specified, logging is enabled for messages at the specified level and higher.</span></span> <span data-ttu-id="4e90b-158">上記の JSON では、`Default` カテゴリは `Information` 以上に対してのみログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-158">In the preceding JSON, the `Default` category is logged for `Information` and higher.</span></span> <span data-ttu-id="4e90b-159">たとえば、`Information`、`Warning`、`Error`、`Critical` のメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-159">For example, `Information`, `Warning`, `Error`, and `Critical` messages are logged.</span></span> <span data-ttu-id="4e90b-160">`LogLevel` が指定されていない場合、ログは既定で `Information` レベルになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-160">If no `LogLevel` is specified, logging defaults to the `Information` level.</span></span> <span data-ttu-id="4e90b-161">詳細については、「[ログ レベル](#llvl)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-161">For more information, see [Log levels](#llvl).</span></span>

<span data-ttu-id="4e90b-162">プロバイダー プロパティで `LogLevel` プロパティを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-162">A provider property can specify a `LogLevel` property.</span></span> <span data-ttu-id="4e90b-163">プロバイダーの下の `LogLevel` によって、そのプロバイダーのログに記録するレベルが指定され、プロバイダー以外のログ設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-163">`LogLevel` under a provider specifies levels to log for that provider, and overrides the non-provider log settings.</span></span> <span data-ttu-id="4e90b-164">以下の *appsettings.json* ファイルについて考えます:</span><span class="sxs-lookup"><span data-stu-id="4e90b-164">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

<span data-ttu-id="4e90b-165">`Logging.{providername}.LogLevel` の設定により `Logging.LogLevel` の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-165">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="4e90b-166">上記の JSON では、`Debug` プロバイダーの既定のログ レベルは `Information` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-166">In the preceding JSON, the `Debug` provider's default log level is set to `Information`:</span></span>

`Logging:Debug:LogLevel:Default:Information`

<span data-ttu-id="4e90b-167">上記の設定により、`Microsoft.Hosting` 以外のすべての `Logging:Debug:` カテゴリに `Information` ログ レベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-167">The preceding setting specifies the `Information` log level for every `Logging:Debug:` category except `Microsoft.Hosting`.</span></span> <span data-ttu-id="4e90b-168">特定のカテゴリが一覧表示されると、特定のカテゴリによって既定のカテゴリがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-168">When a specific category is listed, the specific category overrides the default category.</span></span> <span data-ttu-id="4e90b-169">上記の JSON では、`Logging:Debug:LogLevel` カテゴリ `"Microsoft.Hosting"` と `"Default"` によって、`Logging:LogLevel` の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-169">In the preceding JSON, the `Logging:Debug:LogLevel` categories `"Microsoft.Hosting"` and `"Default"` override the settings in `Logging:LogLevel`</span></span>

<span data-ttu-id="4e90b-170">最小ログ レベルは、次のいずれかに指定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-170">The minimum log level can be specified for any of:</span></span>

* <span data-ttu-id="4e90b-171">特定のプロバイダー: たとえば、`Logging:EventSource:LogLevel:Default:Information`</span><span class="sxs-lookup"><span data-stu-id="4e90b-171">Specific providers:  For example, `Logging:EventSource:LogLevel:Default:Information`</span></span>
* <span data-ttu-id="4e90b-172">特定のカテゴリ: たとえば、`Logging:LogLevel:Microsoft:Warning`</span><span class="sxs-lookup"><span data-stu-id="4e90b-172">Specific categories: For example, `Logging:LogLevel:Microsoft:Warning`</span></span>
* <span data-ttu-id="4e90b-173">すべてのプロバイダーとすべてのカテゴリ: `Logging:LogLevel:Default:Warning`</span><span class="sxs-lookup"><span data-stu-id="4e90b-173">All providers and all categories: `Logging:LogLevel:Default:Warning`</span></span>

<span data-ttu-id="4e90b-174">最小レベルを下回るログは、次のことが "***行われません***"。</span><span class="sxs-lookup"><span data-stu-id="4e90b-174">Any logs below the minimum level are ***not***:</span></span>

* <span data-ttu-id="4e90b-175">プロバイダーに渡される。</span><span class="sxs-lookup"><span data-stu-id="4e90b-175">Passed to the provider.</span></span>
* <span data-ttu-id="4e90b-176">ログに記録または表示される。</span><span class="sxs-lookup"><span data-stu-id="4e90b-176">Logged or displayed.</span></span>

<span data-ttu-id="4e90b-177">すべてのログを抑制するには、[LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel) を指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-177">To suppress all logs, specify [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel).</span></span> <span data-ttu-id="4e90b-178">`LogLevel.None` の値は、`LogLevel.Critical` (5) より大きい 6 です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-178">`LogLevel.None` has a value of 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="4e90b-179">プロバイダーで[ログのスコープ](#logscopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-179">If a provider supports [log scopes](#logscopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="4e90b-180">詳細については、「[ログ スコープ](#logscopes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-180">For more information, see [log scopes](#logscopes)</span></span>

<span data-ttu-id="4e90b-181">次の *appsettings.json* ファイルには、既定で有効になっているすべてのプロバイダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-181">The following *appsettings.json* file contains all the providers enabled by default:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

<span data-ttu-id="4e90b-182">上記のサンプルについて:</span><span class="sxs-lookup"><span data-stu-id="4e90b-182">In the preceding sample:</span></span>

* <span data-ttu-id="4e90b-183">カテゴリとレベルは推奨値ではありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-183">The categories and levels are not suggested values.</span></span> <span data-ttu-id="4e90b-184">このサンプルは、すべての既定のプロバイダーを表示するために提供されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-184">The sample is provided to show all the default providers.</span></span>
* <span data-ttu-id="4e90b-185">`Logging.{providername}.LogLevel` の設定により `Logging.LogLevel` の設定がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-185">Settings in `Logging.{providername}.LogLevel` override settings in `Logging.LogLevel`.</span></span> <span data-ttu-id="4e90b-186">たとえば、`Debug.LogLevel.Default` のレベルにより `LogLevel.Default` のレベルがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-186">For example, the level in `Debug.LogLevel.Default` overrides the level in `LogLevel.Default`.</span></span>
* <span data-ttu-id="4e90b-187">各既定のプロバイダーの "*別名*" が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-187">Each default provider *alias* is used.</span></span> <span data-ttu-id="4e90b-188">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-188">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span> <span data-ttu-id="4e90b-189">組み込みプロバイダーの別名には次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-189">The built-in providers aliases are:</span></span>
  * <span data-ttu-id="4e90b-190">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-190">Console</span></span>
  * <span data-ttu-id="4e90b-191">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-191">Debug</span></span>
  * <span data-ttu-id="4e90b-192">EventSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-192">EventSource</span></span>
  * <span data-ttu-id="4e90b-193">EventLog</span><span class="sxs-lookup"><span data-stu-id="4e90b-193">EventLog</span></span>
  * <span data-ttu-id="4e90b-194">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="4e90b-194">AzureAppServicesFile</span></span>
  * <span data-ttu-id="4e90b-195">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="4e90b-195">AzureAppServicesBlob</span></span>
  * <span data-ttu-id="4e90b-196">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="4e90b-196">ApplicationInsights</span></span>

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a><span data-ttu-id="4e90b-197">コマンド ライン、環境変数、およびその他の構成でログ レベルを設定する</span><span class="sxs-lookup"><span data-stu-id="4e90b-197">Set log level by command line, environment variables, and other configuration</span></span>

<span data-ttu-id="4e90b-198">ログ レベルは、いずれかの[構成プロバイダー](xref:fundamentals/configuration/index)で設定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-198">Log level can be set by any of the [configuration providers](xref:fundamentals/configuration/index).</span></span> 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4e90b-199">次のコマンドは:</span><span class="sxs-lookup"><span data-stu-id="4e90b-199">The following commands:</span></span>

* <span data-ttu-id="4e90b-200">環境キー `Logging:LogLevel:Microsoft` を Windows の `Information` の値に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-200">Set the environment key `Logging:LogLevel:Microsoft` to a value of `Information` on Windows.</span></span>
* <span data-ttu-id="4e90b-201">ASP.NET Core Web アプリケーション テンプレートを使用して作成されたアプリを使用する際に、設定をテストします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-201">Test the settings when using an app created with the ASP.NET Core web application templates.</span></span> <span data-ttu-id="4e90b-202">`dotnet run` コマンドは、`set` を使用した後、プロジェクト ディレクトリで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-202">The `dotnet run` command must be run in the project directory after using `set`.</span></span>

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

<span data-ttu-id="4e90b-203">上記の環境設定は:</span><span class="sxs-lookup"><span data-stu-id="4e90b-203">The preceding environment setting:</span></span>

* <span data-ttu-id="4e90b-204">コマンド ウィンドウから起動されたプロセスでのみ設定可能です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-204">Is only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="4e90b-205">Visual Studio で起動されたブラウザーでは読み取れません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-205">Isn't read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="4e90b-206">次の [setx](/windows-server/administration/windows-commands/setx) コマンドは、Windows 上で環境キーと値も設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-206">The following [setx](/windows-server/administration/windows-commands/setx) command also sets the environment key and value on Windows.</span></span> <span data-ttu-id="4e90b-207">`set` とは異なり、`setx` 設定は保持されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-207">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="4e90b-208">`/M` スイッチにより、システム環境で変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-208">The `/M` switch sets the variable in the system environment.</span></span> <span data-ttu-id="4e90b-209">`/M` が使用されていない場合には、ユーザー環境変数が設定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-209">If `/M` isn't used, a user environment variable is set.</span></span>

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

<span data-ttu-id="4e90b-210">[Azure App Service](https://azure.microsoft.com/services/app-service/) で、 **設定 > 構成** ページの**新しいアプリケーション設定**を選択します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-210">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="4e90b-211">Azure App Service アプリケーションの設定は：</span><span class="sxs-lookup"><span data-stu-id="4e90b-211">Azure App Service application settings are:</span></span>

* <span data-ttu-id="4e90b-212">保存時に暗号化され、暗号化されたチャネルで送信されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-212">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="4e90b-213">環境変数として公開されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-213">Exposed as environment variables.</span></span>

<span data-ttu-id="4e90b-214">詳細については、「[Azure アプリ: Azure Portal を使用してアプリの構成をオーバーライドする](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-214">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4e90b-215">環境変数を使用して ASP.NET Core 構成値を設定する方法の詳細については、「[環境変数](xref:fundamentals/configuration/index#environment-variables)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-215">For more information on setting ASP.NET Core configuration values using environment variables, see [environment variables](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="4e90b-216">コマンド ライン、Azure Key Vault、Azure App Configuration、その他のファイル形式など、他の構成ソースの使用の詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-216">For information on using other configuration sources, including the command line, Azure Key Vault, Azure App Configuration, other file formats, and more, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="4e90b-217">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="4e90b-217">How filtering rules are applied</span></span>

<span data-ttu-id="4e90b-218"><xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを作成すると、<xref:Microsoft.Extensions.Logging.ILoggerFactory> オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-218">When an <xref:Microsoft.Extensions.Logging.ILogger%601> object is created, the <xref:Microsoft.Extensions.Logging.ILoggerFactory> object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="4e90b-219">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-219">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="4e90b-220">使用できる規則から、各プロバイダーとカテゴリのペアごとに最も明確な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-220">The most specific rule for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="4e90b-221">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-221">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="4e90b-222">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-222">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="4e90b-223">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-223">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="4e90b-224">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-224">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="4e90b-225">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-225">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="4e90b-226">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-226">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="4e90b-227">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-227">If no rules are selected, use `MinimumLevel`.</span></span>

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a><span data-ttu-id="4e90b-228">dotnet run および Visual Studio からのログ出力</span><span class="sxs-lookup"><span data-stu-id="4e90b-228">Logging output from dotnet run and Visual Studio</span></span>

<span data-ttu-id="4e90b-229">[既定のログ プロバイダー](#lp)で作成されたログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-229">Logs created with the [default logging providers](#lp) are displayed:</span></span>

* <span data-ttu-id="4e90b-230">Visual Studio 内</span><span class="sxs-lookup"><span data-stu-id="4e90b-230">In Visual Studio</span></span>
  * <span data-ttu-id="4e90b-231">デバッグ時はデバッグ出力ウィンドウ内。</span><span class="sxs-lookup"><span data-stu-id="4e90b-231">In the Debug output window when debugging.</span></span>
  * <span data-ttu-id="4e90b-232">ASP.NET Core Web サーバー ウィンドウ内。</span><span class="sxs-lookup"><span data-stu-id="4e90b-232">In the ASP.NET Core Web Server window.</span></span>
* <span data-ttu-id="4e90b-233">アプリが `dotnet run` で実行されている場合はコンソール ウィンドウ内。</span><span class="sxs-lookup"><span data-stu-id="4e90b-233">In the console window when the app is run with `dotnet run`.</span></span>

<span data-ttu-id="4e90b-234">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-234">Logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="4e90b-235">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-235">ASP.NET Core and application code use the same logging API and providers.</span></span>

<a name="lcat"></a>

## <a name="log-category"></a><span data-ttu-id="4e90b-236">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-236">Log category</span></span>

<span data-ttu-id="4e90b-237">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-237">When an `ILogger` object is created, a *category* is specified.</span></span> <span data-ttu-id="4e90b-238">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-238">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="4e90b-239">カテゴリ文字列は任意ですが、規則ではクラス名を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-239">The category string is arbitrary, but the convention is to use the class name.</span></span> <span data-ttu-id="4e90b-240">たとえば、コントローラーでは、名前が `"TodoApi.Controllers.TodoController"` になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-240">For example, in a controller the name might be `"TodoApi.Controllers.TodoController"`.</span></span> <span data-ttu-id="4e90b-241">ASP.NET Core Web アプリでは、`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名を使用する `ILogger` インスタンスが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-241">The ASP.NET Core web apps use `ILogger<T>` to automatically get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="4e90b-242">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-242">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

<span data-ttu-id="4e90b-243">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-243">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

<a name="llvl"></a>

## <a name="log-level"></a><span data-ttu-id="4e90b-244">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-244">Log level</span></span>

<span data-ttu-id="4e90b-245">次の表に、<xref:Microsoft.Extensions.Logging.LogLevel> 値、便利な `Log{LogLevel}` 拡張メソッド、推奨される使用法を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-245">The following table lists the <xref:Microsoft.Extensions.Logging.LogLevel> values, the convenience `Log{LogLevel}` extension method, and the suggested usage:</span></span>

| <span data-ttu-id="4e90b-246">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4e90b-246">LogLevel</span></span> | <span data-ttu-id="4e90b-247">[値]</span><span class="sxs-lookup"><span data-stu-id="4e90b-247">Value</span></span> | <span data-ttu-id="4e90b-248">Method</span><span class="sxs-lookup"><span data-stu-id="4e90b-248">Method</span></span> | <span data-ttu-id="4e90b-249">説明</span><span class="sxs-lookup"><span data-stu-id="4e90b-249">Description</span></span> |
| -------- | ----- | ------ | ----------- |
| [<span data-ttu-id="4e90b-250">トレース</span><span class="sxs-lookup"><span data-stu-id="4e90b-250">Trace</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-251">0</span><span class="sxs-lookup"><span data-stu-id="4e90b-251">0</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | <span data-ttu-id="4e90b-252">最も詳細なメッセージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-252">Contain the most detailed messages.</span></span> <span data-ttu-id="4e90b-253">これらのメッセージには、機密性の高いアプリ データが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-253">These messages may contain sensitive app data.</span></span> <span data-ttu-id="4e90b-254">これらのメッセージは既定で無効になっているため、運用環境では有効に "***しないでください***"。</span><span class="sxs-lookup"><span data-stu-id="4e90b-254">These messages are disabled by default and should ***not*** be enabled in production.</span></span> |
| [<span data-ttu-id="4e90b-255">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-255">Debug</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-256">1</span><span class="sxs-lookup"><span data-stu-id="4e90b-256">1</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | <span data-ttu-id="4e90b-257">デバッグと開発用。</span><span class="sxs-lookup"><span data-stu-id="4e90b-257">For debugging and development.</span></span> <span data-ttu-id="4e90b-258">量が多いため、運用環境では慎重に使用してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-258">Use with caution in production due to the high volume.</span></span> |
| [<span data-ttu-id="4e90b-259">情報</span><span class="sxs-lookup"><span data-stu-id="4e90b-259">Information</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-260">2</span><span class="sxs-lookup"><span data-stu-id="4e90b-260">2</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | <span data-ttu-id="4e90b-261">アプリの一般的なフローを追跡します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-261">Tracks the general flow of the app.</span></span> <span data-ttu-id="4e90b-262">長期的な値が含まれている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-262">May have long-term value.</span></span> |
| [<span data-ttu-id="4e90b-263">警告</span><span class="sxs-lookup"><span data-stu-id="4e90b-263">Warning</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-264">3</span><span class="sxs-lookup"><span data-stu-id="4e90b-264">3</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | <span data-ttu-id="4e90b-265">異常なイベントや予期しないイベント用。</span><span class="sxs-lookup"><span data-stu-id="4e90b-265">For abnormal or unexpected events.</span></span> <span data-ttu-id="4e90b-266">通常、アプリが失敗する原因にならないエラーや条件が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-266">Typically includes errors or conditions that don't cause the app to fail.</span></span> |
| [<span data-ttu-id="4e90b-267">エラー</span><span class="sxs-lookup"><span data-stu-id="4e90b-267">Error</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-268">4</span><span class="sxs-lookup"><span data-stu-id="4e90b-268">4</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | <span data-ttu-id="4e90b-269">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-269">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="4e90b-270">これらのメッセージは、アプリ全体のエラーではなく、現在の操作や要求における失敗を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-270">These messages indicate a failure in the current operation or request, not an app-wide failure.</span></span> |
| [<span data-ttu-id="4e90b-271">重大</span><span class="sxs-lookup"><span data-stu-id="4e90b-271">Critical</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-272">5</span><span class="sxs-lookup"><span data-stu-id="4e90b-272">5</span></span> | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | <span data-ttu-id="4e90b-273">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-273">For failures that require immediate attention.</span></span> <span data-ttu-id="4e90b-274">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="4e90b-274">Examples: data loss scenarios, out of disk space.</span></span> |
| [<span data-ttu-id="4e90b-275">None</span><span class="sxs-lookup"><span data-stu-id="4e90b-275">None</span></span>](xref:Microsoft.Extensions.Logging.LogLevel) | <span data-ttu-id="4e90b-276">6</span><span class="sxs-lookup"><span data-stu-id="4e90b-276">6</span></span> | | <span data-ttu-id="4e90b-277">ログのカテゴリにメッセージを記述しないように指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-277">Specifies that a logging category should not write any messages.</span></span> |

<span data-ttu-id="4e90b-278">前の表では、重大度が最も低い方から高い方に `LogLevel` が一覧表示されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-278">In the previous table, the `LogLevel` is listed from lowest to highest severity.</span></span>

<span data-ttu-id="4e90b-279">[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) メソッドの最初のパラメーター <xref:Microsoft.Extensions.Logging.LogLevel> は、ログの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-279">The [Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) method's first parameter, <xref:Microsoft.Extensions.Logging.LogLevel>, indicates the severity of the log.</span></span> <span data-ttu-id="4e90b-280">ほとんどの開発者は、`Log(LogLevel, ...)` を呼び出すのではなく、[Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-280">Rather than calling `Log(LogLevel, ...)`, most developers call the [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) extension methods.</span></span> <span data-ttu-id="4e90b-281">`Log{LogLevel}` 拡張メソッドによって、[Log メソッドが呼び出され、LogLevel が指定されます](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-281">The `Log{LogLevel}` extension methods [call the Log method and specify the LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span> <span data-ttu-id="4e90b-282">たとえば、次の 2 つのログ呼び出しは、機能的に同等で、同じログが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-282">For example, the following two logging calls are functionally equivalent and produce the same log:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

<span data-ttu-id="4e90b-283">`MyLogEvents.TestItem` はイベント ID です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-283">`MyLogEvents.TestItem` is the event ID.</span></span> <span data-ttu-id="4e90b-284">`MyLogEvents` はサンプル アプリの一部であり、[ログ イベント ID](#leid) セクションに表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-284">`MyLogEvents` is part of the sample app and is displayed in the [Log event ID](#leid) section.</span></span>

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="4e90b-285">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-285">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="4e90b-286">上記のコードでは、最初の `Log{LogLevel}` パラメーター `MyLogEvents.GetItem` は、[ログ イベント ID](#leid) です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-286">In the preceding code, the first `Log{LogLevel}` parameter,`MyLogEvents.GetItem`, is the [Log event ID](#leid).</span></span> <span data-ttu-id="4e90b-287">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-287">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="4e90b-288">メソッド パラメーターについては、このドキュメントで後述する[メッセージ テンプレート](#lmt)のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-288">The method parameters are explained in the [message template](#lmt) section later in this document.</span></span>

<span data-ttu-id="4e90b-289">適切な `Log{LogLevel}` メソッドを呼び出して、特定のストレージ メディアに書き込むログ出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-289">Call the appropriate `Log{LogLevel}` method to control how much log output is written to a particular storage medium.</span></span> <span data-ttu-id="4e90b-290">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-290">For example:</span></span>

* <span data-ttu-id="4e90b-291">運用環境:</span><span class="sxs-lookup"><span data-stu-id="4e90b-291">In production:</span></span>
  * <span data-ttu-id="4e90b-292">`Trace` または `Information` のレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-292">Logging at the `Trace` or `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="4e90b-293">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` および `Information` のレベルのメッセージを、大容量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-293">To control costs and not exceed data storage limits, log `Trace` and `Information` level messages to a high-volume, low-cost data store.</span></span> <span data-ttu-id="4e90b-294">`Trace` と `Information` を特定のカテゴリに制限することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-294">Consider limiting `Trace` and `Information` to specific categories.</span></span>
  * <span data-ttu-id="4e90b-295">`Warning` から `Critical` のレベルでログを記録しても、ログ メッセージはほとんど生成されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-295">Logging at `Warning` through `Critical` levels should produce few log messages.</span></span>
    * <span data-ttu-id="4e90b-296">通常、コストとストレージの制限は考慮されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-296">Costs and storage limits usually aren't a concern.</span></span>
    * <span data-ttu-id="4e90b-297">ログの数が少ないほど、データ ストアをより柔軟に選択できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-297">Few logs allow more flexibility in data store choices.</span></span>
* <span data-ttu-id="4e90b-298">開発中:</span><span class="sxs-lookup"><span data-stu-id="4e90b-298">In development:</span></span>
  * <span data-ttu-id="4e90b-299">`Warning` を設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-299">Set to `Warning`.</span></span>
  * <span data-ttu-id="4e90b-300">トラブルシューティングの際に `Trace` または `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-300">Add `Trace` or `Information` messages when troubleshooting.</span></span> <span data-ttu-id="4e90b-301">出力を制限するには、調査中のカテゴリに対してのみ `Trace` または `Information` を設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-301">To limit output, set `Trace` or `Information` only for the categories under investigation.</span></span>

<span data-ttu-id="4e90b-302">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-302">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="4e90b-303">たとえば、次のログ出力について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-303">For example, consider the log output for:</span></span>

* <span data-ttu-id="4e90b-304">ASP.NET Core テンプレートを使用して作成された Razor Pages アプリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-304">A Razor Pages app created with the ASP.NET Core templates.</span></span>
* <span data-ttu-id="4e90b-305">`Logging:Console:LogLevel:Microsoft:Information` に設定されているログ</span><span class="sxs-lookup"><span data-stu-id="4e90b-305">Logging set to `Logging:Console:LogLevel:Microsoft:Information`</span></span>
* <span data-ttu-id="4e90b-306">プライバシー ページへの移動</span><span class="sxs-lookup"><span data-stu-id="4e90b-306">Navigation to the Privacy page:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

<span data-ttu-id="4e90b-307">次の JSON は `Logging:Console:LogLevel:Microsoft:Information` を設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-307">The following JSON sets `Logging:Console:LogLevel:Microsoft:Information`:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a><span data-ttu-id="4e90b-308">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="4e90b-308">Log event ID</span></span>

<span data-ttu-id="4e90b-309">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-309">Each log can specify an *event ID*.</span></span> <span data-ttu-id="4e90b-310">このサンプル アプリでは、`MyLogEvents` クラスを使用してイベント ID を定義します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-310">The sample app uses the `MyLogEvents` class to define event IDs:</span></span>

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="4e90b-311">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-311">An event ID associates a set of events.</span></span> <span data-ttu-id="4e90b-312">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-312">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="4e90b-313">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-313">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="4e90b-314">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-314">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="4e90b-315">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-315">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

<span data-ttu-id="4e90b-316">一部のログ プロバイダーでは、イベント ID がフィールドに格納されます。これにより、ID に対するフィルター処理が可能になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-316">Some logging providers store the event ID in a field, which allows for filtering on the ID.</span></span>

<a name="lmt"></a>

## <a name="log-message-template"></a><span data-ttu-id="4e90b-317">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="4e90b-317">Log message template</span></span>
<!-- Review, Each log API uses a message template. -->
<span data-ttu-id="4e90b-318">各ログ API では、メッセージ テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-318">Each log API uses a message template.</span></span> <span data-ttu-id="4e90b-319">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-319">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="4e90b-320">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-320">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

<span data-ttu-id="4e90b-321">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-321">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="4e90b-322">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-322">In the following code, the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="4e90b-323">上記のコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-323">The preceding code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: param1, param2
```

<span data-ttu-id="4e90b-324">この方法により、ログ プロバイダーが [セマンティック ログまたは構造化ログ](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)を実装できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-324">This approach allows logging providers to implement [semantic or structured logging](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging).</span></span> <span data-ttu-id="4e90b-325">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-325">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="4e90b-326">これにより、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-326">This enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="4e90b-327">たとえば、次のロガー メソッドについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-327">For example, consider the following logger method:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="4e90b-328">たとえば、Azure Table Storage にログを記録する場合:</span><span class="sxs-lookup"><span data-stu-id="4e90b-328">For example, when logging to Azure Table Storage:</span></span>

* <span data-ttu-id="4e90b-329">各 Azure Table エンティティには、`ID` プロパティと `RequestTime` プロパティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-329">Each Azure Table entity can have `ID` and `RequestTime` properties.</span></span>
* <span data-ttu-id="4e90b-330">プロパティを持つテーブルによって、ログに記録されたデータに対するクエリが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-330">Tables with properties simplify queries on logged data.</span></span> <span data-ttu-id="4e90b-331">たとえば、クエリによって、特定の `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-331">For example, a query can find all logs within a particular `RequestTime` range without having to parse the time out of the text message.</span></span>

## <a name="log-exceptions"></a><span data-ttu-id="4e90b-332">例外をログに記録する</span><span class="sxs-lookup"><span data-stu-id="4e90b-332">Log exceptions</span></span>

<span data-ttu-id="4e90b-333">ロガー メソッドには、例外パラメーターを受け取るオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-333">The logger methods have overloads that take an exception parameter:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

<span data-ttu-id="4e90b-334">例外ログはプロバイダー固有です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-334">Exception logging is provider-specific.</span></span>

### <a name="default-log-level"></a><span data-ttu-id="4e90b-335">既定のログ レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-335">Default log level</span></span>

<span data-ttu-id="4e90b-336">既定のログ レベルが設定されていない場合、既定のログ レベル値は `Information` になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-336">If the default log level is not set, the default log level value is `Information`.</span></span>

<span data-ttu-id="4e90b-337">たとえば、次の Web アプリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-337">For example, consider the following web app:</span></span>

* <span data-ttu-id="4e90b-338">ASP.NET Web アプリ テンプレートを使用して作成された。</span><span class="sxs-lookup"><span data-stu-id="4e90b-338">Created with the ASP.NET web app templates.</span></span>
* <span data-ttu-id="4e90b-339">*appsettings.json* と *appsettings.Development.json* が削除または名前が変更された。</span><span class="sxs-lookup"><span data-stu-id="4e90b-339">*appsettings.json* and *appsettings.Development.json* deleted or renamed.</span></span>

<span data-ttu-id="4e90b-340">上記の設定で、プライバシー ページまたはホーム ページに移動すると、カテゴリ名に `Microsoft` が含まれる多くの`Trace`、`Debug`、`Information` のメッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-340">With the preceding setup, navigating to the privacy or home page produces many `Trace`, `Debug`, and `Information`  messages with `Microsoft` in the category name.</span></span>

<span data-ttu-id="4e90b-341">次のコードは、既定のログ レベルが構成で設定されていない場合に、既定のログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-341">The following code sets the default log level when the default log level is not set in configuration:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
<span data-ttu-id="4e90b-342">一般に、ログ レベルは、コードではなく構成で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-342">Generally, log levels should be specified in configuration and not code.</span></span>

### <a name="filter-function"></a><span data-ttu-id="4e90b-343">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="4e90b-343">Filter function</span></span>

<span data-ttu-id="4e90b-344">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリに対して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-344">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

<span data-ttu-id="4e90b-345">上記のコードでは、カテゴリに `Controller` または `Microsoft` が含まれ、ログ レベルが `Information` 以上の場合にコンソール ログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-345">The preceding code displays console logs when the category contains `Controller` or `Microsoft` and the log level is `Information` or higher.</span></span>

<span data-ttu-id="4e90b-346">一般に、ログ レベルは、コードではなく構成で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-346">Generally, log levels should be specified in configuration and not code.</span></span>

## <a name="aspnet-core-and-ef-core-categories"></a><span data-ttu-id="4e90b-347">ASP.NET Core と EF Core のカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-347">ASP.NET Core and EF Core categories</span></span>

<span data-ttu-id="4e90b-348">次の表に、ASP.NET Core と Entity Framework Core で使用されるいくつかのカテゴリと、ログに関するメモを示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-348">The following table contains some categories used by ASP.NET Core and Entity Framework Core, with notes about the logs:</span></span>

| <span data-ttu-id="4e90b-349">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-349">Category</span></span>                            | <span data-ttu-id="4e90b-350">メモ</span><span class="sxs-lookup"><span data-stu-id="4e90b-350">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="4e90b-351">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="4e90b-351">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="4e90b-352">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-352">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="4e90b-353">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="4e90b-353">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="4e90b-354">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="4e90b-354">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="4e90b-355">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="4e90b-355">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="4e90b-356">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="4e90b-356">Hosts allowed.</span></span> |
| <span data-ttu-id="4e90b-357">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="4e90b-357">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="4e90b-358">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="4e90b-358">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="4e90b-359">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="4e90b-359">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="4e90b-360">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="4e90b-360">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="4e90b-361">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-361">MVC and Razor diagnostics.</span></span> <span data-ttu-id="4e90b-362">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="4e90b-362">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="4e90b-363">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="4e90b-363">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="4e90b-364">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-364">Route matching information.</span></span> |
| <span data-ttu-id="4e90b-365">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="4e90b-365">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="4e90b-366">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="4e90b-366">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="4e90b-367">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="4e90b-367">HTTPS certificate information.</span></span> |
| <span data-ttu-id="4e90b-368">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="4e90b-368">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="4e90b-369">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="4e90b-369">Files served.</span></span> |
| <span data-ttu-id="4e90b-370">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4e90b-370">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="4e90b-371">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-371">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="4e90b-372">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="4e90b-372">Database activity and configuration, change detection, migrations.</span></span> |

<span data-ttu-id="4e90b-373">コンソール ウィンドウに他のカテゴリを表示するには、**appsettings.Development.json** を次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-373">To view more categories in the console window, set **appsettings.Development.json** to the following:</span></span>

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a><span data-ttu-id="4e90b-374">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="4e90b-374">Log scopes</span></span>

 <span data-ttu-id="4e90b-375">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-375">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="4e90b-376">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-376">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="4e90b-377">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-377">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="4e90b-378">スコープは:</span><span class="sxs-lookup"><span data-stu-id="4e90b-378">A scope:</span></span>

* <span data-ttu-id="4e90b-379"><xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドによって返される <xref:System.IDisposable> 型です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-379">Is an <xref:System.IDisposable> type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method.</span></span>
* <span data-ttu-id="4e90b-380">破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-380">Lasts until it's disposed.</span></span>

<span data-ttu-id="4e90b-381">次のプロバイダーではスコープがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-381">The following providers support scopes:</span></span>

* `Console`
* [<span data-ttu-id="4e90b-382">AzureAppServicesFile と AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="4e90b-382">AzureAppServicesFile and AzureAppServicesBlob</span></span>](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

<span data-ttu-id="4e90b-383">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-383">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

<span data-ttu-id="4e90b-384">次の JSON では、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-384">The following JSON enables scopes for the console provider:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

<span data-ttu-id="4e90b-385">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-385">The following code enables scopes for the console provider:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

<span data-ttu-id="4e90b-386">一般に、ログは、コードではなく構成で指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-386">Generally, logging should be specified in configuration and not code.</span></span>

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a><span data-ttu-id="4e90b-387">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-387">Built-in logging providers</span></span>

<span data-ttu-id="4e90b-388">ASP.NET Core には、次のログ プロバイダーが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-388">ASP.NET Core includes the following logging providers:</span></span>

* [<span data-ttu-id="4e90b-389">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-389">Console</span></span>](#console)
* [<span data-ttu-id="4e90b-390">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-390">Debug</span></span>](#debug)
* [<span data-ttu-id="4e90b-391">EventSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-391">EventSource</span></span>](#event-source)
* [<span data-ttu-id="4e90b-392">EventLog</span><span class="sxs-lookup"><span data-stu-id="4e90b-392">EventLog</span></span>](#welog)
* [<span data-ttu-id="4e90b-393">AzureAppServicesFile と AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="4e90b-393">AzureAppServicesFile and AzureAppServicesBlob</span></span>](#azure-app-service)
* [<span data-ttu-id="4e90b-394">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="4e90b-394">ApplicationInsights</span></span>](#azure-application-insights)

<span data-ttu-id="4e90b-395">ASP.NET Core モジュールを使用した `stdout` およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-395">For information on `stdout` and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console"></a><span data-ttu-id="4e90b-396">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-396">Console</span></span>

<span data-ttu-id="4e90b-397">`Console` プロバイダーでは、コンソールへの出力がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-397">The `Console` provider logs output to the console.</span></span> <span data-ttu-id="4e90b-398">開発中の `Console` ログを表示する方法の詳細については、「[dotnet run および Visual Studio からのログ出力](#dnrvs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-398">For more information on viewing `Console` logs in development, see [Logging output from dotnet run and Visual Studio](#dnrvs).</span></span>

### <a name="debug"></a><span data-ttu-id="4e90b-399">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-399">Debug</span></span>

<span data-ttu-id="4e90b-400">`Debug` プロバイダーでは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラスを使用してログ出力が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-400">The `Debug` provider writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class.</span></span> <span data-ttu-id="4e90b-401">`Debug` プロバイダーに書き込むために `System.Diagnostics.Debug.WriteLine` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-401">Calls to `System.Diagnostics.Debug.WriteLine` write to the `Debug` provider.</span></span>

<span data-ttu-id="4e90b-402">Linux では、`Debug` プロバイダーのログの場所は配布によって異なり、次のいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-402">On Linux, the `Debug` provider log location is distribution-dependent and may be one of the following:</span></span>

* <span data-ttu-id="4e90b-403">*/var/log/message*</span><span class="sxs-lookup"><span data-stu-id="4e90b-403">*/var/log/message*</span></span>
* <span data-ttu-id="4e90b-404">*/var/log/syslog*</span><span class="sxs-lookup"><span data-stu-id="4e90b-404">*/var/log/syslog*</span></span>

### <a name="event-source"></a><span data-ttu-id="4e90b-405">イベント ソース</span><span class="sxs-lookup"><span data-stu-id="4e90b-405">Event Source</span></span>

<span data-ttu-id="4e90b-406">`EventSource` プロバイダーでは、`Microsoft-Extensions-Logging` という名前のクロスプラットフォーム イベント ソースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-406">The `EventSource` provider writes to a cross-platform event source with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="4e90b-407">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-407">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="4e90b-408">dotnet trace ツール</span><span class="sxs-lookup"><span data-stu-id="4e90b-408">dotnet trace tooling</span></span>

<span data-ttu-id="4e90b-409">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ツールは、実行中のプロセスの .NET Core のトレースのコレクションを有効にする、クロスプラットフォームの CLI グローバル ツールです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-409">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="4e90b-410">このツールでは、<xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> を使用して <xref:Microsoft.Extensions.Logging.EventSource> プロバイダー データを収集します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-410">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="4e90b-411">インストール手順については、[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-411">See [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) for installation instructions.</span></span>

<span data-ttu-id="4e90b-412">dotnet trace ツールを使用して、アプリからトレースを収集します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-412">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="4e90b-413">`dotnet run` コマンドを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-413">Run the app with the `dotnet run` command.</span></span>
1. <span data-ttu-id="4e90b-414">.NET Core アプリのプロセス識別子 (PID) を決定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-414">Determine the process identifier (PID) of the .NET Core app:</span></span>
   * <span data-ttu-id="4e90b-415">Windows では、次の方法のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-415">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="4e90b-416">タスク マネージャー (Ctrl + Alt + Del)</span><span class="sxs-lookup"><span data-stu-id="4e90b-416">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="4e90b-417">tasklist コマンド</span><span class="sxs-lookup"><span data-stu-id="4e90b-417">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="4e90b-418">Get-Process PowerShell コマンド</span><span class="sxs-lookup"><span data-stu-id="4e90b-418">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="4e90b-419">Linux では、[pidof コマンド](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-419">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="4e90b-420">アプリのアセンブリと同じ名前を持つプロセスの PID を検索します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-420">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="4e90b-421">`dotnet trace` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-421">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="4e90b-422">一般的なコマンド構文</span><span class="sxs-lookup"><span data-stu-id="4e90b-422">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="4e90b-423">PowerShell コマンド シェルを使用する場合は、`--providers` 値を単一引用符 (`'`) で囲みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-423">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="4e90b-424">Windows 以外のプラットフォームでは、`-f speedscope` オプションを追加して、出力トレース ファイルの形式を `speedscope` に変更します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-424">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="4e90b-425">キーワード</span><span class="sxs-lookup"><span data-stu-id="4e90b-425">Keyword</span></span> | <span data-ttu-id="4e90b-426">説明</span><span class="sxs-lookup"><span data-stu-id="4e90b-426">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="4e90b-427">1</span><span class="sxs-lookup"><span data-stu-id="4e90b-427">1</span></span>       | <span data-ttu-id="4e90b-428">`LoggingEventSource` に関するメタ イベントをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-428">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="4e90b-429">`ILogger` からのイベントは記録されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-429">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="4e90b-430">2</span><span class="sxs-lookup"><span data-stu-id="4e90b-430">2</span></span>       | <span data-ttu-id="4e90b-431">`ILogger.Log()` が呼び出されたときに、`Message` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-431">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="4e90b-432">プログラムで (書式設定されずに) 情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-432">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="4e90b-433">4</span><span class="sxs-lookup"><span data-stu-id="4e90b-433">4</span></span>       | <span data-ttu-id="4e90b-434">`ILogger.Log()` が呼び出されたときに、`FormatMessage` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-434">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="4e90b-435">書式設定された文字列バージョンの情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-435">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="4e90b-436">8</span><span class="sxs-lookup"><span data-stu-id="4e90b-436">8</span></span>       | <span data-ttu-id="4e90b-437">`ILogger.Log()` が呼び出されたときに、`MessageJson` イベントをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-437">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="4e90b-438">引数の JSON 表現が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-438">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="4e90b-439">イベント レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-439">Event Level</span></span> | <span data-ttu-id="4e90b-440">説明</span><span class="sxs-lookup"><span data-stu-id="4e90b-440">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="4e90b-441">0</span><span class="sxs-lookup"><span data-stu-id="4e90b-441">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="4e90b-442">1</span><span class="sxs-lookup"><span data-stu-id="4e90b-442">1</span></span>           | `Critical`      |
   | <span data-ttu-id="4e90b-443">2</span><span class="sxs-lookup"><span data-stu-id="4e90b-443">2</span></span>           | `Error`         |
   | <span data-ttu-id="4e90b-444">3</span><span class="sxs-lookup"><span data-stu-id="4e90b-444">3</span></span>           | `Warning`       |
   | <span data-ttu-id="4e90b-445">4</span><span class="sxs-lookup"><span data-stu-id="4e90b-445">4</span></span>           | `Informational` |
   | <span data-ttu-id="4e90b-446">5</span><span class="sxs-lookup"><span data-stu-id="4e90b-446">5</span></span>           | `Verbose`       |

   <span data-ttu-id="4e90b-447">`{Logger Category}` と `{Event Level}` の `FilterSpecs` エントリは、追加のログ フィルター条件を表します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-447">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="4e90b-448">セミコロン (`;`) で `FilterSpecs` エントリを区切ります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-448">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="4e90b-449">Windows コマンド シェルを使用した例 (`--providers` 値を囲む単一引用符**なし**):</span><span class="sxs-lookup"><span data-stu-id="4e90b-449">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="4e90b-450">上記のコマンドにより次のことがアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-450">The preceding command activates:</span></span>

   * <span data-ttu-id="4e90b-451">エラー (`2`) に対して書式設定された文字列 (`4`) を生成するイベント ソース ロガー。</span><span class="sxs-lookup"><span data-stu-id="4e90b-451">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="4e90b-452">`Informational` ログ レベル (`4`) での `Microsoft.AspNetCore.Hosting` のログ記録。</span><span class="sxs-lookup"><span data-stu-id="4e90b-452">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="4e90b-453">Enter キーまたは Ctrl + C キーを押すことで、dotnet trace ツールを停止します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-453">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="4e90b-454">トレースは、`dotnet trace` コマンドが実行されたフォルダーに *trace.nettrace* という名前で保存されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-454">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="4e90b-455">[Perfview](#perfview) を使用してトレースを開きます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-455">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="4e90b-456">*trace.nettrace* ファイルを開き、トレース イベントを調べます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-456">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="4e90b-457">アプリで `CreateDefaultBuilder` を使ってホストがビルドされない場合は、[イベント ソース プロバイダー](#event-source-provider)をアプリのログ構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-457">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

<span data-ttu-id="4e90b-458">詳細については次を参照してください:</span><span class="sxs-lookup"><span data-stu-id="4e90b-458">For more information, see:</span></span>

* <span data-ttu-id="4e90b-459">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="4e90b-459">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="4e90b-460">[パフォーマンス分析ユーティリティのトレース (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub リポジトリ ドキュメント)</span><span class="sxs-lookup"><span data-stu-id="4e90b-460">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="4e90b-461">[LoggingEventSource クラス](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API ブラウザー)</span><span class="sxs-lookup"><span data-stu-id="4e90b-461">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="4e90b-462">[LoggingEventSource 参照ソース (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs):別のバージョンの参照ソースを取得するには、分岐を `release/{Version}` に変更します。ここでは、`{Version}` は目的の ASP.NET Core のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-462">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="4e90b-463">[Perfview](#perfview):イベント ソース トレースの表示に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-463">[Perfview](#perfview): Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="4e90b-464">Perfview</span><span class="sxs-lookup"><span data-stu-id="4e90b-464">Perfview</span></span>

<span data-ttu-id="4e90b-465">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-465">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="4e90b-466">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-466">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="4e90b-467">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="4e90b-467">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="4e90b-468">文字列の先頭にある `*` を忘れないでください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-468">Don't miss the `*` at the start of the string.</span></span>

<a name="welog"></a>

### <a name="windows-eventlog"></a><span data-ttu-id="4e90b-469">Windows EventLog</span><span class="sxs-lookup"><span data-stu-id="4e90b-469">Windows EventLog</span></span>

<span data-ttu-id="4e90b-470">`EventLog` プロバイダーにより、ログ出力が Windows イベント ログに送信されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-470">The `EventLog` provider sends log output to the Windows Event Log.</span></span> <span data-ttu-id="4e90b-471">他のプロバイダーとは異なり、`EventLog` プロバイダーでは既定のプロバイダー以外の設定が継承 "***されません***"。</span><span class="sxs-lookup"><span data-stu-id="4e90b-471">Unlike the other providers, the `EventLog` provider does ***not*** inherit the default non-provider settings.</span></span> <span data-ttu-id="4e90b-472">`EventLog` ログ設定が指定されていない場合は、[既定の LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103) になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-472">If `EventLog` log settings aren't specified, they [default to LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).</span></span>

<span data-ttu-id="4e90b-473"><xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> より下のイベントをログに記録するには、ログ レベルを明示的に設定してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-473">To log events lower than <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType>, explicitly set the log level.</span></span> <span data-ttu-id="4e90b-474">次の例では、イベント ログの既定のログ レベルを <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-474">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

<span data-ttu-id="4e90b-475">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-475">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) can pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="4e90b-476">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-476">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="4e90b-477">`LogName`:"Application"</span><span class="sxs-lookup"><span data-stu-id="4e90b-477">`LogName`: "Application"</span></span>
* <span data-ttu-id="4e90b-478">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="4e90b-478">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="4e90b-479">`MachineName`:ローカル コンピューター名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-479">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="4e90b-480">次のコードでは、`SourceName` が既定値の `".NET Runtime"` から `MyLogs` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-480">The following code changes the `SourceName` from the default value of `".NET Runtime"` to `MyLogs`:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a><span data-ttu-id="4e90b-481">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="4e90b-481">Azure App Service</span></span>

<span data-ttu-id="4e90b-482">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-482">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

<span data-ttu-id="4e90b-483">プロバイダー パッケージは、共有フレームワークに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-483">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="4e90b-484">プロバイダーを使用するには、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-484">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="4e90b-485">プロバイダーの設定を構成するには、次の例のように <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> と <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-485">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

<span data-ttu-id="4e90b-486">Azure App Service にデプロイされると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) セクションの設定がアプリで使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-486">When deployed to Azure App Service, the app uses the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="4e90b-487">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-487">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="4e90b-488">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="4e90b-488">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="4e90b-489">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="4e90b-489">**Application Logging (Blob)**</span></span>

<span data-ttu-id="4e90b-490">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-490">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="4e90b-491">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-491">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="4e90b-492">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-492">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="4e90b-493">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみログを記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-493">This provider only logs when the project runs in the Azure environment.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="4e90b-494">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="4e90b-494">Azure log streaming</span></span>

<span data-ttu-id="4e90b-495">Azure ログのストリーミングでは、以下からのリアル タイムでのログ アクティビティの表示がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-495">Azure log streaming supports viewing log activity in real time from:</span></span>

* <span data-ttu-id="4e90b-496">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="4e90b-496">The app server</span></span>
* <span data-ttu-id="4e90b-497">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="4e90b-497">The web server</span></span>
* <span data-ttu-id="4e90b-498">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="4e90b-498">Failed request tracing</span></span>

<span data-ttu-id="4e90b-499">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="4e90b-499">To configure Azure log streaming:</span></span>

* <span data-ttu-id="4e90b-500">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-500">Navigate to the **App Service logs** page from the app's portal page.</span></span>
* <span data-ttu-id="4e90b-501">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-501">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="4e90b-502">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-502">Choose the log **Level**.</span></span> <span data-ttu-id="4e90b-503">この設定は、Azure ログ ストリーミングにのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-503">This setting only applies to Azure log streaming.</span></span>

<span data-ttu-id="4e90b-504">**[ログ ストリーム]** ページに移動して、ログを表示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-504">Navigate to the **Log Stream** page to view logs.</span></span> <span data-ttu-id="4e90b-505">ログに記録されたメッセージは、`ILogger` インターフェイスを使用してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-505">The logged messages are logged with the `ILogger` interface.</span></span>

### <a name="azure-application-insights"></a><span data-ttu-id="4e90b-506">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="4e90b-506">Azure Application Insights</span></span>

<span data-ttu-id="4e90b-507">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、[Azure Application Insights](/azure/azure-monitor/app/cloudservices) にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-507">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to [Azure Application Insights](/azure/azure-monitor/app/cloudservices).</span></span> <span data-ttu-id="4e90b-508">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-508">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="4e90b-509">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-509">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="4e90b-510">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-510">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="4e90b-511">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-511">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="4e90b-512">[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージは、ASP.NET Core ではなく、ASP.NET 4.x 用です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-512">The [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package is for ASP.NET 4.x, not ASP.NET Core.</span></span>

<span data-ttu-id="4e90b-513">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-513">For more information, see the following resources:</span></span>

* [<span data-ttu-id="4e90b-514">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="4e90b-514">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="4e90b-515">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-515">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="4e90b-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-516">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="4e90b-517">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-517">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="4e90b-518">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-518">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="4e90b-519">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-519">Third-party logging providers</span></span>

<span data-ttu-id="4e90b-520">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-520">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="4e90b-521">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-521">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-522">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="4e90b-523">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="4e90b-523">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="4e90b-524">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="4e90b-524">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="4e90b-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="4e90b-525">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="4e90b-526">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-526">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-527">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-527">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub リポジトリ](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span><span class="sxs-lookup"><span data-stu-id="4e90b-528">[PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([GitHub repo](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))</span></span>
* <span data-ttu-id="4e90b-529">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="4e90b-529">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="4e90b-530">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="4e90b-530">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="4e90b-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="4e90b-531">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="4e90b-532">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-532">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="4e90b-533">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-533">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="4e90b-534">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-534">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="4e90b-535">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-535">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="4e90b-536">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-536">For more information, see each provider's documentation.</span></span> <span data-ttu-id="4e90b-537">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-537">Third-party logging providers aren't supported by Microsoft.</span></span>

<a name="nhca"></a>

## <a name="non-host-console-app"></a><span data-ttu-id="4e90b-538">ホスト コンソール以外のアプリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-538">Non-host console app</span></span>

<span data-ttu-id="4e90b-539">非 Web コンソール アプリで汎用ホストを使用する方法の例については、[バックグラウンド タスクのサンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>) の *Program.cs* ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-539">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="4e90b-540">汎用ホストを使用しないアプリのログ記録コードは、[プロバイダーの追加](#add-providers)方法と[ロガーの作成](#create-logs)方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-540">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> 

### <a name="logging-providers"></a><span data-ttu-id="4e90b-541">ログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-541">Logging providers</span></span>

<span data-ttu-id="4e90b-542">ホスト コンソール以外のアプリでは、`LoggerFactory` を作成するときにプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-542">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a><span data-ttu-id="4e90b-543">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-543">Create logs</span></span>

<span data-ttu-id="4e90b-544">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-544">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="4e90b-545">`ILogger` を作成するには、`LoggerFactory` を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-545">Use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="4e90b-546">次の例では、カテゴリが `LoggingConsoleApp.Program` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-546">The following example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

<span data-ttu-id="4e90b-547">次の ASP.NET Core の例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-547">In the following ASP.NET CORE examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="4e90b-548">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-548">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

<span data-ttu-id="4e90b-549">[レベル](#log-level)と[カテゴリ](#log-category)については、このドキュメントで詳しく説明しています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-549">[Levels](#log-level) and [categories](#log-category) are explained in more detail in this document.</span></span>

<a name="lhc"></a>

## <a name="log-during-host-construction"></a><span data-ttu-id="4e90b-550">ホストの構築時のログ記録</span><span class="sxs-lookup"><span data-stu-id="4e90b-550">Log during host construction</span></span>

<span data-ttu-id="4e90b-551">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-551">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="4e90b-552">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-552">However, a separate logger can be used.</span></span> <span data-ttu-id="4e90b-553">次の例では、`CreateHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-553">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="4e90b-554">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-554">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a><span data-ttu-id="4e90b-555">ILogger に依存するサービスを構成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-555">Configure a service that depends on ILogger</span></span>

<span data-ttu-id="4e90b-556">ASP.NET Core の以前のバージョンでは Web ホスト用に別の DI コンテナーが作成されるため、ロガーの `Startup` へのコンストラクター挿入が機能します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-556">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="4e90b-557">汎用ホストに対して 1 つのコンテナーのみが作成される理由については、[破壊的変更に関するお知らせ](https://github.com/aspnet/Announcements/issues/353)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-557">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="4e90b-558">`ILogger<T>` に依存するサービスを構成するには、コンストラクターの挿入を使用するか、ファクトリ メソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-558">To configure a service that depends on `ILogger<T>`, use constructor injection or provide a factory method.</span></span> <span data-ttu-id="4e90b-559">ファクトリ メソッドの方法は、他の選択肢がない場合にのみお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-559">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="4e90b-560">たとえば、DI によって提供される `ILogger<T>` インスタンスを必要とするサービスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-560">For example, consider a service that needs an `ILogger<T>` instance provided by DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="4e90b-561">上記の強調表示されているコードは、DI コンテナーで `MyService` のインスタンスを初めて構築する必要があるときに実行される [Func](/dotnet/api/system.func-2) です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-561">The preceding highlighted code is a [Func](/dotnet/api/system.func-2) that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="4e90b-562">この方法では、任意の登録済みサービスにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-562">You can access any of the registered services in this way.</span></span>

<a name="clms"></a>

## <a name="create-logs-in-main"></a><span data-ttu-id="4e90b-563">Main でログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-563">Create logs in Main</span></span>

<span data-ttu-id="4e90b-564">次のコードでは、ホストを構築した後に DI から `ILogger` インスタンスを取得することによって `Main` でログを記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-564">The following code logs in `Main` by getting an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a><span data-ttu-id="4e90b-565">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-565">Create logs in Startup</span></span>

<span data-ttu-id="4e90b-566">次のコードは、`Startup.Configure` にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-566">The following code writes logs in `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

<span data-ttu-id="4e90b-567">`Startup.ConfigureServices` メソッドでの DI コンテナーの設定が完了する前にログを書き込むことはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-567">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="4e90b-568">`Startup` コンストラクターへのロガーの挿入はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-568">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="4e90b-569">`Startup.ConfigureServices` メソッド シグネチャへのロガーの挿入はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="4e90b-569">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="4e90b-570">この制限の理由は、ログ記録は DI と構成に依存しており、さらに構成は DI に依存しているためです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-570">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="4e90b-571">DI コンテナーは、`ConfigureServices` が完了するまで設定されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-571">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="4e90b-572">`ILogger<T>` に依存するサービスの構成、または `Startup` へのロガーのコンストラクター挿入が以前のバージョンで機能した理由については、[ILogger に依存するサービスの構成](#csdi)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-572">For information on configuring a service that depends on `ILogger<T>` or why constructor injection of a logger into `Startup` worked in earlier versions, see [Configure a service that depends on ILogger](#csdi)</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="4e90b-573">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="4e90b-573">No asynchronous logger methods</span></span>

<span data-ttu-id="4e90b-574">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-574">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="4e90b-575">ログ データ ストアが低速の場合は、そこへ直接書き込まないでください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-575">If a logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="4e90b-576">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-576">Consider writing the log messages to a fast store initially, then moving them to the slow store later.</span></span> <span data-ttu-id="4e90b-577">たとえば、SQL Server にログを記録する場合、`Log` メソッドは同期しているため、`Log` メソッドで直接それを行わないでください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-577">For example, when logging to SQL Server, don't do so directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="4e90b-578">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-578">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="4e90b-579">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-579">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a><span data-ttu-id="4e90b-580">実行中のアプリのログ レベルを変更する</span><span class="sxs-lookup"><span data-stu-id="4e90b-580">Change log levels in a running app</span></span>

<span data-ttu-id="4e90b-581">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-581">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="4e90b-582">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-582">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="4e90b-583">たとえば、[ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)では、既定でログ構成が再度読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-583">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), reloads logging configuration by default.</span></span> <span data-ttu-id="4e90b-584">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-584">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

## <a name="ilogger-and-iloggerfactory"></a><span data-ttu-id="4e90b-585">ILogger と ILoggerFactory</span><span class="sxs-lookup"><span data-stu-id="4e90b-585">ILogger and ILoggerFactory</span></span>

<span data-ttu-id="4e90b-586"><xref:Microsoft.Extensions.Logging.ILogger%601> と <xref:Microsoft.Extensions.Logging.ILoggerFactory> のインターフェイスと実装は .NET Core SDK に含まれています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-586">The <xref:Microsoft.Extensions.Logging.ILogger%601> and <xref:Microsoft.Extensions.Logging.ILoggerFactory> interfaces and implementations are included in the .NET Core SDK.</span></span> <span data-ttu-id="4e90b-587">これらは、次の NuGet パッケージでも入手できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-587">They are also available in the following NuGet packages:</span></span>  

* <span data-ttu-id="4e90b-588">インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) にあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-588">The interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).</span></span>
* <span data-ttu-id="4e90b-589">既定の実装は、[Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) にあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-589">The default implementations are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a><span data-ttu-id="4e90b-590">コードでログ フィルター規則を適用する</span><span class="sxs-lookup"><span data-stu-id="4e90b-590">Apply log filter rules in code</span></span>

<span data-ttu-id="4e90b-591">ログ フィルター規則を設定するには、[構成](xref:fundamentals/configuration/index)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-591">The preferred approach for setting log filter rules is by using [Configuration](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="4e90b-592">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-592">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

<span data-ttu-id="4e90b-593">`logging.AddFilter("System", LogLevel.Debug)` は `System` カテゴリとログ レベル `Debug` を指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-593">`logging.AddFilter("System", LogLevel.Debug)` specifies the `System` category and log level `Debug`.</span></span> <span data-ttu-id="4e90b-594">特定のプロバイダーが構成されていないため、フィルターはすべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-594">The filter is applied to all providers because a specific provider was not configured.</span></span>

<span data-ttu-id="4e90b-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` は以下を指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-595">`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` specifies:</span></span>

* <span data-ttu-id="4e90b-596">`Debug` ログ プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="4e90b-596">The `Debug` logging provider.</span></span>
* <span data-ttu-id="4e90b-597">ログ レベル `Information` 以上。</span><span class="sxs-lookup"><span data-stu-id="4e90b-597">Log level `Information` and higher.</span></span>
* <span data-ttu-id="4e90b-598">`"Microsoft"` で始まるすべてのカテゴリ。</span><span class="sxs-lookup"><span data-stu-id="4e90b-598">All categories starting with `"Microsoft"`.</span></span>

## <a name="create-a-custom-logger"></a><span data-ttu-id="4e90b-599">カスタム ロガーを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-599">Create a custom logger</span></span>

<span data-ttu-id="4e90b-600">カスタム ロガーを追加するには、<xref:Microsoft.Extensions.Logging.ILoggerFactory> を使用して <xref:Microsoft.Extensions.Logging.ILoggerProvider> を追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-600">To add a custom logger, add an <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

<span data-ttu-id="4e90b-601">`ILoggerProvider` により、1 つ以上の `ILogger` インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-601">The `ILoggerProvider` creates one or more `ILogger` instances.</span></span> <span data-ttu-id="4e90b-602">`ILogger` インスタンスは、情報をログに記録するためにフレームワークによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-602">The `ILogger` instances are used by the framework to log the information.</span></span>

### <a name="sample-custom-logger-configuration"></a><span data-ttu-id="4e90b-603">カスタム ロガーの構成のサンプル</span><span class="sxs-lookup"><span data-stu-id="4e90b-603">Sample custom logger configuration</span></span>

<span data-ttu-id="4e90b-604">サンプルでは次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-604">The sample:</span></span>

* <span data-ttu-id="4e90b-605">イベント ID とログ レベルによってログ コンソールの色を設定する非常に基本的なサンプルとして設計されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-605">Is designed to be a very basic sample that sets the color of the log console by event ID and log level.</span></span> <span data-ttu-id="4e90b-606">ロガーは通常、イベント ID によって変更されることはなく、ログ レベルに固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-606">Loggers generally don't change by event ID and are not specific to log level.</span></span>
* <span data-ttu-id="4e90b-607">次の構成の種類を使用して、ログ レベルとイベント ID ごとに異なるカラー コンソール エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-607">Creates different color console entries per log level and event ID using the following configuration type:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

<span data-ttu-id="4e90b-608">上記のコードでは、既定のレベルを `Warning` に設定し、色を `Yellow` に設定しています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-608">The preceding code sets the default level to `Warning` and the color to `Yellow`.</span></span> <span data-ttu-id="4e90b-609">`EventId` が 0 に設定されている場合は、すべてのイベントをログに記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-609">If the `EventId` is set to 0, we will log all events.</span></span>

### <a name="create-the-custom-logger"></a><span data-ttu-id="4e90b-610">カスタム ロガーを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-610">Create the custom logger</span></span>

<span data-ttu-id="4e90b-611">`ILogger` 実装カテゴリ名は、通常、ログ ソースです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-611">The `ILogger` implementation category name is typically the logging source.</span></span> <span data-ttu-id="4e90b-612">たとえば、ロガーが作成される型は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-612">For example, the type where the logger is created:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

<span data-ttu-id="4e90b-613">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-613">The preceding code:</span></span>

* <span data-ttu-id="4e90b-614">カテゴリ名ごとにロガー インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-614">Creates a logger instance per category name.</span></span>
* <span data-ttu-id="4e90b-615">各 `logLevel` に一意のロガーがあるようにするため、`IsEnabled` で `logLevel == _config.LogLevel` を確認します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-615">Checks `logLevel == _config.LogLevel` in `IsEnabled`, so each `logLevel` has a unique logger.</span></span> <span data-ttu-id="4e90b-616">一般に、ロガーは、すべての上位ログ レベルに対しても有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-616">Generally, loggers should also be enabled for all higher log levels:</span></span>

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a><span data-ttu-id="4e90b-617">カスタム LoggerProvider を作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-617">Create the custom LoggerProvider</span></span>

<span data-ttu-id="4e90b-618">`LoggerProvider` は、ロガー インスタンスを作成するクラスです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-618">The `LoggerProvider` is the class that creates the logger instances.</span></span> <span data-ttu-id="4e90b-619">カテゴリごとにロガー インスタンスを作成する必要はないかもしれませんが、これは NLog や log4net などの一部のロガーでは理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-619">Maybe it is not needed to create a logger instance per category, but this makes sense for some Loggers, like NLog or log4net.</span></span> <span data-ttu-id="4e90b-620">これを行うと、必要に応じて、カテゴリごとに異なるログ出力ターゲットを選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-620">Doing this you are also able to choose different logging output targets per category if needed:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

<span data-ttu-id="4e90b-621">上記のコードでは、<xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> によってカテゴリ名ごとに `ColorConsoleLogger` のインスタンスが 1 つ作成され、それが [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-621">In the preceding code, <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> creates a single instance of the `ColorConsoleLogger` per category name and stores it in the [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2);</span></span>

### <a name="usage-and-registration-of-the-custom-logger"></a><span data-ttu-id="4e90b-622">カスタム ロガーの使用と登録</span><span class="sxs-lookup"><span data-stu-id="4e90b-622">Usage and registration of the custom logger</span></span>

<span data-ttu-id="4e90b-623">`Startup.Configure` にロガーを登録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-623">Register the logger in the `Startup.Configure`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

<span data-ttu-id="4e90b-624">上記のコードでは、`ILoggerFactory` に少なくとも 1 つの拡張メソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-624">For the preceding code, provide at least one extension method for the `ILoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="4e90b-625">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4e90b-625">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
* <span data-ttu-id="4e90b-626">ログのバグは、[github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) リポジトリに作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-626">Logging bugs should be created in the [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) repo.</span></span>
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e90b-627">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4e90b-627">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4e90b-628">.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-628">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="4e90b-629">この記事では、組み込みプロバイダーと共にログ API を使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-629">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="4e90b-630">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4e90b-630">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="4e90b-631">プロバイダーを追加する</span><span class="sxs-lookup"><span data-stu-id="4e90b-631">Add providers</span></span>

<span data-ttu-id="4e90b-632">ログ プロバイダーによってログが表示または格納されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-632">A logging provider displays or stores logs.</span></span> <span data-ttu-id="4e90b-633">たとえば、Console プロバイダーによってコンソール上にログが表示され、Azure Application Insights プロバイダーによってそれらが Azure Application Insights に格納されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-633">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="4e90b-634">複数のプロバイダーを追加することで、複数の宛先にログを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-634">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="4e90b-635">プロバイダーを追加するには、*Program.cs* でプロバイダーの `Add{provider name}` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-635">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="4e90b-636">上記のコードには、`Microsoft.Extensions.Logging` と `Microsoft.Extensions.Configuration` への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-636">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="4e90b-637">既定のプロジェクト テンプレートでは、次のログ プロバイダーを追加する <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-637">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="4e90b-638">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-638">Console</span></span>
* <span data-ttu-id="4e90b-639">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-639">Debug</span></span>
* <span data-ttu-id="4e90b-640">EventSource (ASP.NET Core 2.2 以降)</span><span class="sxs-lookup"><span data-stu-id="4e90b-640">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="4e90b-641">`CreateDefaultBuilder` を使用する場合は、既定のプロバイダーを自分で選択したものと置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-641">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="4e90b-642"><xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> を呼び出し、目的のプロバイダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-642">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="4e90b-643">この記事の後半では、[組み込みログ プロバイダー](#built-in-logging-providers)と[サードパーティ製ログ プロバイダー](#third-party-logging-providers)について説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-643">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="4e90b-644">ログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-644">Create logs</span></span>

<span data-ttu-id="4e90b-645">ログを作成するには、<xref:Microsoft.Extensions.Logging.ILogger%601> オブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-645">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="4e90b-646">Web アプリまたはホステッド サービスで、依存関係の挿入 (DI) から `ILogger` を取得します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-646">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="4e90b-647">ホスト コンソール以外のアプリでは、`LoggerFactory` を使用して `ILogger` を作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-647">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="4e90b-648">次の ASP.NET Core の例では、カテゴリが `TodoApiSample.Pages.AboutModel` のロガーを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-648">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="4e90b-649">ログの "*カテゴリ*" は、各ログに関連付けられている文字列です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-649">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="4e90b-650">DI で提供される `ILogger<T>` インスタンスでは、カテゴリとして型 `T` の完全修飾名を持つログが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-650">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="4e90b-651">次の ASP.NET Core とコンソール アプリの例では、ロガーを使用して、レベルが `Information` のログを作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-651">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="4e90b-652">ログの "*レベル*" は、ログに記録されるイベントの重大度を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-652">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="4e90b-653">[レベル](#log-level)と[カテゴリ](#log-category)の詳細については、この記事で後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-653">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="4e90b-654">Startup でログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-654">Create logs in Startup</span></span>

<span data-ttu-id="4e90b-655">`Startup` クラスでログを作成するには、コンストラクター シグネチャに `ILogger` パラメーターを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-655">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="4e90b-656">Program クラスでログを作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-656">Create logs in the Program class</span></span>

<span data-ttu-id="4e90b-657">`Program` クラスでログを作成するには、DI から `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-657">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="4e90b-658">ホストの構築時のログ記録は、直接サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-658">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="4e90b-659">ただし、別のロガーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-659">However, a separate logger can be used.</span></span> <span data-ttu-id="4e90b-660">次の例では、`CreateWebHostBuilder` でログを記録するために、[Serilog](https://serilog.net/) ロガーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-660">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="4e90b-661">`AddSerilog` では、`Log.Logger` で指定された静的な構成が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-661">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="4e90b-662">非同期でないロガー メソッド</span><span class="sxs-lookup"><span data-stu-id="4e90b-662">No asynchronous logger methods</span></span>

<span data-ttu-id="4e90b-663">ログ記録は高速に実行され、非同期コードのパフォーマンス コストを下回る必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-663">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="4e90b-664">ログ記録のデータ ストアが低速の場合は、そこへ直接書き込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-664">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="4e90b-665">まずログ メッセージを高速なストアに書き込んでから、後で低速なストアに移動する方法を検討してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-665">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="4e90b-666">たとえば、SQL Server にログインしている場合、それを `Log` メソッドで直接実行したくはないでしょう。`Log` が同期メソッドであるためです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-666">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="4e90b-667">代わりに、ログ メッセージをインメモリ キューに同期的に追加し、バックグラウンド ワーカーにキューからメッセージをプルさせて、SQL Server にデータをプッシュする非同期処理を実行させます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-667">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="4e90b-668">詳細については、[この](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub の問題を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-668">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="4e90b-669">構成</span><span class="sxs-lookup"><span data-stu-id="4e90b-669">Configuration</span></span>

<span data-ttu-id="4e90b-670">ログ プロバイダーの構成は、1 つまたは複数の構成プロバイダーによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-670">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="4e90b-671">ファイル形式 (INI、JSON、および XML)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-671">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="4e90b-672">コマンド ライン引数。</span><span class="sxs-lookup"><span data-stu-id="4e90b-672">Command-line arguments.</span></span>
* <span data-ttu-id="4e90b-673">環境変数。</span><span class="sxs-lookup"><span data-stu-id="4e90b-673">Environment variables.</span></span>
* <span data-ttu-id="4e90b-674">メモリ内 .NET オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="4e90b-674">In-memory .NET objects.</span></span>
* <span data-ttu-id="4e90b-675">暗号化されていない[シークレット マネージャー](xref:security/app-secrets)の記憶域。</span><span class="sxs-lookup"><span data-stu-id="4e90b-675">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="4e90b-676">[Azure Key Vault](xref:security/key-vault-configuration) などの暗号化されたユーザー ストア。</span><span class="sxs-lookup"><span data-stu-id="4e90b-676">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="4e90b-677">カスタム プロバイダー (インストール済みまたは作成済み)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-677">Custom providers (installed or created).</span></span>

<span data-ttu-id="4e90b-678">たとえば、一般的に、ログの構成はアプリ設定ファイルの `Logging` セクションで指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-678">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="4e90b-679">次の例は、一般的な *appsettings.Development.json* ファイルの内容を示しています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-679">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="4e90b-680">`Logging` プロパティには `LogLevel` およびログ プロバイダーのプロパティ (Console が示されています) を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-680">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="4e90b-681">`Logging` の下の `LogLevel` プロパティでは、選択したカテゴリに対するログの最小の[レベル](#log-level)が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-681">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="4e90b-682">この例では、`System` と `Microsoft` カテゴリが `Information` レベルで、その他はすべて `Debug` レベルでログに記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-682">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="4e90b-683">`Logging` の下のその他のプロパティではログ プロバイダーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-683">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="4e90b-684">この例では、Console プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-684">The example is for the Console provider.</span></span> <span data-ttu-id="4e90b-685">プロバイダーで[ログのスコープ](#log-scopes)がサポートされている場合、`IncludeScopes` によってそれを有効にするかどうかが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-685">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="4e90b-686">プロバイダーのプロパティ (例の `Console` など) では、`LogLevel` プロパティが指定される場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-686">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="4e90b-687">プロバイダーの下の `LogLevel` では、そのプロバイダーのログのレベルが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-687">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="4e90b-688">`Logging.{providername}.LogLevel` でレベルが指定される場合、それによって `Logging.LogLevel` で設定されたものはすべてオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-688">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span> <span data-ttu-id="4e90b-689">たとえば、次の JSON について考えます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-689">For example, consider the following JSON:</span></span>

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<span data-ttu-id="4e90b-690">上記の JSON では、`Console` プロバイダーの設定によって上記の (既定の) ログ レベルがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-690">In the preceding JSON, the `Console` provider settings overrides the preceding (default) log level.</span></span>

<span data-ttu-id="4e90b-691">ログ API には、アプリの実行中にログ レベルを変更するシナリオは含まれていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-691">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="4e90b-692">ただし、一部の構成プロバイダーは構成を再読み込みすることができ、ログ構成に直ちに影響します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-692">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="4e90b-693">たとえば、設定ファイルを読み取るために `CreateDefaultBuilder` によって追加される [ファイル構成プロバイダー](xref:fundamentals/configuration/index#file-configuration-provider)は、既定でログ構成を再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-693">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="4e90b-694">アプリの実行中にコードの構成が変更された場合、アプリは [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) を呼び出して、アプリのログ構成を更新できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-694">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="4e90b-695">構成プロバイダーの実装について詳しくは、<xref:fundamentals/configuration/index> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-695">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="4e90b-696">サンプルのログ記録の出力</span><span class="sxs-lookup"><span data-stu-id="4e90b-696">Sample logging output</span></span>

<span data-ttu-id="4e90b-697">前のセクションで紹介したサンプル コードでは、コマンド ラインからアプリを実行するとコンソールにログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-697">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="4e90b-698">コンソールの出力例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-698">Here's an example of console output:</span></span>

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

<span data-ttu-id="4e90b-699">前のログは、`http://localhost:5000/api/todo/0` のサンプル アプリに向けて HTTP Get 要求を作成することで生成されました。</span><span class="sxs-lookup"><span data-stu-id="4e90b-699">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="4e90b-700">Visual Studio でサンプル アプリを実行したときに [デバッグ] ウィンドウに表示されるログと同じログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-700">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="4e90b-701">前のセクションで紹介した `ILogger` の呼び出しで作成されるログは、"TodoApi" から始まります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-701">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="4e90b-702">"Microsoft" カテゴリから始まるログは、ASP.NET Core のフレームワークのコードからのログです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-702">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="4e90b-703">ASP.NET Core とアプリケーション コードでは、同じログ API とログ プロバイダーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-703">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="4e90b-704">以降、この記事では、ログ記録の詳細とオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-704">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="4e90b-705">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="4e90b-705">NuGet packages</span></span>

<span data-ttu-id="4e90b-706">`ILogger` および `ILoggerFactory` インターフェイスは、[Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 内にあり、それらの既定の実装は [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 内にあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-706">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="4e90b-707">ログのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-707">Log category</span></span>

<span data-ttu-id="4e90b-708">`ILogger` オブジェクトが作成されるときに、"*カテゴリ*" が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-708">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="4e90b-709">このカテゴリは、`ILogger` のインスタンスによって作成される各ログ メッセージと共に含められます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-709">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="4e90b-710">カテゴリには任意の文字列を指定できますが、"TodoApi.Controllers.TodoController" などのクラス名を使用するのが慣例です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-710">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="4e90b-711">`ILogger<T>` を使用して、カテゴリとして `T` の完全修飾型名が使用される `ILogger` インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-711">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="4e90b-712">カテゴリを明示的に指定するには、`ILoggerFactory.CreateLogger` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-712">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="4e90b-713">`ILogger<T>` は、`T` の完全修飾型名を使用した `CreateLogger` の呼び出しと同じです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-713">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="4e90b-714">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-714">Log level</span></span>

<span data-ttu-id="4e90b-715">すべてのログで <xref:Microsoft.Extensions.Logging.LogLevel> 値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-715">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="4e90b-716">ログ レベルは、重大度または重要度を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-716">The log level indicates the severity or importance.</span></span> <span data-ttu-id="4e90b-717">たとえば、メソッドが正常に終了した場合は `Information` ログを、メソッドが *404 Not Found* 状態コードを返した場合は `Warning` ログを書き込む場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-717">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="4e90b-718">`Information` および `Warning` ログを作成するコードを次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-718">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="4e90b-719">上記のコードでは、`MyLogEvents.GetItem` パラメーターと `MyLogEvents.GetItemNotFound` パラメーターは [ログ イベント ID](#log-event-id)です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-719">In the preceding code, the `MyLogEvents.GetItem` and `MyLogEvents.GetItemNotFound` parameters are the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="4e90b-720">2 つ目のパラメーターは、他のメソッド パラメーターによって提供される引数値のプレースホルダーを含むメッセージ テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-720">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="4e90b-721">メソッド パラメーターについては、この記事の「[ログ メッセージ テンプレート](#lmt)」のセクションで説明されています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-721">The method parameters are explained in the [Log message template section](#lmt) in this article.</span></span>

<span data-ttu-id="4e90b-722">メソッド名にレベルを含むログ メソッド (たとえば `LogInformation` や `LogWarning`) は、[ILogger の拡張メソッド](xref:Microsoft.Extensions.Logging.LoggerExtensions)です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-722">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="4e90b-723">これらのメソッドでは、`LogLevel` パラメーターを受け取る `Log` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-723">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="4e90b-724">これらの拡張メソッドのいずれかではなく、`Log` メソッドを直接呼び出すことができますが、構文は比較的複雑です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-724">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="4e90b-725">詳細については、<xref:Microsoft.Extensions.Logging.ILogger> および[ロガー拡張ソース コード](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-725">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="4e90b-726">ASP.NET Core には、次のログ レベルが定義されています (重大度の低いものから高い順)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-726">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="4e90b-727">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="4e90b-727">Trace = 0</span></span>

  <span data-ttu-id="4e90b-728">通常はデバッグでのみ役立つ情報の場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-728">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="4e90b-729">これらのメッセージには機密性の高いアプリケーション データが含まれる可能性があるため、運用環境は有効にしないことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-729">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="4e90b-730">*既定で無効です。*</span><span class="sxs-lookup"><span data-stu-id="4e90b-730">*Disabled by default.*</span></span>

* <span data-ttu-id="4e90b-731">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="4e90b-731">Debug = 1</span></span>

  <span data-ttu-id="4e90b-732">開発とデバッグで役立つ可能性がある情報の場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-732">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="4e90b-733">例:`Entering method Configure with flag set to true.``Debug` レベルのログは、ログのサイズが大きくなるため、トラブルシューティングの場合を除き運用環境では有効にしません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-733">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="4e90b-734">Information = 2</span><span class="sxs-lookup"><span data-stu-id="4e90b-734">Information = 2</span></span>

  <span data-ttu-id="4e90b-735">アプリの一般的なフローを追跡する場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-735">For tracking the general flow of the app.</span></span> <span data-ttu-id="4e90b-736">通常、これらのログには、長期的な値があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-736">These logs typically have some long-term value.</span></span> <span data-ttu-id="4e90b-737">例 : `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="4e90b-737">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="4e90b-738">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="4e90b-738">Warning = 3</span></span>

  <span data-ttu-id="4e90b-739">アプリのフローで異常なイベントや予期しないイベントが発生した場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-739">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="4e90b-740">アプリの停止の原因にはならないが、調査する必要があるエラーやその他の状態がここに含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-740">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="4e90b-741">`Warning` ログ レベルが使用される一般的な場所として、例外の処理があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-741">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="4e90b-742">例 : `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="4e90b-742">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="4e90b-743">Error = 4</span><span class="sxs-lookup"><span data-stu-id="4e90b-743">Error = 4</span></span>

  <span data-ttu-id="4e90b-744">処理できないエラーと例外の場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-744">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="4e90b-745">これらのメッセージは、アプリ全体のエラーではなく、現在のアクティビティまたは操作 (現在の HTTP 要求など) におけるエラーを示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-745">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="4e90b-746">ログ メッセージの例: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="4e90b-746">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="4e90b-747">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="4e90b-747">Critical = 5</span></span>

  <span data-ttu-id="4e90b-748">即時の注意が必要なエラーの場合。</span><span class="sxs-lookup"><span data-stu-id="4e90b-748">For failures that require immediate attention.</span></span> <span data-ttu-id="4e90b-749">例: データ損失のシナリオ、ディスク領域不足。</span><span class="sxs-lookup"><span data-stu-id="4e90b-749">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="4e90b-750">ログ レベルを使用して、特定のストレージ メディアまたは表示ウィンドウに書き込むログの出力量を制御します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-750">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="4e90b-751">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-751">For example:</span></span>

* <span data-ttu-id="4e90b-752">運用環境:</span><span class="sxs-lookup"><span data-stu-id="4e90b-752">In production:</span></span>
  * <span data-ttu-id="4e90b-753">`Trace` から `Information` までのレベルでログを記録すると、詳細なログ メッセージが大量に生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-753">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="4e90b-754">コストを制御し、データ ストレージの上限を超えないようにするには、`Trace` から `Information` のレベルのメッセージを、大量の低コストのデータ ストアに記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-754">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="4e90b-755">`Warning` から `Critical` までのレベルでログを記録すると、通常はより少ないログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-755">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="4e90b-756">そのため、コストとストレージの制限は通常は問題にならないため、データ ストアの選択肢がより柔軟になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-756">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="4e90b-757">開発中:</span><span class="sxs-lookup"><span data-stu-id="4e90b-757">During development:</span></span>
  * <span data-ttu-id="4e90b-758">コンソールに `Warning` から `Critical` のメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-758">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="4e90b-759">トラブルシューティングの際に `Trace` から `Information` のメッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-759">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="4e90b-760">この記事で後述する「[ログのフィルター処理](#log-filtering)」セクションでは、プロバイダーで処理するログ レベルの制御方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-760">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="4e90b-761">ASP.NET Core では、フレームワーク イベントのログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-761">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="4e90b-762">この記事の前のログの例では、`Information` レベル以下のログを除外したため、`Debug` または `Trace` レベルのログは作成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="4e90b-762">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="4e90b-763">`Debug` ログを示すために構成したサンプル アプリを実行することで生成されるコンソールのログの例を、次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-763">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="4e90b-764">ログ イベント ID</span><span class="sxs-lookup"><span data-stu-id="4e90b-764">Log event ID</span></span>

<span data-ttu-id="4e90b-765">各ログで "*イベント ID*" を指定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-765">Each log can specify an *event ID*.</span></span> <span data-ttu-id="4e90b-766">サンプル アプリでは、この処理にローカルで定義された `LoggingEvents` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-766">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="4e90b-767">イベント ID によって一連のイベントが関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-767">An event ID associates a set of events.</span></span> <span data-ttu-id="4e90b-768">たとえば、ページ上に項目の一覧を表示する機能に関連するすべてのログを 1001 に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-768">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="4e90b-769">ログ プロバイダーでは、ID フィールドやログ メッセージにイベント ID が格納されたり、またはまったく格納されなかったりする場合があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-769">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="4e90b-770">Debug プロバイダーでイベント ID が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-770">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="4e90b-771">Console プロバイダーでは、カテゴリの後のブラケット内にイベント ID が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-771">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="4e90b-772">ログ メッセージ テンプレート</span><span class="sxs-lookup"><span data-stu-id="4e90b-772">Log message template</span></span>

<span data-ttu-id="4e90b-773">各ログでメッセージ テンプレートが指定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-773">Each log specifies a message template.</span></span> <span data-ttu-id="4e90b-774">メッセージ テンプレートには、指定される引数のためのプレースホルダーを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-774">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="4e90b-775">プレースホルダーには、数値ではなく名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-775">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="4e90b-776">プレースホルダーの名前ではなく、プレースホルダーの順序によって、値の指定に使用されるパラメーターが決まります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-776">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="4e90b-777">次のコードでは、パラメーター名がメッセージ テンプレート内のシーケンスの外にあることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-777">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="4e90b-778">このコードでは、シーケンス内のパラメーター値を含むログ メッセージが作成されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-778">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="4e90b-779">ログ プロバイダーが[セマンティック ログ記録 (または構造化ログ記録)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)を実装できるようにするために、ログ フレームワークはこのように動作します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-779">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="4e90b-780">書式設定されたメッセージ テンプレートだけでなく、引数自体がログ システムに渡されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-780">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="4e90b-781">この情報により、ログ プロバイダーはフィールドとしてパラメーター値を格納することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-781">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="4e90b-782">たとえば、つぎのようなロガー メソッドの呼び出しがあるとします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-782">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="4e90b-783">Azure Table Storage にログを送信する場合、各 Azure Table エンティティに `ID` および `RequestTime` プロパティを指定し、ログ データのクエリを簡略化することができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-783">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="4e90b-784">クエリによって指定した `RequestTime` の範囲内のすべてのログを検索できます。テキスト メッセージから時間を解析する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-784">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="4e90b-785">ログ記録の例外</span><span class="sxs-lookup"><span data-stu-id="4e90b-785">Logging exceptions</span></span>

<span data-ttu-id="4e90b-786">ロガー メソッドには、次の例のように、例外で渡すことができるオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-786">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="4e90b-787">プロバイダーごとに、例外情報の処理方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-787">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="4e90b-788">前述のコードの Debug プロバイダーの出力の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-788">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="4e90b-789">ログのフィルター処理</span><span class="sxs-lookup"><span data-stu-id="4e90b-789">Log filtering</span></span>

<span data-ttu-id="4e90b-790">特定のプロバイダーとカテゴリ、またはすべてのプロバイダーまたはすべてのカテゴリに最小ログ レベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-790">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="4e90b-791">最小レベルを下回るログは、そのプロバイダーに渡されないので、表示または保存されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-791">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="4e90b-792">すべてのログを抑制するには、最小ログ レベルに `LogLevel.None` を指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-792">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="4e90b-793">`LogLevel.None` の整数値は 6 であり、`LogLevel.Critical` (5) を超えます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-793">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="4e90b-794">構成にフィルター規則を作成する</span><span class="sxs-lookup"><span data-stu-id="4e90b-794">Create filter rules in configuration</span></span>

<span data-ttu-id="4e90b-795">プロジェクト テンプレート コードは `CreateDefaultBuilder` を呼び出して、コンソール、デバッグ、EventSource (ASP.NET Core 2.2 以降) のプロバイダーのログ記録を設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-795">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="4e90b-796">`Logging`この記事で既に説明[したように、`CreateDefaultBuilder` メソッドでは、](#configuration) セクションで構成を検索するようにログが設定されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-796">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="4e90b-797">次の例のように、構成データでは、プロバイダーとカテゴリごとに最小ログ レベルを指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-797">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="4e90b-798">この JSON では、6 個のフィルター規則を作成します。1 つは Debug プロバイダー用、4 つは Console プロバイダー用、1 つはすべてのプロバイダー用です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-798">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="4e90b-799">`ILogger` オブジェクトが作成されると、各プロバイダーに対して 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-799">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="4e90b-800">コードのフィルター規則</span><span class="sxs-lookup"><span data-stu-id="4e90b-800">Filter rules in code</span></span>

<span data-ttu-id="4e90b-801">コードにフィルター規則を登録する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-801">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="4e90b-802">2 つ目の `AddFilter` では、プロバイダーの種類名を使用して Debug プロバイダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-802">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="4e90b-803">1 つ目の `AddFilter` は、プロバイダーの種類を指定していないため、すべてのプロバイダーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-803">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="4e90b-804">フィルター規則を適用する方法</span><span class="sxs-lookup"><span data-stu-id="4e90b-804">How filtering rules are applied</span></span>

<span data-ttu-id="4e90b-805">前の例の構成データと `AddFilter` コードでは、次の表に示す規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-805">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="4e90b-806">最初の 6 つは構成例、最後の 2 つはコード例のものです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-806">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="4e90b-807">数値</span><span class="sxs-lookup"><span data-stu-id="4e90b-807">Number</span></span> | <span data-ttu-id="4e90b-808">プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-808">Provider</span></span>      | <span data-ttu-id="4e90b-809">以下から始まるカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-809">Categories that begin with ...</span></span>          | <span data-ttu-id="4e90b-810">最小ログ レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-810">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="4e90b-811">1</span><span class="sxs-lookup"><span data-stu-id="4e90b-811">1</span></span>      | <span data-ttu-id="4e90b-812">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-812">Debug</span></span>         | <span data-ttu-id="4e90b-813">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-813">All categories</span></span>                          | <span data-ttu-id="4e90b-814">情報</span><span class="sxs-lookup"><span data-stu-id="4e90b-814">Information</span></span>       |
| <span data-ttu-id="4e90b-815">2</span><span class="sxs-lookup"><span data-stu-id="4e90b-815">2</span></span>      | <span data-ttu-id="4e90b-816">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-816">Console</span></span>       | <span data-ttu-id="4e90b-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="4e90b-817">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="4e90b-818">警告</span><span class="sxs-lookup"><span data-stu-id="4e90b-818">Warning</span></span>           |
| <span data-ttu-id="4e90b-819">3</span><span class="sxs-lookup"><span data-stu-id="4e90b-819">3</span></span>      | <span data-ttu-id="4e90b-820">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-820">Console</span></span>       | <span data-ttu-id="4e90b-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="4e90b-821">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="4e90b-822">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-822">Debug</span></span>             |
| <span data-ttu-id="4e90b-823">4</span><span class="sxs-lookup"><span data-stu-id="4e90b-823">4</span></span>      | <span data-ttu-id="4e90b-824">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-824">Console</span></span>       | <span data-ttu-id="4e90b-825">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="4e90b-825">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="4e90b-826">Error</span><span class="sxs-lookup"><span data-stu-id="4e90b-826">Error</span></span>             |
| <span data-ttu-id="4e90b-827">5</span><span class="sxs-lookup"><span data-stu-id="4e90b-827">5</span></span>      | <span data-ttu-id="4e90b-828">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-828">Console</span></span>       | <span data-ttu-id="4e90b-829">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-829">All categories</span></span>                          | <span data-ttu-id="4e90b-830">情報</span><span class="sxs-lookup"><span data-stu-id="4e90b-830">Information</span></span>       |
| <span data-ttu-id="4e90b-831">6</span><span class="sxs-lookup"><span data-stu-id="4e90b-831">6</span></span>      | <span data-ttu-id="4e90b-832">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-832">All providers</span></span> | <span data-ttu-id="4e90b-833">すべてのカテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-833">All categories</span></span>                          | <span data-ttu-id="4e90b-834">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-834">Debug</span></span>             |
| <span data-ttu-id="4e90b-835">7</span><span class="sxs-lookup"><span data-stu-id="4e90b-835">7</span></span>      | <span data-ttu-id="4e90b-836">すべてのプロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-836">All providers</span></span> | <span data-ttu-id="4e90b-837">システム</span><span class="sxs-lookup"><span data-stu-id="4e90b-837">System</span></span>                                  | <span data-ttu-id="4e90b-838">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-838">Debug</span></span>             |
| <span data-ttu-id="4e90b-839">8</span><span class="sxs-lookup"><span data-stu-id="4e90b-839">8</span></span>      | <span data-ttu-id="4e90b-840">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-840">Debug</span></span>         | <span data-ttu-id="4e90b-841">Microsoft</span><span class="sxs-lookup"><span data-stu-id="4e90b-841">Microsoft</span></span>                               | <span data-ttu-id="4e90b-842">トレース</span><span class="sxs-lookup"><span data-stu-id="4e90b-842">Trace</span></span>             |

<span data-ttu-id="4e90b-843">`ILogger` オブジェクトを作成すると、`ILoggerFactory` オブジェクトによって、そのロガーに適用するプロバイダーごとに 1 つの規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-843">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="4e90b-844">`ILogger` インスタンスによって書き込まれるすべてのメッセージは、選択した規則に基づいてフィルター処理されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-844">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="4e90b-845">使用できる規則から、各プロバイダーとカテゴリのペアごとに該当する最も限定的な規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-845">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="4e90b-846">特定のカテゴリに `ILogger` が作成されるときに、各プロバイダーに次のアルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-846">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="4e90b-847">プロバイダーとそのエイリアスと一致するすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-847">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="4e90b-848">一致が見つからない場合は、空のプロバイダーですべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-848">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="4e90b-849">前の手順の結果、最も長いカテゴリのプレフィックスが一致する規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-849">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="4e90b-850">一致が見つからない場合は、カテゴリを指定しないすべての規則が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-850">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="4e90b-851">複数の規則が選択されている場合は、**最後**の 1 つが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-851">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="4e90b-852">規則が選択されていない場合は、`MinimumLevel` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-852">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="4e90b-853">前の規則一覧を使用して、カテゴリ "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine" に `ILogger` オブジェクトを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-853">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="4e90b-854">Debug プロバイダーの場合、規則 1、6、8 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-854">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="4e90b-855">規則 8 が最も限定的なので、規則 8 が選択されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-855">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="4e90b-856">Console プロバイダーの場合、規則 3、4、5、6 が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-856">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="4e90b-857">規則 3 が最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-857">Rule 3 is most specific.</span></span>

<span data-ttu-id="4e90b-858">作成される `ILogger` インスタンスでは、Debug プロバイダーに `Trace` レベル以上のログが送信されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-858">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="4e90b-859">`Debug` レベル以上のログが Console プロバイダーに送信されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-859">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="4e90b-860">プロバイダーのエイリアス</span><span class="sxs-lookup"><span data-stu-id="4e90b-860">Provider aliases</span></span>

<span data-ttu-id="4e90b-861">各プロバイダーでは "*エイリアス*" が定義されます。これは構成で完全修飾型名の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-861">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="4e90b-862">組み込みのプロバイダーの場合は、次のエイリアスを使用してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-862">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="4e90b-863">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-863">Console</span></span>
* <span data-ttu-id="4e90b-864">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-864">Debug</span></span>
* <span data-ttu-id="4e90b-865">EventSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-865">EventSource</span></span>
* <span data-ttu-id="4e90b-866">EventLog</span><span class="sxs-lookup"><span data-stu-id="4e90b-866">EventLog</span></span>
* <span data-ttu-id="4e90b-867">TraceSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-867">TraceSource</span></span>
* <span data-ttu-id="4e90b-868">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="4e90b-868">AzureAppServicesFile</span></span>
* <span data-ttu-id="4e90b-869">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="4e90b-869">AzureAppServicesBlob</span></span>
* <span data-ttu-id="4e90b-870">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="4e90b-870">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="4e90b-871">既定の最小レベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-871">Default minimum level</span></span>

<span data-ttu-id="4e90b-872">指定したプロバイダーとカテゴリに適用される構成またはコードの規則がない場合にのみ反映される最小レベルの設定があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-872">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="4e90b-873">最小レベルを設定する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-873">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="4e90b-874">最小レベルを明示的に設定しない場合、既定値は `Information` です。これは、`Trace` および `Debug` ログが無視されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-874">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="4e90b-875">フィルター関数</span><span class="sxs-lookup"><span data-stu-id="4e90b-875">Filter functions</span></span>

<span data-ttu-id="4e90b-876">フィルター関数は、構成またはコードによって規則が割り当てられていないすべてのプロバイダーとカテゴリについて呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-876">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="4e90b-877">関数内のコードから、プロバイダーの種類、カテゴリ、ログ レベルにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-877">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="4e90b-878">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-878">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="4e90b-879">システムのカテゴリとレベル</span><span class="sxs-lookup"><span data-stu-id="4e90b-879">System categories and levels</span></span>

<span data-ttu-id="4e90b-880">ASP.NET Core と Entity Framework Core によって使用されるいくつかのカテゴリと、それらから想定されるログの種類に関するメモを、次に示します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-880">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="4e90b-881">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="4e90b-881">Category</span></span>                            | <span data-ttu-id="4e90b-882">メモ</span><span class="sxs-lookup"><span data-stu-id="4e90b-882">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="4e90b-883">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="4e90b-883">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="4e90b-884">ASP.NET Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-884">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="4e90b-885">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="4e90b-885">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="4e90b-886">どのキーが検討、検索、および使用されたか。</span><span class="sxs-lookup"><span data-stu-id="4e90b-886">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="4e90b-887">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="4e90b-887">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="4e90b-888">許可されるホスト。</span><span class="sxs-lookup"><span data-stu-id="4e90b-888">Hosts allowed.</span></span> |
| <span data-ttu-id="4e90b-889">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="4e90b-889">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="4e90b-890">HTTP 要求が完了するまでにかかった時間と、それらの開始時刻。</span><span class="sxs-lookup"><span data-stu-id="4e90b-890">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="4e90b-891">どのホスティング スタートアップ アセンブリが読み込まれたか。</span><span class="sxs-lookup"><span data-stu-id="4e90b-891">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="4e90b-892">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="4e90b-892">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="4e90b-893">MVC と Razor の診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-893">MVC and Razor diagnostics.</span></span> <span data-ttu-id="4e90b-894">モデルの構築、フィルター処理の実行、ビューのコンパイル、アクションの選択。</span><span class="sxs-lookup"><span data-stu-id="4e90b-894">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="4e90b-895">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="4e90b-895">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="4e90b-896">一致する情報をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="4e90b-896">Route matching information.</span></span> |
| <span data-ttu-id="4e90b-897">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="4e90b-897">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="4e90b-898">接続の開始、停止、キープ アライブ応答。</span><span class="sxs-lookup"><span data-stu-id="4e90b-898">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="4e90b-899">HTTPS 証明書情報。</span><span class="sxs-lookup"><span data-stu-id="4e90b-899">HTTPS certificate information.</span></span> |
| <span data-ttu-id="4e90b-900">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="4e90b-900">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="4e90b-901">提供されるファイル。</span><span class="sxs-lookup"><span data-stu-id="4e90b-901">Files served.</span></span> |
| <span data-ttu-id="4e90b-902">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4e90b-902">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="4e90b-903">Entity Framework Core の一般的な診断。</span><span class="sxs-lookup"><span data-stu-id="4e90b-903">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="4e90b-904">データベースのアクティビティと構成、変更の検出、移行。</span><span class="sxs-lookup"><span data-stu-id="4e90b-904">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="4e90b-905">ログのスコープ</span><span class="sxs-lookup"><span data-stu-id="4e90b-905">Log scopes</span></span>

 <span data-ttu-id="4e90b-906">"*スコープ*" では、論理操作のセットをグループ化できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-906">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="4e90b-907">このグループ化を使用して、セットの一部として作成される各ログに同じデータをアタッチすることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-907">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="4e90b-908">たとえば、トランザクション処理の一部として作成されるすべてのログに、トランザクション ID を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-908">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="4e90b-909">スコープは <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> メソッドから返される `IDisposable` の種類であり、破棄されるまで継続します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-909">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="4e90b-910">ロガーの呼び出しを `using` ブロックでラップすることによって、スコープを使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-910">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="4e90b-911">次のコードでは、Console プロバイダーのスコープを有効にしています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-911">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="4e90b-912">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4e90b-912">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="4e90b-913">スコープベースのログ記録を有効にするには、`IncludeScopes` コンソールのロガー オプションを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-913">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="4e90b-914">構成について詳しくは、「[構成](#configuration)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-914">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="4e90b-915">各ログ メッセージには、スコープ内の情報が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-915">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="4e90b-916">組み込みのログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-916">Built-in logging providers</span></span>

<span data-ttu-id="4e90b-917">ASP.NET Core には次のプロバイダーが付属しています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-917">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="4e90b-918">コンソール</span><span class="sxs-lookup"><span data-stu-id="4e90b-918">Console</span></span>](#console-provider)
* [<span data-ttu-id="4e90b-919">デバッグ</span><span class="sxs-lookup"><span data-stu-id="4e90b-919">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="4e90b-920">EventSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-920">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="4e90b-921">EventLog</span><span class="sxs-lookup"><span data-stu-id="4e90b-921">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="4e90b-922">TraceSource</span><span class="sxs-lookup"><span data-stu-id="4e90b-922">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="4e90b-923">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="4e90b-923">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="4e90b-924">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="4e90b-924">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="4e90b-925">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="4e90b-925">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="4e90b-926">ASP.NET Core モジュールを使用した stdout およびデバッグ ログの詳細については、「<xref:test/troubleshoot-azure-iis>」および「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-926">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="4e90b-927">Console プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-927">Console provider</span></span>

<span data-ttu-id="4e90b-928">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) プロバイダー パッケージは、ログ出力をコンソールに送信します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-928">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="4e90b-929">コンソールのログ出力を確認するには、プロジェクト フォルダーでコマンド プロンプトを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-929">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="4e90b-930">Debug プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-930">Debug provider</span></span>

<span data-ttu-id="4e90b-931">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) プロバイダー パッケージは、[System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) クラス (`Debug.WriteLine` メソッドの呼び出し) を使用してログの出力を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-931">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="4e90b-932">Linux では、このプロバイダーから */var/log/message* にログが書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-932">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="4e90b-933">イベント ソース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-933">Event Source provider</span></span>

<span data-ttu-id="4e90b-934">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) プロバイダー パッケージでは、`Microsoft-Extensions-Logging` という名前でイベント ソース クロスプラットフォームに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-934">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="4e90b-935">Windows では、プロバイダーによって [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-935">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="4e90b-936">イベント ソース プロバイダーは、ホストをビルドするために `CreateDefaultBuilder` が呼び出されたときに、自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-936">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="4e90b-937">ログの収集と表示には、[PerfView ユーティリティ](https://github.com/Microsoft/perfview)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-937">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="4e90b-938">ETW ログを表示できる他のツールはありますが、ASP.NET Core から出力される ETW イベントを操作する場合、PerfView は最適なエクスペリエンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-938">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="4e90b-939">このプロバイダーでログに記録されるイベントを収集するように PerfView を構成するには、 **[追加プロバイダー]** の一覧に文字列 `*Microsoft-Extensions-Logging` を追加します</span><span class="sxs-lookup"><span data-stu-id="4e90b-939">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="4e90b-940">(文字列の先頭に忘れずにアスタリスクを付けてください)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-940">(Don't miss the asterisk at the start of the string.)</span></span>

![Perfview の追加プロバイダー](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="4e90b-942">Windows EventLog プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-942">Windows EventLog provider</span></span>

<span data-ttu-id="4e90b-943">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) プロバイダー パッケージは、ログ出力を Windows イベント ログに送信します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-943">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="4e90b-944">[AddEventLog のオーバーロード](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)を使用すると、<xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-944">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="4e90b-945">`null` または指定しない場合は、次の既定の設定が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-945">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="4e90b-946">`LogName`:"Application"</span><span class="sxs-lookup"><span data-stu-id="4e90b-946">`LogName`: "Application"</span></span>
* <span data-ttu-id="4e90b-947">`SourceName`: ".NET Runtime"</span><span class="sxs-lookup"><span data-stu-id="4e90b-947">`SourceName`: ".NET Runtime"</span></span>
* <span data-ttu-id="4e90b-948">`MachineName`:ローカル コンピューター名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-948">`MachineName`: The local machine name is used.</span></span>

<span data-ttu-id="4e90b-949">[警告レベル以上](#log-level)のイベントがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-949">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="4e90b-950">次の例では、イベント ログの既定のログ レベルを <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-950">The following example sets the Event Log default log level to <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>:</span></span>

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="4e90b-951">TraceSource プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-951">TraceSource provider</span></span>

<span data-ttu-id="4e90b-952">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) プロバイダー パッケージでは、<xref:System.Diagnostics.TraceSource> ライブラリとプロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-952">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="4e90b-953">[AddTraceSource オーバーロード](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions)を使用すると、ソース スイッチとトレース リスナーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-953">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="4e90b-954">このプロバイダーを使用するには、アプリを (.NET Core ではなく) .NET Framework 上で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-954">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="4e90b-955">このプロバイダーでは、サンプル アプリで使用されている <xref:System.Diagnostics.TextWriterTraceListener> など、さまざまな[リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)にメッセージをルーティングさせることができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-955">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="4e90b-956">Azure App Service プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-956">Azure App Service provider</span></span>

<span data-ttu-id="4e90b-957">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) プロバイダー パッケージは、Azure App Service アプリのファイル システムのテキスト ファイルと、Azure Storage アカウントの [BLOB ストレージ](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-957">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="4e90b-958">プロバイダー パッケージは、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)には含まれません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-958">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="4e90b-959">.NET Framework をターゲットとする場合、または `Microsoft.AspNetCore.App` を参照している場合は、プロバイダー パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-959">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="4e90b-960"><xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> のオーバーロードによって <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-960">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="4e90b-961">設定オブジェクトで既定の設定をオーバーライドできます。たとえば、ログの出力テンプレート、BLOB 名、ファイル サイズの制限などです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-961">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="4e90b-962">("*出力テンプレート*" は、`ILogger` メソッドの呼び出しと共に指定されるものに加えて、すべてのログに適用されるメッセージ テンプレートです。)</span><span class="sxs-lookup"><span data-stu-id="4e90b-962">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="4e90b-963">アプリケーションを App Service アプリにデプロイすると、Azure portal の **[App Service]** ページの [[App Service ログ]](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) セクションで指定された設定が適用されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-963">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="4e90b-964">次の設定が更新されると、アプリの再起動や再デプロイを必要とせずに、変更がすぐに有効になります。</span><span class="sxs-lookup"><span data-stu-id="4e90b-964">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="4e90b-965">**[アプリケーション ログ (ファイル システム)]**</span><span class="sxs-lookup"><span data-stu-id="4e90b-965">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="4e90b-966">**[アプリケーション ログ (BLOB)]**</span><span class="sxs-lookup"><span data-stu-id="4e90b-966">**Application Logging (Blob)**</span></span>

<span data-ttu-id="4e90b-967">ログ ファイルの既定の場所は、*D:\\home\\LogFiles\\Application* です。既定のファイル名は *diagnostics-yyyymmdd.txt* です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-967">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="4e90b-968">既定のファイル サイズ制限は 10 MB です。保持されるファイルの既定の最大数は 2 です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-968">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="4e90b-969">既定の BLOB 名は *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt* です。</span><span class="sxs-lookup"><span data-stu-id="4e90b-969">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="4e90b-970">このプロバイダーは、プロジェクトが Azure 環境で実行される場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-970">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="4e90b-971">プロジェクトをローカルで実行しても、効果はありません&mdash;BLOB のローカル ファイルまたはローカル開発ストレージへの書き込みは行われません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-971">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="4e90b-972">Azure ログのストリーミング</span><span class="sxs-lookup"><span data-stu-id="4e90b-972">Azure log streaming</span></span>

<span data-ttu-id="4e90b-973">Azure ログのストリーミングを使用すると、以下からリアル タイムでログ アクティビティを確認できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-973">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="4e90b-974">アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="4e90b-974">The app server</span></span>
* <span data-ttu-id="4e90b-975">Web サーバー</span><span class="sxs-lookup"><span data-stu-id="4e90b-975">The web server</span></span>
* <span data-ttu-id="4e90b-976">失敗した要求のトレース</span><span class="sxs-lookup"><span data-stu-id="4e90b-976">Failed request tracing</span></span>

<span data-ttu-id="4e90b-977">Azure ログのストリーミングを構成するには</span><span class="sxs-lookup"><span data-stu-id="4e90b-977">To configure Azure log streaming:</span></span>

* <span data-ttu-id="4e90b-978">アプリのポータル ページから **[App Service ログ]** ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-978">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="4e90b-979">**[アプリケーション ログ (ファイル システム)]** を **[オン]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-979">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="4e90b-980">ログ **[レベル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-980">Choose the log **Level**.</span></span> <span data-ttu-id="4e90b-981">この設定は、Azure ログのストリーミングにのみ適用され、アプリ内の他のログ プロバイダーには適用されません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-981">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="4e90b-982">**[ログ ストリーム]** ページに移動して、アプリのメッセージを確認します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-982">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="4e90b-983">これらはアプリによって、`ILogger` インターフェイスを介してログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-983">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="4e90b-984">Azure Application Insights のトレース ログ</span><span class="sxs-lookup"><span data-stu-id="4e90b-984">Azure Application Insights trace logging</span></span>

<span data-ttu-id="4e90b-985">[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) プロバイダー パッケージでは、Azure Application Insights にログを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-985">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="4e90b-986">Application Insights は、Web アプリを監視するサービスであり、クエリを実行してテレメトリ データを分析するためのツールを提供します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-986">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="4e90b-987">このプロバイダーを使用する場合は、Application Insights ツールを使ってクエリを実行し、ログを分析できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-987">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="4e90b-988">ログ プロバイダーは、[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) の依存関係として組み込まれており、ASP.NET Core で利用可能なすべてのテレメトリを提供するパッケージです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-988">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="4e90b-989">このパッケージを使用する場合は、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-989">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="4e90b-990">ASP.NET 4.x. に対応している [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) パッケージを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-990">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="4e90b-991">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-991">For more information, see the following resources:</span></span>

* [<span data-ttu-id="4e90b-992">Application Insights の概要</span><span class="sxs-lookup"><span data-stu-id="4e90b-992">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="4e90b-993">[Application Insights for ASP.NET Core アプリケーション](/azure/azure-monitor/app/asp-net-core) - ログ記録と共に完全な Application Insights テレメトリを実装する場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-993">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="4e90b-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger ログ](/azure/azure-monitor/app/ilogger) - ログ プロバイダーを実装し、Application Insights テレメトリのそれ以外の部分は除く場合は、ここから開始します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-994">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="4e90b-995">[Application Insights のログ アダプター](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。</span><span class="sxs-lookup"><span data-stu-id="4e90b-995">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="4e90b-996">[Application Insights SDK のインストール、構成、および初期化](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn サイト上にある対話型のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="4e90b-996">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="4e90b-997">サードパーティ製のログ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4e90b-997">Third-party logging providers</span></span>

<span data-ttu-id="4e90b-998">ASP.NET Core で使用できるサードパーティ製のログ記録フレームワークをいくつか紹介します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-998">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="4e90b-999">[elmah.io](https://elmah.io/) ([GitHub リポジトリ](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-999">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub リポジトリ](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1000">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="4e90b-1001">[JSNLog](https://jsnlog.com/) ([GitHub リポジトリ](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1001">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="4e90b-1002">[KissLog.net](https://kisslog.net/) ([GitHub リポジトリ](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1002">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="4e90b-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub リポジトリ](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1003">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="4e90b-1004">[Loggr](https://loggr.net/) ([GitHub リポジトリ](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1004">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-1005">[NLog](https://nlog-project.org/) ([GitHub リポジトリ](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1005">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="4e90b-1006">[Sentry](https://sentry.io/welcome/) ([GitHub リポジトリ](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1006">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="4e90b-1007">[Serilog](https://serilog.net/) ([GitHub リポジトリ](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1007">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="4e90b-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github リポジトリ](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="4e90b-1008">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="4e90b-1009">一部のサードパーティ製フレームワークは、[セマンティック ログ記録 (構造化ログ記録とも呼ばれます)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) を実行できます。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1009">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="4e90b-1010">サード パーティ製フレームワークを使用することは、組み込みのプロバイダーのいずれかを使用することと似ています。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1010">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="4e90b-1011">プロジェクトに NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1011">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="4e90b-1012">ログ記録フレームワークによって提供される `ILoggerFactory` 拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1012">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="4e90b-1013">詳細については、各プロバイダーのドキュメントをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1013">For more information, see each provider's documentation.</span></span> <span data-ttu-id="4e90b-1014">サード パーティ製のログ プロバイダーは、Microsoft ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="4e90b-1014">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e90b-1015">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4e90b-1015">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
