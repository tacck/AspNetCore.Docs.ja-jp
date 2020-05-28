---
<span data-ttu-id="20e99-101">title: ' ASP.NET Core でのログと診断 ' SignalR 作成者: 説明: ' ASP.NET Core アプリから診断を収集する方法について説明 SignalR します。 '</span><span class="sxs-lookup"><span data-stu-id="20e99-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="20e99-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="20e99-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="20e99-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="20e99-103">'Blazor'</span></span>
- <span data-ttu-id="20e99-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="20e99-104">'Identity'</span></span>
- <span data-ttu-id="20e99-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="20e99-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="20e99-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="20e99-106">'Razor'</span></span>
- <span data-ttu-id="20e99-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="20e99-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="20e99-108">ASP.NET Core でのログ記録と診断SignalR</span><span class="sxs-lookup"><span data-stu-id="20e99-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="20e99-109">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="20e99-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="20e99-110">この記事では、ASP.NET Core アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供 SignalR します。</span><span class="sxs-lookup"><span data-stu-id="20e99-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="20e99-111">サーバー側のログ記録</span><span class="sxs-lookup"><span data-stu-id="20e99-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="20e99-112">サーバー側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20e99-113">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="20e99-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20e99-114">SignalRは ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。</span><span class="sxs-lookup"><span data-stu-id="20e99-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="20e99-115">既定の構成では、は SignalR ごくわずかな情報をログに記録しますが、これは構成できます。</span><span class="sxs-lookup"><span data-stu-id="20e99-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="20e99-116">ASP.NET Core ログの構成の詳細については、[ASP.NET Core ログ](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="20e99-117">2つの logger カテゴリを使用します。</span><span class="sxs-lookup"><span data-stu-id="20e99-117"> uses two logger categories:</span></span>

* <span data-ttu-id="20e99-118">`Microsoft.AspNetCore.SignalR`: ハブプロトコルに関連するログ、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティ。</span><span class="sxs-lookup"><span data-stu-id="20e99-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="20e99-119">`Microsoft.AspNetCore.Http.Connections`: Websocket、長いポーリング、サーバー送信イベント、低レベルのインフラストラクチャなどのトランスポートに関連するログ。 SignalR</span><span class="sxs-lookup"><span data-stu-id="20e99-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="20e99-120">から詳細なログを有効にするには SignalR 、の `Debug` サブセクションに次の項目を追加して、前のプレフィックスを*appsettings*ファイルのレベルに構成し `LogLevel` `Logging` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="20e99-121">これは、メソッドのコードで構成することもでき `CreateWebHostBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="20e99-122">JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。</span><span class="sxs-lookup"><span data-stu-id="20e99-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="20e99-123">構成システムのドキュメントを調べて、入れ子になった構成値を指定する方法を確認してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="20e99-124">たとえば、環境変数の使用時には、`:` の代わりに 2 つの `_` 文字を使用します (例: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。</span><span class="sxs-lookup"><span data-stu-id="20e99-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="20e99-125">アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="20e99-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="20e99-126">`Trace` レベルでは非常に低レベルの診断情報が生成されるため、アプリの問題を診断するために必要となることはまれです。</span><span class="sxs-lookup"><span data-stu-id="20e99-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="20e99-127">サーバー側ログにアクセスする</span><span class="sxs-lookup"><span data-stu-id="20e99-127">Access server-side logs</span></span>

<span data-ttu-id="20e99-128">サーバー側のログにアクセスする方法は、実行環境によって異なります。</span><span class="sxs-lookup"><span data-stu-id="20e99-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="20e99-129">IIS の外部のコンソールアプリとして</span><span class="sxs-lookup"><span data-stu-id="20e99-129">As a console app outside IIS</span></span>

<span data-ttu-id="20e99-130">コンソール アプリで実行中の場合は、[コンソール ロガー](xref:fundamentals/logging/index#console)を既定で有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="20e99-131">ログはコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="20e99-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="20e99-132">Visual Studio から IIS Express 内</span><span class="sxs-lookup"><span data-stu-id="20e99-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="20e99-133">Visual Studio では、[**出力**] ウィンドウにログ出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="20e99-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="20e99-134">[ **ASP.NET Core Web サーバー** ] ドロップダウンオプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="20e99-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="20e99-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20e99-135">Azure App Service</span></span>

<span data-ttu-id="20e99-136">Azure App Service ポータルの [**診断ログ**] セクションで [**アプリケーションログ (ファイルシステム)** ] オプションを有効にし、**レベル**をに構成し `Verbose` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="20e99-137">ログは、**ログストリーミング**サービスおよび App Service のファイルシステムのログで使用できます。</span><span class="sxs-lookup"><span data-stu-id="20e99-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="20e99-138">詳細については、「 [Azure ログストリーミング](xref:fundamentals/logging/index#azure-log-streaming)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="20e99-139">その他の環境</span><span class="sxs-lookup"><span data-stu-id="20e99-139">Other environments</span></span>

<span data-ttu-id="20e99-140">アプリが別の環境 (Docker、Kubernetes、Windows サービスなど) にデプロイされる場合は、その環境に適したログ プロバイダーを構成する方法についての詳細を、「<xref:fundamentals/logging/index>」で確認してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="20e99-141">JavaScript クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="20e99-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="20e99-142">クライアント側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20e99-143">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="20e99-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20e99-144">JavaScript クライアントを使用する場合は、でメソッドを使用してログオプションを構成でき `configureLogging` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="20e99-145">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="20e99-146">次の表は、JavaScript クライアントで使用できるログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="20e99-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="20e99-147">ログレベルをこれらの値のいずれかに設定すると、そのレベルおよびテーブル内のすべてのレベルでログ記録が有効になります。</span><span class="sxs-lookup"><span data-stu-id="20e99-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="20e99-148">Level</span><span class="sxs-lookup"><span data-stu-id="20e99-148">Level</span></span> | <span data-ttu-id="20e99-149">説明</span><span class="sxs-lookup"><span data-stu-id="20e99-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="20e99-150">title: ' ASP.NET Core でのログと診断 ' SignalR 作成者: 説明: ' ASP.NET Core アプリから診断を収集する方法について説明 SignalR します。 '</span><span class="sxs-lookup"><span data-stu-id="20e99-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="20e99-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="20e99-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="20e99-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="20e99-152">'Blazor'</span></span>
- <span data-ttu-id="20e99-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="20e99-153">'Identity'</span></span>
- <span data-ttu-id="20e99-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="20e99-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="20e99-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="20e99-155">'Razor'</span></span>
- <span data-ttu-id="20e99-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="20e99-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="20e99-157">title: ' ASP.NET Core でのログと診断 ' SignalR 作成者: 説明: ' ASP.NET Core アプリから診断を収集する方法について説明 SignalR します。 '</span><span class="sxs-lookup"><span data-stu-id="20e99-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="20e99-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="20e99-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="20e99-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="20e99-159">'Blazor'</span></span>
- <span data-ttu-id="20e99-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="20e99-160">'Identity'</span></span>
- <span data-ttu-id="20e99-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="20e99-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="20e99-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="20e99-162">'Razor'</span></span>
- <span data-ttu-id="20e99-163">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="20e99-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="20e99-164">title: ' ASP.NET Core でのログと診断 ' SignalR 作成者: 説明: ' ASP.NET Core アプリから診断を収集する方法について説明 SignalR します。 '</span><span class="sxs-lookup"><span data-stu-id="20e99-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="20e99-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="20e99-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="20e99-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="20e99-166">'Blazor'</span></span>
- <span data-ttu-id="20e99-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="20e99-167">'Identity'</span></span>
- <span data-ttu-id="20e99-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="20e99-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="20e99-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="20e99-169">'Razor'</span></span>
- <span data-ttu-id="20e99-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="20e99-170">'SignalR' uid:</span></span> 

<span data-ttu-id="20e99-171">------ | |`None` |メッセージはログに記録されません。</span><span class="sxs-lookup"><span data-stu-id="20e99-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="20e99-172">| |`Critical` |アプリ全体でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="20e99-173">| |`Error` |現在の操作でエラーが発生したことを示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="20e99-174">| |`Warning` |致命的ではない問題を示すメッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="20e99-175">| |`Information` |情報メッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="20e99-176">| |`Debug` |デバッグに役立つ診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="20e99-177">| |`Trace` |特定の問題を診断するために設計された、非常に詳細な診断メッセージ。</span><span class="sxs-lookup"><span data-stu-id="20e99-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="20e99-178">詳細設定を構成すると、ログはブラウザーコンソール (または NodeJS アプリの標準出力) に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="20e99-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="20e99-179">カスタムログシステムにログを送信する場合は、インターフェイスを実装する JavaScript オブジェクトを指定でき `ILogger` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="20e99-180">実装する必要のあるメソッドは `log` 、イベントのレベルとイベントに関連付けられたメッセージを受け取るだけです。</span><span class="sxs-lookup"><span data-stu-id="20e99-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="20e99-181">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="20e99-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="20e99-182">.NET クライアントのログ記録</span><span class="sxs-lookup"><span data-stu-id="20e99-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="20e99-183">クライアント側のログには、アプリからの機密情報が含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20e99-184">運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="20e99-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20e99-185">.NET クライアントからログを取得するには、でメソッドを使用し `ConfigureLogging` `HubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="20e99-186">これは、およびのメソッドと同じように動作し `ConfigureLogging` `WebHostBuilder` `HostBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="20e99-187">ASP.NET Core で使用するのと同じログプロバイダーを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="20e99-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="20e99-188">ただし、個々のログプロバイダーに対して NuGet パッケージを手動でインストールして有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="20e99-189">[コンソールのログ記録]</span><span class="sxs-lookup"><span data-stu-id="20e99-189">Console logging</span></span>

<span data-ttu-id="20e99-190">コンソールのログ記録を有効にするには[、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)追加します。</span><span class="sxs-lookup"><span data-stu-id="20e99-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="20e99-191">次に、メソッドを使用し `AddConsole` て、コンソールロガーを構成します。</span><span class="sxs-lookup"><span data-stu-id="20e99-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="20e99-192">デバッグ出力ウィンドウのログ記録</span><span class="sxs-lookup"><span data-stu-id="20e99-192">Debug output window logging</span></span>

<span data-ttu-id="20e99-193">また、ログを構成して、Visual Studio の [**出力**] ウィンドウにアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="20e99-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="20e99-194">次のよう[に、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールして、メソッドを使用します。 `AddDebug`</span><span class="sxs-lookup"><span data-stu-id="20e99-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="20e99-195">その他のログプロバイダー</span><span class="sxs-lookup"><span data-stu-id="20e99-195">Other logging providers</span></span>

SignalR<span data-ttu-id="20e99-196">では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、と統合されるその他のログ記録プロバイダーがサポートされてい `Microsoft.Extensions.Logging` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="20e99-197">ログシステムにが用意されている場合は `ILoggerProvider` 、次のように登録でき `AddProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="20e99-198">コントロールの詳細度</span><span class="sxs-lookup"><span data-stu-id="20e99-198">Control verbosity</span></span>

<span data-ttu-id="20e99-199">アプリ内の他の場所からログを記録している場合は、既定のレベルをに変更すると、詳細が表示されない場合 `Debug` があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="20e99-200">フィルターを使用して、ログのログ記録レベルを構成でき SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="20e99-201">これは、サーバーの場合とほぼ同じ方法でコードで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="20e99-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="20e99-202">ネットワーク トレース</span><span class="sxs-lookup"><span data-stu-id="20e99-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="20e99-203">ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。</span><span class="sxs-lookup"><span data-stu-id="20e99-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="20e99-204">実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="20e99-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20e99-205">問題が発生した場合は、ネットワークトレースを使用すると、役に立つ情報が得られることがあります。</span><span class="sxs-lookup"><span data-stu-id="20e99-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="20e99-206">これは、問題の追跡ツールで問題をファイルする場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="20e99-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="20e99-207">Fiddler を使用してネットワークトレースを収集する (推奨オプション)</span><span class="sxs-lookup"><span data-stu-id="20e99-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="20e99-208">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="20e99-208">This method works for all apps.</span></span>

<span data-ttu-id="20e99-209">Fiddler は、HTTP トレースを収集するための非常に強力なツールです。</span><span class="sxs-lookup"><span data-stu-id="20e99-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="20e99-210">[Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。</span><span class="sxs-lookup"><span data-stu-id="20e99-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="20e99-211">Fiddler は Windows で使用でき、macOS と Linux のベータ版があります。</span><span class="sxs-lookup"><span data-stu-id="20e99-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="20e99-212">HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックの暗号化を解除できるようにするための追加の手順がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="20e99-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="20e99-213">詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="20e99-214">トレースを収集したら、[**ファイル**  >  **Save**  >  ] メニューの [**すべてのセッション**を保存] をクリックして、トレースをエクスポートできます。</span><span class="sxs-lookup"><span data-stu-id="20e99-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="20e99-216">Tcpdump を使用してネットワークトレースを収集する (macOS および Linux のみ)</span><span class="sxs-lookup"><span data-stu-id="20e99-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="20e99-217">この方法は、すべてのアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="20e99-217">This method works for all apps.</span></span>

<span data-ttu-id="20e99-218">コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。</span><span class="sxs-lookup"><span data-stu-id="20e99-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="20e99-219">`root`アクセス許可のエラーが表示された場合は、コマンドにまたはのプレフィックスを付ける必要があり `sudo` ます。</span><span class="sxs-lookup"><span data-stu-id="20e99-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="20e99-220">を `[interface]` キャプチャするネットワークインターフェイスで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="20e99-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="20e99-221">通常は、 `/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。</span><span class="sxs-lookup"><span data-stu-id="20e99-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="20e99-222">詳細については、 `tcpdump` ホストシステムの man ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="20e99-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="20e99-223">ブラウザーでネットワークトレースを収集する</span><span class="sxs-lookup"><span data-stu-id="20e99-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="20e99-224">この方法は、ブラウザーベースのアプリに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="20e99-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="20e99-225">ほとんどのブラウザーには、ブラウザーとサーバー間のネットワークアクティビティをキャプチャできる [ネットワーク] タブがあり開発者ツール。</span><span class="sxs-lookup"><span data-stu-id="20e99-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="20e99-226">ただし、これらのトレースには、WebSocket およびサーバー送信のイベントメッセージは含まれません。</span><span class="sxs-lookup"><span data-stu-id="20e99-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="20e99-227">これらのトランスポートを使用している場合は、Fiddler や TcpDump などのツールを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="20e99-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="20e99-228">Microsoft Edge と Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="20e99-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="20e99-229">(この手順は、Edge と Internet Explorer の両方で同じです)。</span><span class="sxs-lookup"><span data-stu-id="20e99-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="20e99-230">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="20e99-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20e99-231">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20e99-231">Click the Network Tab</span></span>
3. <span data-ttu-id="20e99-232">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="20e99-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20e99-233">ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="20e99-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge Dev Tools の [ネットワーク] タブの [保存] アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="20e99-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="20e99-235">Google Chrome</span></span>

1. <span data-ttu-id="20e99-236">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="20e99-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20e99-237">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20e99-237">Click the Network Tab</span></span>
3. <span data-ttu-id="20e99-238">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="20e99-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20e99-239">要求の一覧の任意の場所を右クリックし、[コンテンツと共に HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="20e99-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome Dev ツールの [ネットワーク] タブの [HAR として保存] オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="20e99-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="20e99-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="20e99-242">F12 キーを押して開発ツールを開きます。</span><span class="sxs-lookup"><span data-stu-id="20e99-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20e99-243">[ネットワーク] タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20e99-243">Click the Network Tab</span></span>
3. <span data-ttu-id="20e99-244">必要に応じてページを更新し、問題を再現します</span><span class="sxs-lookup"><span data-stu-id="20e99-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20e99-245">要求の一覧の任意の場所を右クリックし、[すべてを HAR として保存] を選択します。</span><span class="sxs-lookup"><span data-stu-id="20e99-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox Dev Tools [ネットワーク] タブの [すべてを HAR として保存] オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="20e99-247">診断ファイルを GitHub の問題にアタッチする</span><span class="sxs-lookup"><span data-stu-id="20e99-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="20e99-248">GitHub の問題に診断ファイルを添付するには、名前を変更して拡張機能を設定し、 `.txt` 問題にドラッグアンドドロップします。</span><span class="sxs-lookup"><span data-stu-id="20e99-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="20e99-249">ログファイルやネットワークトレースの内容を GitHub の問題に貼り付けることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="20e99-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="20e99-250">これらのログとトレースは非常に大きくなる可能性があり、GitHub は通常、これらを切り捨てます。</span><span class="sxs-lookup"><span data-stu-id="20e99-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![GitHub の問題にログファイルをドラッグする](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="20e99-252">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="20e99-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
