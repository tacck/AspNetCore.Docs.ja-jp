---
title: 'ASP.NET Core でのログ記録と診断 :::no-loc(SignalR):::'
author: anurse
description: 'ASP.NET Core アプリから診断を収集する方法について説明 :::no-loc(SignalR)::: します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/diagnostics
ms.openlocfilehash: 890359c9e9f6c3c60f3105124f52c66b09a8a4fb
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690680"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="7f6d6-103">ASP.NET Core でのログ記録と診断 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="7f6d6-103">Logging and diagnostics in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="7f6d6-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7f6d6-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7f6d6-105">この記事では、ASP.NET Core アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供 :::no-loc(SignalR)::: します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-105">This article provides guidance for gathering diagnostics from your ASP.NET Core :::no-loc(SignalR)::: app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="7f6d6-106">サーバー側のログ記録</span><span class="sxs-lookup"><span data-stu-id="7f6d6-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="7f6d6-107">サーバー側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="7f6d6-108">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="7f6d6-109">:::no-loc(SignalR):::は ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-109">Since :::no-loc(SignalR)::: is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="7f6d6-110">既定の構成では、は :::no-loc(SignalR)::: ごくわずかな情報をログに記録しますが、これは構成できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-110">In the default configuration, :::no-loc(SignalR)::: logs very little information, but this can configured.</span></span> <span data-ttu-id="7f6d6-111">ASP.NET Core ログの構成の詳細については、[ASP.NET Core ログ](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="7f6d6-112">:::no-loc(SignalR)::: 2つの logger カテゴリを使用します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-112">:::no-loc(SignalR)::: uses two logger categories:</span></span>

* <span data-ttu-id="7f6d6-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: ハブプロトコルに関連するログ、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="7f6d6-114">`Microsoft.AspNetCore.Http.Connections`: Websocket、長いポーリング、Server-Sent イベント、低レベルのインフラストラクチャなどのトランスポートに関連するログ。 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="7f6d6-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level :::no-loc(SignalR)::: infrastructure.</span></span>

<span data-ttu-id="7f6d6-115">から詳細なログを有効にするには :::no-loc(SignalR)::: 、の `Debug` サブセクションに次の項目を追加して、上記のプレフィックスの両方を *appsettings.js* のファイルのレベルに構成し `LogLevel` `Logging` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-115">To enable detailed logs from :::no-loc(SignalR):::, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="7f6d6-116">これは、メソッドのコードで構成することもでき `CreateWebHostBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="7f6d6-117">JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.:::no-loc(SignalR):::` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="7f6d6-118">構成システムのドキュメントを調べて、入れ子になった構成値を指定する方法を確認してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="7f6d6-119">たとえば、環境変数の使用時には、`:` の代わりに 2 つの `_` 文字を使用します (例: `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`)。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`).</span></span>

<span data-ttu-id="7f6d6-120">アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="7f6d6-121">`Trace` レベルでは非常に低レベルの診断情報が生成されるため、アプリの問題を診断するために必要となることはまれです。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="7f6d6-122">サーバー側ログにアクセスする</span><span class="sxs-lookup"><span data-stu-id="7f6d6-122">Access server-side logs</span></span>

<span data-ttu-id="7f6d6-123">サーバー側のログにアクセスする方法は、実行環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="7f6d6-124">IIS の外部のコンソールアプリとして</span><span class="sxs-lookup"><span data-stu-id="7f6d6-124">As a console app outside IIS</span></span>

<span data-ttu-id="7f6d6-125">コンソール アプリで実行中の場合は、[コンソール ロガー](xref:fundamentals/logging/index#console)を既定で有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="7f6d6-126">:::no-loc(SignalR)::: ログはコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-126">:::no-loc(SignalR)::: logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="7f6d6-127">Visual Studio から IIS Express 内</span><span class="sxs-lookup"><span data-stu-id="7f6d6-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="7f6d6-128">Visual Studio では、[ **出力** ] ウィンドウにログ出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="7f6d6-129">[ **ASP.NET Core Web サーバー** ] ドロップダウンオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="7f6d6-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7f6d6-130">Azure App Service</span></span>

<span data-ttu-id="7f6d6-131">Azure App Service ポータルの [ **診断ログ** ] セクションで [ **アプリケーションログ (ファイルシステム)** ] オプションを有効にし、 **レベル** をに構成し `Verbose` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="7f6d6-132">ログは、 **ログストリーミング** サービスおよび App Service のファイルシステムのログで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="7f6d6-133">詳細については、「 [Azure ログストリーミング](xref:fundamentals/logging/index#azure-log-streaming)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="7f6d6-134">その他の環境</span><span class="sxs-lookup"><span data-stu-id="7f6d6-134">Other environments</span></span>

<span data-ttu-id="7f6d6-135">アプリが別の環境 (Docker、Kubernetes、Windows サービスなど) にデプロイされる場合は、その環境に適したログ プロバイダーを構成する方法についての詳細を、「<xref:fundamentals/logging/index>」で確認してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="7f6d6-136">JavaScript クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="7f6d6-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="7f6d6-137">クライアント側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="7f6d6-138">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="7f6d6-139">JavaScript クライアントを使用する場合は、でメソッドを使用してログオプションを構成でき `configureLogging` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="7f6d6-140">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="7f6d6-141">次の表は、JavaScript クライアントで使用できるログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="7f6d6-142">ログレベルをこれらの値のいずれかに設定すると、そのレベルおよびテーブル内のすべてのレベルでログ記録が有効になります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="7f6d6-143">Level</span><span class="sxs-lookup"><span data-stu-id="7f6d6-143">Level</span></span> | <span data-ttu-id="7f6d6-144">説明</span><span class="sxs-lookup"><span data-stu-id="7f6d6-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="7f6d6-145">メッセージはログに記録されません。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="7f6d6-146">アプリ全体でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="7f6d6-147">現在の操作でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="7f6d6-148">致命的ではない問題を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="7f6d6-149">情報メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="7f6d6-150">デバッグに役立つ診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="7f6d6-151">特定の問題を診断するために設計された、非常に詳細な診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="7f6d6-152">詳細設定を構成すると、ログはブラウザーコンソール (または NodeJS アプリの標準出力) に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="7f6d6-153">カスタムログシステムにログを送信する場合は、インターフェイスを実装する JavaScript オブジェクトを指定でき `ILogger` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="7f6d6-154">実装する必要のあるメソッドは `log` 、イベントのレベルとイベントに関連付けられたメッセージを受け取るだけです。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="7f6d6-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="7f6d6-156"> .NET クライアント ログ</span><span class="sxs-lookup"><span data-stu-id="7f6d6-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="7f6d6-157">クライアント側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="7f6d6-158">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください** 。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="7f6d6-159">.NET クライアントからログを取得するには、でメソッドを使用し `ConfigureLogging` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="7f6d6-160">これは、およびのメソッドと同じように動作し `ConfigureLogging` `WebHostBuilder` `HostBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="7f6d6-161">ASP.NET Core で使用するのと同じログプロバイダーを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="7f6d6-162">ただし、個々のログプロバイダーに対して NuGet パッケージを手動でインストールして有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="7f6d6-163">.NET クライアントのログ記録をアプリに追加するに :::no-loc(Blazor WebAssembly)::: は、「」を参照してください <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> 。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-163">To add .NET client logging to a :::no-loc(Blazor WebAssembly)::: app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="7f6d6-164">[コンソールのログ記録]</span><span class="sxs-lookup"><span data-stu-id="7f6d6-164">Console logging</span></span>

<span data-ttu-id="7f6d6-165">コンソールのログ記録を有効にするには [、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 追加します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="7f6d6-166">次に、メソッドを使用し `AddConsole` て、コンソールロガーを構成します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="7f6d6-167">デバッグ出力ウィンドウのログ記録</span><span class="sxs-lookup"><span data-stu-id="7f6d6-167">Debug output window logging</span></span>

<span data-ttu-id="7f6d6-168">また、ログを構成して、Visual Studio の [ **出力** ] ウィンドウにアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="7f6d6-169">次のよう[に、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールして、メソッドを使用します。 `AddDebug`</span><span class="sxs-lookup"><span data-stu-id="7f6d6-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="7f6d6-170">その他のログプロバイダー</span><span class="sxs-lookup"><span data-stu-id="7f6d6-170">Other logging providers</span></span>

<span data-ttu-id="7f6d6-171">:::no-loc(SignalR)::: では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、と統合されるその他のログ記録プロバイダーがサポートされてい `Microsoft.Extensions.Logging` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-171">:::no-loc(SignalR)::: supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="7f6d6-172">ログシステムにが用意されている場合は `ILoggerProvider` 、次のように登録でき `AddProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="7f6d6-173">コントロールの詳細度</span><span class="sxs-lookup"><span data-stu-id="7f6d6-173">Control verbosity</span></span>

<span data-ttu-id="7f6d6-174">アプリ内の他の場所からログを記録している場合は、既定のレベルをに変更すると、詳細が表示されない場合 `Debug` があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="7f6d6-175">フィルターを使用して、ログのログ記録レベルを構成でき :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-175">You can use a Filter to configure the logging level for :::no-loc(SignalR)::: logs.</span></span> <span data-ttu-id="7f6d6-176">これは、サーバーの場合とほぼ同じ方法でコードで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="7f6d6-177">ネットワーク トレース</span><span class="sxs-lookup"><span data-stu-id="7f6d6-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="7f6d6-178">ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="7f6d6-179">実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿 **しない** でください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="7f6d6-180">問題が発生した場合は、ネットワークトレースを使用すると、役に立つ情報が得られることがあります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="7f6d6-181">これは、問題の追跡ツールで問題をファイルする場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="7f6d6-182">Fiddler を使用してネットワークトレースを収集する (推奨オプション)</span><span class="sxs-lookup"><span data-stu-id="7f6d6-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="7f6d6-183">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-183">This method works for all apps.</span></span>

<span data-ttu-id="7f6d6-184">Fiddler は、HTTP トレースを収集するための非常に強力なツールです。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="7f6d6-185">[Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="7f6d6-186">Fiddler は Windows で使用でき、macOS と Linux のベータ版があります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="7f6d6-187">HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックの暗号化を解除できるようにするための追加の手順がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="7f6d6-188">詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="7f6d6-189">トレースを収集したら、[ **ファイル**  >  **Save**  >  ] メニューの [ **すべてのセッション** を保存] をクリックして、トレースをエクスポートできます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="7f6d6-191">Tcpdump を使用してネットワークトレースを収集する (macOS および Linux のみ)</span><span class="sxs-lookup"><span data-stu-id="7f6d6-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="7f6d6-192">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-192">This method works for all apps.</span></span>

<span data-ttu-id="7f6d6-193">コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="7f6d6-194">`root`アクセス許可のエラーが表示された場合は、コマンドにまたはのプレフィックスを付ける必要があり `sudo` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="7f6d6-195">を `[interface]` キャプチャするネットワークインターフェイスで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="7f6d6-196">通常は、 `/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="7f6d6-197">詳細については、 `tcpdump` ホストシステムの man ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="7f6d6-198">ブラウザーでネットワークトレースを収集する</span><span class="sxs-lookup"><span data-stu-id="7f6d6-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="7f6d6-199">この方法は、ブラウザーベースのアプリに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="7f6d6-200">ほとんどのブラウザーには、ブラウザーとサーバー間のネットワークアクティビティをキャプチャできる [ネットワーク] タブがあり開発者ツール。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="7f6d6-201">ただし、これらのトレースには、WebSocket と Server-Sent イベントメッセージは含まれません。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="7f6d6-202">これらのトランスポートを使用している場合は、Fiddler や TcpDump などのツールを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="7f6d6-203">Microsoft Edge と Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="7f6d6-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="7f6d6-204">(この手順は、Edge と Internet Explorer の両方で同じです)。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="7f6d6-205">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="7f6d6-206">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-206">Click the Network Tab</span></span>
3. <span data-ttu-id="7f6d6-207">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="7f6d6-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="7f6d6-208">ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge Dev Tools の [ネットワーク] タブの [保存] アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="7f6d6-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="7f6d6-210">Google Chrome</span></span>

1. <span data-ttu-id="7f6d6-211">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="7f6d6-212">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-212">Click the Network Tab</span></span>
3. <span data-ttu-id="7f6d6-213">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="7f6d6-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="7f6d6-214">要求の一覧の任意の場所を右クリックし、[コンテンツと共に HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome Dev ツールの [ネットワーク] タブの [HAR として保存] オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="7f6d6-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="7f6d6-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="7f6d6-217">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="7f6d6-218">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-218">Click the Network Tab</span></span>
3. <span data-ttu-id="7f6d6-219">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="7f6d6-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="7f6d6-220">要求の一覧の任意の場所を右クリックし、[すべてを HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools [ネットワーク] タブの [すべてを HAR として保存] オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="7f6d6-222">診断ファイルを GitHub の問題にアタッチする</span><span class="sxs-lookup"><span data-stu-id="7f6d6-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="7f6d6-223">GitHub の問題に診断ファイルを添付するには、名前を変更して拡張機能を設定し、 `.txt` 問題にドラッグアンドドロップします。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="7f6d6-224">ログファイルやネットワークトレースの内容を GitHub の問題に貼り付けることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="7f6d6-225">これらのログとトレースは非常に大きくなる可能性があり、GitHub は通常、これらを切り捨てます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub の問題にログファイルをドラッグする](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="7f6d6-227">メトリック</span><span class="sxs-lookup"><span data-stu-id="7f6d6-227">Metrics</span></span>

<span data-ttu-id="7f6d6-228">メトリックは、一定期間のデータ測定値を表します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="7f6d6-229">たとえば、1秒あたりの要求です。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-229">For example, requests per second.</span></span> <span data-ttu-id="7f6d6-230">メトリックデータを使用すると、アプリの状態を高レベルで監視できます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="7f6d6-231">.NET gRPC メトリックは <xref:System.Diagnostics.Tracing.EventCounter> を使用して出力されます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="7f6d6-232">:::no-loc(SignalR)::: サーバーメトリック</span><span class="sxs-lookup"><span data-stu-id="7f6d6-232">:::no-loc(SignalR)::: server metrics</span></span>

<span data-ttu-id="7f6d6-233">:::no-loc(SignalR)::: サーバーメトリックは、イベントソースで報告され <xref:Microsoft.AspNetCore.Http.Connections> ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-233">:::no-loc(SignalR)::: server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="7f6d6-234">名前</span><span class="sxs-lookup"><span data-stu-id="7f6d6-234">Name</span></span>                    | <span data-ttu-id="7f6d6-235">説明</span><span class="sxs-lookup"><span data-stu-id="7f6d6-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="7f6d6-236">開始された接続の合計</span><span class="sxs-lookup"><span data-stu-id="7f6d6-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="7f6d6-237">停止した接続の合計数</span><span class="sxs-lookup"><span data-stu-id="7f6d6-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="7f6d6-238">タイムアウトした接続の合計数</span><span class="sxs-lookup"><span data-stu-id="7f6d6-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="7f6d6-239">現在の接続数</span><span class="sxs-lookup"><span data-stu-id="7f6d6-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="7f6d6-240">平均接続時間</span><span class="sxs-lookup"><span data-stu-id="7f6d6-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="7f6d6-241">メトリックを観察する</span><span class="sxs-lookup"><span data-stu-id="7f6d6-241">Observe metrics</span></span>

<span data-ttu-id="7f6d6-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) は、アドホックな正常性監視と最初のレベルのパフォーマンス調査を目的としたパフォーマンス監視ツールです。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="7f6d6-243">プロバイダー名としてを使用して .NET アプリを監視し `Microsoft.AspNetCore.Http.Connections` ます。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="7f6d6-244">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="7f6d6-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="7f6d6-245">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7f6d6-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
