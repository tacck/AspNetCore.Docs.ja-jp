---
<span data-ttu-id="79766-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-102">'Blazor'</span></span>
- <span data-ttu-id="79766-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-103">'Identity'</span></span>
- <span data-ttu-id="79766-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-105">'Razor'</span></span>
- <span data-ttu-id="79766-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="79766-107">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="79766-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79766-108">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="79766-109">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="79766-110">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="79766-110">Supported operating systems</span></span>

<span data-ttu-id="79766-111">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="79766-112">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="79766-112">Windows 7 or later</span></span>
* <span data-ttu-id="79766-113">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="79766-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="79766-114">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="79766-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="79766-115">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="79766-116">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="79766-117">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="79766-118">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="79766-118">Supported platforms</span></span>

<span data-ttu-id="79766-119">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="79766-120">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="79766-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="79766-121">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="79766-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="79766-122">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="79766-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="79766-123">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="79766-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="79766-124">32 ビット (x86) 用に公開されたアプリでは、IIS アプリケーション プールで 32 ビットが有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="79766-125">詳細については、「[IIS サイトを作成する](#create-the-iis-site)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="79766-126">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="79766-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="79766-127">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="79766-128">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="79766-129">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-129">In-process hosting model</span></span>

<span data-ttu-id="79766-130">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="79766-131">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="79766-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="79766-132">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="79766-133">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="79766-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="79766-134">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-134">Performs app initialization.</span></span>
  * <span data-ttu-id="79766-135">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="79766-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="79766-136">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="79766-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="79766-137">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="79766-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="79766-138">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="79766-140">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="79766-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="79766-141">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="79766-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="79766-142">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="79766-143">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="79766-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="79766-144">IIS HTTP サーバーによって要求が処理された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="79766-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="79766-145">要求は ASP.NET Core ミドルウェア パイプラインに送信されます。</span><span class="sxs-lookup"><span data-stu-id="79766-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="79766-146">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="79766-147">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="79766-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="79766-148">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="79766-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="79766-149">既存のアプリではインプロセス ホスティングがオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="79766-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="79766-150">ASP.NET Core Web テンプレートでは、インプロセス ホスティング モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="79766-151">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="79766-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="79766-152">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) へのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="79766-153">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="79766-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="79766-154">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-154">Out-of-process hosting model</span></span>

<span data-ttu-id="79766-155">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="79766-156">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="79766-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="79766-157">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="79766-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="79766-158">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="79766-160">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="79766-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="79766-161">ドライバーは、Web サイトの構成ポートで IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="79766-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="79766-162">構成されるポートは、通常 80 (HTTP) または 443 (HTTPS) です。</span><span class="sxs-lookup"><span data-stu-id="79766-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="79766-163">モジュールでは、アプリのランダムなポートで Kestrel に要求を転送します。</span><span class="sxs-lookup"><span data-stu-id="79766-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="79766-164">ランダムなポートは 80 または 443 ではありません。</span><span class="sxs-lookup"><span data-stu-id="79766-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="79766-165">ASP.NET Core モジュールは、起動時に環境変数によってポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="79766-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="79766-166"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能は、`http://localhost:{PORT}` でリッスンするようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="79766-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="79766-167">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="79766-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="79766-168">モジュールは HTTPS 転送をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="79766-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="79766-169">要求は HTTPS を介して IIS によって受信された場合でも HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="79766-170">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインに転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="79766-171">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="79766-172">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="79766-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="79766-173">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="79766-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="79766-174">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="79766-175">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="79766-176">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="79766-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="79766-177">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="79766-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="79766-178">`CreateHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="79766-179">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="79766-180">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="79766-180">IIS options</span></span>

<span data-ttu-id="79766-181">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="79766-181">**In-process hosting model**</span></span>

<span data-ttu-id="79766-182">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="79766-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="79766-183">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="79766-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="79766-184">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-184">Option</span></span>                         | <span data-ttu-id="79766-185">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-185">Default</span></span> | <span data-ttu-id="79766-186">設定</span><span class="sxs-lookup"><span data-stu-id="79766-186">Setting</span></span> |
| ---
<span data-ttu-id="79766-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-188">'Blazor'</span></span>
- <span data-ttu-id="79766-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-189">'Identity'</span></span>
- <span data-ttu-id="79766-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-191">'Razor'</span></span>
- <span data-ttu-id="79766-192">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-194">'Blazor'</span></span>
- <span data-ttu-id="79766-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-195">'Identity'</span></span>
- <span data-ttu-id="79766-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-197">'Razor'</span></span>
- <span data-ttu-id="79766-198">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-200">'Blazor'</span></span>
- <span data-ttu-id="79766-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-201">'Identity'</span></span>
- <span data-ttu-id="79766-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-203">'Razor'</span></span>
- <span data-ttu-id="79766-204">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-206">'Blazor'</span></span>
- <span data-ttu-id="79766-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-207">'Identity'</span></span>
- <span data-ttu-id="79766-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-209">'Razor'</span></span>
- <span data-ttu-id="79766-210">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-212">'Blazor'</span></span>
- <span data-ttu-id="79766-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-213">'Identity'</span></span>
- <span data-ttu-id="79766-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-215">'Razor'</span></span>
- <span data-ttu-id="79766-216">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-218">'Blazor'</span></span>
- <span data-ttu-id="79766-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-219">'Identity'</span></span>
- <span data-ttu-id="79766-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-221">'Razor'</span></span>
- <span data-ttu-id="79766-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-224">'Blazor'</span></span>
- <span data-ttu-id="79766-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-225">'Identity'</span></span>
- <span data-ttu-id="79766-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-227">'Razor'</span></span>
- <span data-ttu-id="79766-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-230">'Blazor'</span></span>
- <span data-ttu-id="79766-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-231">'Identity'</span></span>
- <span data-ttu-id="79766-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-233">'Razor'</span></span>
- <span data-ttu-id="79766-234">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-236">'Blazor'</span></span>
- <span data-ttu-id="79766-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-237">'Identity'</span></span>
- <span data-ttu-id="79766-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-239">'Razor'</span></span>
- <span data-ttu-id="79766-240">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-242">'Blazor'</span></span>
- <span data-ttu-id="79766-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-243">'Identity'</span></span>
- <span data-ttu-id="79766-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-245">'Razor'</span></span>
- <span data-ttu-id="79766-246">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-248">'Blazor'</span></span>
- <span data-ttu-id="79766-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-249">'Identity'</span></span>
- <span data-ttu-id="79766-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-251">'Razor'</span></span>
- <span data-ttu-id="79766-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-254">'Blazor'</span></span>
- <span data-ttu-id="79766-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-255">'Identity'</span></span>
- <span data-ttu-id="79766-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-257">'Razor'</span></span>
- <span data-ttu-id="79766-258">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-260">'Blazor'</span></span>
- <span data-ttu-id="79766-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-261">'Identity'</span></span>
- <span data-ttu-id="79766-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-263">'Razor'</span></span>
- <span data-ttu-id="79766-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-264">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-266">'Blazor'</span></span>
- <span data-ttu-id="79766-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-267">'Identity'</span></span>
- <span data-ttu-id="79766-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-269">'Razor'</span></span>
- <span data-ttu-id="79766-270">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-270">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-271">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が IIS サーバーによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-272">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-273">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-274">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-275">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="79766-276">| | `AllowSynchronousIO`           | `false` | `HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるかどうか。</span><span class="sxs-lookup"><span data-stu-id="79766-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="79766-277">| | `MaxRequestBodySize`           | `30000000`  | `HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="79766-278">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="79766-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="79766-279">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="79766-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="79766-280">`maxAllowedContentLength`を引き上げるには、*web.config* 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="79766-281">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="79766-282">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="79766-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="79766-283">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="79766-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="79766-284">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="79766-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="79766-285">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-285">Option</span></span>                         | <span data-ttu-id="79766-286">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-286">Default</span></span> | <span data-ttu-id="79766-287">設定</span><span class="sxs-lookup"><span data-stu-id="79766-287">Setting</span></span> |
| ---
<span data-ttu-id="79766-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-289">'Blazor'</span></span>
- <span data-ttu-id="79766-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-290">'Identity'</span></span>
- <span data-ttu-id="79766-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-292">'Razor'</span></span>
- <span data-ttu-id="79766-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-295">'Blazor'</span></span>
- <span data-ttu-id="79766-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-296">'Identity'</span></span>
- <span data-ttu-id="79766-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-298">'Razor'</span></span>
- <span data-ttu-id="79766-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-301">'Blazor'</span></span>
- <span data-ttu-id="79766-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-302">'Identity'</span></span>
- <span data-ttu-id="79766-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-304">'Razor'</span></span>
- <span data-ttu-id="79766-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-307">'Blazor'</span></span>
- <span data-ttu-id="79766-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-308">'Identity'</span></span>
- <span data-ttu-id="79766-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-310">'Razor'</span></span>
- <span data-ttu-id="79766-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-313">'Blazor'</span></span>
- <span data-ttu-id="79766-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-314">'Identity'</span></span>
- <span data-ttu-id="79766-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-316">'Razor'</span></span>
- <span data-ttu-id="79766-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-319">'Blazor'</span></span>
- <span data-ttu-id="79766-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-320">'Identity'</span></span>
- <span data-ttu-id="79766-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-322">'Razor'</span></span>
- <span data-ttu-id="79766-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-325">'Blazor'</span></span>
- <span data-ttu-id="79766-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-326">'Identity'</span></span>
- <span data-ttu-id="79766-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-328">'Razor'</span></span>
- <span data-ttu-id="79766-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-331">'Blazor'</span></span>
- <span data-ttu-id="79766-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-332">'Identity'</span></span>
- <span data-ttu-id="79766-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-334">'Razor'</span></span>
- <span data-ttu-id="79766-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-337">'Blazor'</span></span>
- <span data-ttu-id="79766-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-338">'Identity'</span></span>
- <span data-ttu-id="79766-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-340">'Razor'</span></span>
- <span data-ttu-id="79766-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-343">'Blazor'</span></span>
- <span data-ttu-id="79766-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-344">'Identity'</span></span>
- <span data-ttu-id="79766-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-346">'Razor'</span></span>
- <span data-ttu-id="79766-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-349">'Blazor'</span></span>
- <span data-ttu-id="79766-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-350">'Identity'</span></span>
- <span data-ttu-id="79766-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-352">'Razor'</span></span>
- <span data-ttu-id="79766-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-355">'Blazor'</span></span>
- <span data-ttu-id="79766-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-356">'Identity'</span></span>
- <span data-ttu-id="79766-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-358">'Razor'</span></span>
- <span data-ttu-id="79766-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-361">'Blazor'</span></span>
- <span data-ttu-id="79766-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-362">'Identity'</span></span>
- <span data-ttu-id="79766-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-364">'Razor'</span></span>
- <span data-ttu-id="79766-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-365">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-367">'Blazor'</span></span>
- <span data-ttu-id="79766-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-368">'Identity'</span></span>
- <span data-ttu-id="79766-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-370">'Razor'</span></span>
- <span data-ttu-id="79766-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-371">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-372">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-373">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-374">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-375">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="79766-376">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="79766-377">| | `ForwardClientCertificate`     | `true`  | `true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="79766-378">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="79766-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="79766-379">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)と ASP.NET Core モジュールは、次を転送するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="79766-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="79766-380">スキーム (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="79766-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="79766-381">要求元のリモート IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="79766-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="79766-382">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)は、Forwarded Headers Middleware を構成します。</span><span class="sxs-lookup"><span data-stu-id="79766-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="79766-383">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="79766-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="79766-384">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="79766-385">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-385">web.config file</span></span>

<span data-ttu-id="79766-386">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-387">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="79766-388">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="79766-389">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="79766-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="79766-390">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="79766-391">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="79766-392">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="79766-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="79766-393">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="79766-394">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="79766-395">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="79766-396">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="79766-397">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="79766-398">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="79766-398">web.config file location</span></span>

<span data-ttu-id="79766-399">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="79766-400">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="79766-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="79766-401">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="79766-402">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="79766-403">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="79766-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="79766-404">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="79766-405">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="79766-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="79766-406">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="79766-406">Transform web.config</span></span>

<span data-ttu-id="79766-407">発行時に "*web.config*" を変換する場合は、「<xref:host-and-deploy/iis/transform-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="79766-408">発行時に "*web.config*" を変換して、構成、プロファイル、環境に基づいた環境変数を設定しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="79766-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="79766-409">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="79766-409">IIS configuration</span></span>

<span data-ttu-id="79766-410">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="79766-411">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="79766-412">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="79766-413">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="79766-415">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="79766-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="79766-416">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="79766-418">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-419">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="79766-420">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-421">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-422">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-423">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-424">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="79766-425">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-426">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-427">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="79766-428">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="79766-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="79766-429">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="79766-430">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-431">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="79766-432">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="79766-433">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="79766-434">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="79766-435">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-436">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="79766-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="79766-437">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-438">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="79766-439">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-440">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-441">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-442">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-443">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="79766-444">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-445">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-446">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-446">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="79766-448">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="79766-449">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="79766-450">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="79766-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-451">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="79766-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="79766-452">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="79766-453">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="79766-454">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="79766-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="79766-455">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="79766-456">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="79766-456">Direct download (current version)</span></span>

<span data-ttu-id="79766-457">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="79766-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="79766-458">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="79766-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="79766-459">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="79766-459">Earlier versions of the installer</span></span>

<span data-ttu-id="79766-460">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="79766-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="79766-461">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="79766-462">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="79766-463">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="79766-464">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="79766-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="79766-465">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="79766-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="79766-466">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="79766-467">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="79766-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="79766-468">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-468">Run the installer on the server.</span></span> <span data-ttu-id="79766-469">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="79766-470">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="79766-471">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="79766-472">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-473">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="79766-474">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-475">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="79766-476">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="79766-477">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="79766-478">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="79766-479">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="79766-480">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="79766-481">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="79766-482">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="79766-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="79766-483">ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。</span><span class="sxs-lookup"><span data-stu-id="79766-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="79766-484">新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="79766-485">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="79766-486">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="79766-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="79766-487">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="79766-488">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="79766-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="79766-489">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="79766-490">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="79766-491">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="79766-491">Create the IIS site</span></span>

1. <span data-ttu-id="79766-492">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="79766-493">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="79766-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="79766-494">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="79766-495">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="79766-496">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="79766-497">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-498">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="79766-499">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="79766-501">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="79766-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="79766-502">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="79766-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="79766-503">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="79766-504">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="79766-505">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="79766-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="79766-506">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="79766-507">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="79766-508">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-509">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="79766-511">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="79766-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="79766-512">ASP.NET Core を使用するためにデスクトップ CLR (.NET CLR) を読み込む必要はありません。</span><span class="sxs-lookup"><span data-stu-id="79766-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="79766-513">.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="79766-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="79766-514">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="79766-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="79766-515">*ASP.NET Core 2.2 以降*:</span><span class="sxs-lookup"><span data-stu-id="79766-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="79766-516">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 32 ビット SDK で公開される 32 ビット (x86) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット用のアプリケーション プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="79766-517">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="79766-518">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-518">Select the app's Application Pool.</span></span> <span data-ttu-id="79766-519">**[操作]** サイドバーで、[**詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="79766-520">**[32 ビット アプリケーションの有効化]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="79766-521">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="79766-522">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="79766-523">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-523">Select the app's Application Pool.</span></span> <span data-ttu-id="79766-524">**[操作]** サイドバーで、[**詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="79766-525">**[32 ビット アプリケーションの有効化]** を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="79766-526">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="79766-527">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="79766-528">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="79766-529">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="79766-530">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="79766-531">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="79766-531">Deploy the app</span></span>

<span data-ttu-id="79766-532">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="79766-533">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="79766-534">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="79766-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="79766-535">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="79766-536">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="79766-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="79766-538">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="79766-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="79766-539">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="79766-540">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="79766-541">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="79766-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="79766-542">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="79766-543">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="79766-544">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="79766-544">Browse the website</span></span>

<span data-ttu-id="79766-545">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="79766-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="79766-546">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="79766-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="79766-547">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-547">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="79766-549">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-549">Locked deployment files</span></span>

<span data-ttu-id="79766-550">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="79766-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="79766-551">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="79766-552">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-553">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="79766-554">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="79766-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="79766-555">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="79766-556">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="79766-557">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="79766-558">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="79766-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="79766-559">データの保護</span><span class="sxs-lookup"><span data-stu-id="79766-559">Data protection</span></span>

<span data-ttu-id="79766-560">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="79766-561">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="79766-562">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="79766-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="79766-563">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="79766-564">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="79766-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="79766-565">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="79766-566">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="79766-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="79766-567">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="79766-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="79766-568">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-569">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="79766-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="79766-570">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="79766-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="79766-571">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="79766-572">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="79766-573">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="79766-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="79766-574">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="79766-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="79766-575">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="79766-576">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="79766-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="79766-577">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="79766-578">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="79766-579">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="79766-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="79766-580">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="79766-581">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="79766-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="79766-582">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="79766-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="79766-583">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="79766-584">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="79766-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="79766-585">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="79766-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="79766-586">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="79766-587">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="79766-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="79766-588">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="79766-589">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="79766-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="79766-590">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="79766-591">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="79766-592">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="79766-593">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="79766-594">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="79766-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="79766-595">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="79766-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="79766-596">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="79766-597">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="79766-598">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="79766-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="79766-599">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="79766-600">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="79766-601">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="79766-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="79766-602">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="79766-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="79766-603">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="79766-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="79766-604">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="79766-605">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="79766-605">Virtual Directories</span></span>

<span data-ttu-id="79766-606">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="79766-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="79766-607">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="79766-608">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="79766-608">Sub-applications</span></span>

<span data-ttu-id="79766-609">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="79766-610">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="79766-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="79766-611">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="79766-612">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="79766-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="79766-613">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="79766-614">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="79766-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="79766-615">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="79766-616">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="79766-617">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="79766-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="79766-618">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="79766-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="79766-619">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="79766-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="79766-620">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="79766-621">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="79766-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="79766-622">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="79766-623">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="79766-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="79766-624">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-624">Select **OK**.</span></span>

<span data-ttu-id="79766-625">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="79766-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="79766-626">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="79766-627">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="79766-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="79766-628">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="79766-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="79766-629">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="79766-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="79766-630">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="79766-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="79766-631">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="79766-632">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="79766-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="79766-633">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある "*AppCmd.exe コマンド*" のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="79766-634">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="79766-634">Configuration sections of web.config</span></span>

<span data-ttu-id="79766-635">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="79766-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="79766-636">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="79766-637">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="79766-638">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="79766-638">Application Pools</span></span>

<span data-ttu-id="79766-639">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="79766-640">インプロセス ホスティング: 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="79766-641">アウトプロセス ホスティング: アプリケーションをそれぞれ専用のアプリケーション プールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="79766-642">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="79766-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="79766-643">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="79766-644">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="79766-645">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="79766-645">Application Pool Identity</span></span>

<span data-ttu-id="79766-646">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="79766-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="79766-647">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="79766-648">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="79766-650">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="79766-651">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="79766-652">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="79766-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="79766-653">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="79766-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="79766-654">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="79766-655">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="79766-656">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="79766-657">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="79766-658">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="79766-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="79766-659">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-659">Select the **Check Names** button.</span></span> <span data-ttu-id="79766-660">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="79766-661">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="79766-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="79766-662">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="79766-663">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="79766-665">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-665">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="79766-667">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="79766-668">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="79766-669">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="79766-670">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="79766-671">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="79766-672">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="79766-672">HTTP/2 support</span></span>

<span data-ttu-id="79766-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="79766-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="79766-674">インプロセス</span><span class="sxs-lookup"><span data-stu-id="79766-674">In-process</span></span>
  * <span data-ttu-id="79766-675">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="79766-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="79766-676">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="79766-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="79766-677">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="79766-677">Out-of-process</span></span>
  * <span data-ttu-id="79766-678">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="79766-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="79766-679">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="79766-680">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="79766-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="79766-681">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="79766-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="79766-682">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="79766-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="79766-683">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="79766-684">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="79766-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="79766-685">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="79766-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="79766-686">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="79766-687">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="79766-687">CORS preflight requests</span></span>

<span data-ttu-id="79766-688">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="79766-689">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="79766-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="79766-690">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="79766-691">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="79766-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="79766-692">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="79766-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="79766-693">[Application Initialization モジュール](#application-initialization-module): アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="79766-694">[アイドル タイムアウト](#idle-timeout): アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="79766-695">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="79766-695">Application Initialization Module</span></span>

<span data-ttu-id="79766-696">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="79766-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="79766-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="79766-698">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="79766-698">The request triggers the app to start.</span></span> <span data-ttu-id="79766-699">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="79766-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="79766-700">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="79766-701">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="79766-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="79766-702">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="79766-703">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="79766-704">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="79766-705">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="79766-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="79766-706">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="79766-707">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="79766-708">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="79766-709">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="79766-710">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="79766-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="79766-711">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="79766-712">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="79766-713">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="79766-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="79766-714">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="79766-715">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-715">Select **OK**.</span></span>
  1. <span data-ttu-id="79766-716">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="79766-717">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="79766-718">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="79766-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="79766-719">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="79766-720">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-720">Select **OK**.</span></span>

* <span data-ttu-id="79766-721">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="79766-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="79766-722">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="79766-722">Idle Timeout</span></span>

<span data-ttu-id="79766-723">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="79766-724">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="79766-725">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="79766-726">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="79766-727">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="79766-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="79766-728">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="79766-729">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-729">Select **OK**.</span></span>
1. <span data-ttu-id="79766-730">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="79766-730">Recycle the worker process.</span></span>

<span data-ttu-id="79766-731">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="79766-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="79766-732">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="79766-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="79766-733">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="79766-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="79766-734">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79766-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="79766-735">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="79766-735">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="79766-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="79766-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="79766-737">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="79766-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="79766-738">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="79766-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="79766-739">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="79766-739">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="79766-740">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="79766-740">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="79766-741">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="79766-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="79766-742">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79766-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="79766-743">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="79766-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="79766-744">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="79766-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="79766-745">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="79766-745">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="79766-746">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="79766-747">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="79766-748">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="79766-748">Supported operating systems</span></span>

<span data-ttu-id="79766-749">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="79766-750">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="79766-750">Windows 7 or later</span></span>
* <span data-ttu-id="79766-751">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="79766-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="79766-752">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="79766-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="79766-753">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="79766-754">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="79766-755">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="79766-756">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="79766-756">Supported platforms</span></span>

<span data-ttu-id="79766-757">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="79766-758">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="79766-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="79766-759">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="79766-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="79766-760">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="79766-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="79766-761">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="79766-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="79766-762">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="79766-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="79766-763">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="79766-764">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="79766-765">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-765">In-process hosting model</span></span>

<span data-ttu-id="79766-766">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="79766-767">インプロセス ホスティングでは、次の理由により、アウトプロセス ホスティングよりもパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="79766-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="79766-768">要求はループバック アダプター経由でプロキシ化されません。</span><span class="sxs-lookup"><span data-stu-id="79766-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="79766-769">ループバック アダプターは、同じマシンに送信ネットワーク トラフィックを戻すネットワーク インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="79766-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="79766-770">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="79766-771">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="79766-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="79766-772">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-772">Performs app initialization.</span></span>
  * <span data-ttu-id="79766-773">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="79766-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="79766-774">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="79766-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="79766-775">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="79766-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="79766-776">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="79766-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="79766-777">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="79766-779">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="79766-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="79766-780">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="79766-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="79766-781">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="79766-782">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="79766-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="79766-783">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="79766-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="79766-784">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="79766-785">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="79766-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="79766-786">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="79766-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="79766-787">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="79766-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="79766-788">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="79766-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="79766-789">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="79766-790">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="79766-790">Out-of-process hosting model</span></span>

<span data-ttu-id="79766-791">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="79766-792">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="79766-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="79766-793">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="79766-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="79766-794">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="79766-796">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="79766-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="79766-797">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="79766-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="79766-798">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="79766-799">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="79766-800">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="79766-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="79766-801">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="79766-802">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="79766-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="79766-803">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="79766-804">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="79766-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="79766-805">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="79766-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="79766-806">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="79766-807">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="79766-808">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="79766-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="79766-809">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="79766-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="79766-810">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="79766-811">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="79766-812">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="79766-812">IIS options</span></span>

<span data-ttu-id="79766-813">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="79766-813">**In-process hosting model**</span></span>

<span data-ttu-id="79766-814">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="79766-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="79766-815">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="79766-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="79766-816">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-816">Option</span></span>                         | <span data-ttu-id="79766-817">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-817">Default</span></span> | <span data-ttu-id="79766-818">設定</span><span class="sxs-lookup"><span data-stu-id="79766-818">Setting</span></span> |
| ---
<span data-ttu-id="79766-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-820">'Blazor'</span></span>
- <span data-ttu-id="79766-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-821">'Identity'</span></span>
- <span data-ttu-id="79766-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-823">'Razor'</span></span>
- <span data-ttu-id="79766-824">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-826">'Blazor'</span></span>
- <span data-ttu-id="79766-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-827">'Identity'</span></span>
- <span data-ttu-id="79766-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-829">'Razor'</span></span>
- <span data-ttu-id="79766-830">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-832">'Blazor'</span></span>
- <span data-ttu-id="79766-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-833">'Identity'</span></span>
- <span data-ttu-id="79766-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-835">'Razor'</span></span>
- <span data-ttu-id="79766-836">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-838">'Blazor'</span></span>
- <span data-ttu-id="79766-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-839">'Identity'</span></span>
- <span data-ttu-id="79766-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-841">'Razor'</span></span>
- <span data-ttu-id="79766-842">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-844">'Blazor'</span></span>
- <span data-ttu-id="79766-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-845">'Identity'</span></span>
- <span data-ttu-id="79766-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-847">'Razor'</span></span>
- <span data-ttu-id="79766-848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-850">'Blazor'</span></span>
- <span data-ttu-id="79766-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-851">'Identity'</span></span>
- <span data-ttu-id="79766-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-853">'Razor'</span></span>
- <span data-ttu-id="79766-854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-856">'Blazor'</span></span>
- <span data-ttu-id="79766-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-857">'Identity'</span></span>
- <span data-ttu-id="79766-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-859">'Razor'</span></span>
- <span data-ttu-id="79766-860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-862">'Blazor'</span></span>
- <span data-ttu-id="79766-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-863">'Identity'</span></span>
- <span data-ttu-id="79766-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-865">'Razor'</span></span>
- <span data-ttu-id="79766-866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-868">'Blazor'</span></span>
- <span data-ttu-id="79766-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-869">'Identity'</span></span>
- <span data-ttu-id="79766-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-871">'Razor'</span></span>
- <span data-ttu-id="79766-872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-874">'Blazor'</span></span>
- <span data-ttu-id="79766-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-875">'Identity'</span></span>
- <span data-ttu-id="79766-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-877">'Razor'</span></span>
- <span data-ttu-id="79766-878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-880">'Blazor'</span></span>
- <span data-ttu-id="79766-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-881">'Identity'</span></span>
- <span data-ttu-id="79766-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-883">'Razor'</span></span>
- <span data-ttu-id="79766-884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-886">'Blazor'</span></span>
- <span data-ttu-id="79766-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-887">'Identity'</span></span>
- <span data-ttu-id="79766-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-889">'Razor'</span></span>
- <span data-ttu-id="79766-890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-892">'Blazor'</span></span>
- <span data-ttu-id="79766-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-893">'Identity'</span></span>
- <span data-ttu-id="79766-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-895">'Razor'</span></span>
- <span data-ttu-id="79766-896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-896">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-898">'Blazor'</span></span>
- <span data-ttu-id="79766-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-899">'Identity'</span></span>
- <span data-ttu-id="79766-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-901">'Razor'</span></span>
- <span data-ttu-id="79766-902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-902">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-903">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が IIS サーバーによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-904">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-905">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-906">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-907">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="79766-908">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="79766-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="79766-909">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="79766-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="79766-910">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="79766-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="79766-911">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-911">Option</span></span>                         | <span data-ttu-id="79766-912">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-912">Default</span></span> | <span data-ttu-id="79766-913">設定</span><span class="sxs-lookup"><span data-stu-id="79766-913">Setting</span></span> |
| ---
<span data-ttu-id="79766-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-915">'Blazor'</span></span>
- <span data-ttu-id="79766-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-916">'Identity'</span></span>
- <span data-ttu-id="79766-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-918">'Razor'</span></span>
- <span data-ttu-id="79766-919">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-921">'Blazor'</span></span>
- <span data-ttu-id="79766-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-922">'Identity'</span></span>
- <span data-ttu-id="79766-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-924">'Razor'</span></span>
- <span data-ttu-id="79766-925">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-927">'Blazor'</span></span>
- <span data-ttu-id="79766-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-928">'Identity'</span></span>
- <span data-ttu-id="79766-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-930">'Razor'</span></span>
- <span data-ttu-id="79766-931">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-933">'Blazor'</span></span>
- <span data-ttu-id="79766-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-934">'Identity'</span></span>
- <span data-ttu-id="79766-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-936">'Razor'</span></span>
- <span data-ttu-id="79766-937">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-939">'Blazor'</span></span>
- <span data-ttu-id="79766-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-940">'Identity'</span></span>
- <span data-ttu-id="79766-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-942">'Razor'</span></span>
- <span data-ttu-id="79766-943">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-945">'Blazor'</span></span>
- <span data-ttu-id="79766-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-946">'Identity'</span></span>
- <span data-ttu-id="79766-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-948">'Razor'</span></span>
- <span data-ttu-id="79766-949">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-951">'Blazor'</span></span>
- <span data-ttu-id="79766-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-952">'Identity'</span></span>
- <span data-ttu-id="79766-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-954">'Razor'</span></span>
- <span data-ttu-id="79766-955">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-957">'Blazor'</span></span>
- <span data-ttu-id="79766-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-958">'Identity'</span></span>
- <span data-ttu-id="79766-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-960">'Razor'</span></span>
- <span data-ttu-id="79766-961">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-963">'Blazor'</span></span>
- <span data-ttu-id="79766-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-964">'Identity'</span></span>
- <span data-ttu-id="79766-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-966">'Razor'</span></span>
- <span data-ttu-id="79766-967">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-969">'Blazor'</span></span>
- <span data-ttu-id="79766-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-970">'Identity'</span></span>
- <span data-ttu-id="79766-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-972">'Razor'</span></span>
- <span data-ttu-id="79766-973">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-975">'Blazor'</span></span>
- <span data-ttu-id="79766-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-976">'Identity'</span></span>
- <span data-ttu-id="79766-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-978">'Razor'</span></span>
- <span data-ttu-id="79766-979">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-981">'Blazor'</span></span>
- <span data-ttu-id="79766-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-982">'Identity'</span></span>
- <span data-ttu-id="79766-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-984">'Razor'</span></span>
- <span data-ttu-id="79766-985">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-987">'Blazor'</span></span>
- <span data-ttu-id="79766-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-988">'Identity'</span></span>
- <span data-ttu-id="79766-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-990">'Razor'</span></span>
- <span data-ttu-id="79766-991">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-991">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-993">'Blazor'</span></span>
- <span data-ttu-id="79766-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-994">'Identity'</span></span>
- <span data-ttu-id="79766-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-996">'Razor'</span></span>
- <span data-ttu-id="79766-997">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-997">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-998">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-999">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-1000">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-1001">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="79766-1002">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="79766-1003">| | `ForwardClientCertificate`     | `true`  | `true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="79766-1004">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="79766-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="79766-1005">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="79766-1006">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="79766-1007">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="79766-1008">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-1008">web.config file</span></span>

<span data-ttu-id="79766-1009">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-1010">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="79766-1011">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="79766-1012">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="79766-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="79766-1013">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="79766-1014">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="79766-1015">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="79766-1016">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="79766-1017">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="79766-1018">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="79766-1019">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="79766-1020">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="79766-1021">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="79766-1021">web.config file location</span></span>

<span data-ttu-id="79766-1022">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="79766-1023">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="79766-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="79766-1024">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="79766-1025">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="79766-1026">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="79766-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="79766-1027">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="79766-1028">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="79766-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="79766-1029">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="79766-1029">Transform web.config</span></span>

<span data-ttu-id="79766-1030">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="79766-1031">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="79766-1031">IIS configuration</span></span>

<span data-ttu-id="79766-1032">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="79766-1033">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="79766-1034">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="79766-1035">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="79766-1037">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="79766-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="79766-1038">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="79766-1040">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-1041">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="79766-1042">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-1043">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-1044">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-1045">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-1046">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="79766-1047">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-1048">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-1049">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="79766-1050">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="79766-1051">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="79766-1052">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-1053">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="79766-1054">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="79766-1055">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="79766-1056">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="79766-1057">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-1058">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="79766-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="79766-1059">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-1060">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="79766-1061">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-1062">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-1063">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-1064">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-1065">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="79766-1066">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-1067">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-1068">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1068">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="79766-1070">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="79766-1071">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="79766-1072">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-1073">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="79766-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="79766-1074">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="79766-1075">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="79766-1076">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="79766-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="79766-1077">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="79766-1078">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="79766-1078">Download</span></span>

1. <span data-ttu-id="79766-1079">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="79766-1080">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="79766-1081">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="79766-1082">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="79766-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="79766-1083">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="79766-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="79766-1084">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="79766-1085">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="79766-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="79766-1086">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1086">Run the installer on the server.</span></span> <span data-ttu-id="79766-1087">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="79766-1088">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="79766-1089">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="79766-1090">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-1091">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="79766-1092">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-1093">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="79766-1094">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="79766-1095">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="79766-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="79766-1097">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="79766-1098">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="79766-1099">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="79766-1100">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="79766-1101">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="79766-1102">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="79766-1103">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="79766-1104">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="79766-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="79766-1105">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="79766-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="79766-1106">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="79766-1107">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="79766-1108">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="79766-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="79766-1109">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="79766-1110">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="79766-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="79766-1111">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="79766-1112">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="79766-1113">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="79766-1113">Create the IIS site</span></span>

1. <span data-ttu-id="79766-1114">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="79766-1115">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="79766-1116">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="79766-1117">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="79766-1118">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="79766-1119">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-1120">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="79766-1121">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="79766-1123">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="79766-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="79766-1124">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="79766-1125">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="79766-1126">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="79766-1127">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="79766-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="79766-1128">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="79766-1129">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="79766-1130">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-1131">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="79766-1133">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="79766-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="79766-1134">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="79766-1135">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="79766-1136">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="79766-1137">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="79766-1138">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="79766-1139">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="79766-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="79766-1140">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="79766-1141">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="79766-1142">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="79766-1143">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="79766-1144">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="79766-1145">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="79766-1145">Deploy the app</span></span>

<span data-ttu-id="79766-1146">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="79766-1147">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="79766-1148">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="79766-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="79766-1149">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="79766-1150">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="79766-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="79766-1152">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="79766-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="79766-1153">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="79766-1154">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="79766-1155">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="79766-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="79766-1156">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="79766-1157">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="79766-1158">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="79766-1158">Browse the website</span></span>

<span data-ttu-id="79766-1159">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="79766-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="79766-1160">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="79766-1161">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-1161">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="79766-1163">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-1163">Locked deployment files</span></span>

<span data-ttu-id="79766-1164">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="79766-1165">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="79766-1166">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-1167">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="79766-1168">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="79766-1169">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="79766-1170">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="79766-1171">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="79766-1172">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="79766-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="79766-1173">データの保護</span><span class="sxs-lookup"><span data-stu-id="79766-1173">Data protection</span></span>

<span data-ttu-id="79766-1174">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="79766-1175">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="79766-1176">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="79766-1177">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="79766-1178">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="79766-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="79766-1179">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="79766-1180">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="79766-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="79766-1181">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="79766-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="79766-1182">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-1183">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="79766-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="79766-1184">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="79766-1185">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="79766-1186">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="79766-1187">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="79766-1188">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="79766-1189">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="79766-1190">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="79766-1191">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="79766-1192">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="79766-1193">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="79766-1194">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="79766-1195">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="79766-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="79766-1196">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="79766-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="79766-1197">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="79766-1198">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="79766-1199">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="79766-1200">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="79766-1201">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="79766-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="79766-1202">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="79766-1203">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="79766-1204">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="79766-1205">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="79766-1206">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="79766-1207">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="79766-1208">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="79766-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="79766-1209">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="79766-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="79766-1210">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="79766-1211">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="79766-1212">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="79766-1213">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="79766-1214">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="79766-1215">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="79766-1216">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="79766-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="79766-1217">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="79766-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="79766-1218">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="79766-1219">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="79766-1219">Virtual Directories</span></span>

<span data-ttu-id="79766-1220">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="79766-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="79766-1221">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="79766-1222">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="79766-1222">Sub-applications</span></span>

<span data-ttu-id="79766-1223">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="79766-1224">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="79766-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="79766-1225">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="79766-1226">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="79766-1227">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="79766-1228">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="79766-1229">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="79766-1230">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="79766-1231">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="79766-1232">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="79766-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="79766-1233">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="79766-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="79766-1234">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="79766-1235">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="79766-1236">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="79766-1237">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="79766-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="79766-1238">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1238">Select **OK**.</span></span>

<span data-ttu-id="79766-1239">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="79766-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="79766-1240">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="79766-1241">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="79766-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="79766-1242">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="79766-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="79766-1243">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="79766-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="79766-1244">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="79766-1245">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="79766-1246">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="79766-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="79766-1247">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある "*AppCmd.exe コマンド*" のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="79766-1248">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="79766-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="79766-1249">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="79766-1250">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="79766-1251">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="79766-1252">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="79766-1252">Application Pools</span></span>

<span data-ttu-id="79766-1253">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="79766-1254">インプロセス ホスティング: 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="79766-1255">アウトプロセス ホスティング: アプリケーションをそれぞれ専用のアプリケーション プールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="79766-1256">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="79766-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="79766-1257">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="79766-1258">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="79766-1259">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="79766-1259">Application Pool Identity</span></span>

<span data-ttu-id="79766-1260">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="79766-1261">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="79766-1262">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="79766-1264">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="79766-1265">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="79766-1266">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="79766-1267">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="79766-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="79766-1268">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="79766-1269">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="79766-1270">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="79766-1271">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="79766-1272">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="79766-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="79766-1273">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="79766-1274">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="79766-1275">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="79766-1276">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="79766-1277">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="79766-1279">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1279">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="79766-1281">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="79766-1282">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="79766-1283">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="79766-1284">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="79766-1285">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="79766-1286">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="79766-1286">HTTP/2 support</span></span>

<span data-ttu-id="79766-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="79766-1288">インプロセス</span><span class="sxs-lookup"><span data-stu-id="79766-1288">In-process</span></span>
  * <span data-ttu-id="79766-1289">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="79766-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="79766-1290">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="79766-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="79766-1291">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="79766-1291">Out-of-process</span></span>
  * <span data-ttu-id="79766-1292">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="79766-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="79766-1293">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="79766-1294">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="79766-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="79766-1295">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="79766-1296">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="79766-1297">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="79766-1298">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="79766-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="79766-1299">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="79766-1300">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="79766-1301">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="79766-1301">CORS preflight requests</span></span>

<span data-ttu-id="79766-1302">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="79766-1303">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="79766-1304">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="79766-1305">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="79766-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="79766-1306">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="79766-1307">[Application Initialization モジュール](#application-initialization-module): アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="79766-1308">[アイドル タイムアウト](#idle-timeout): アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="79766-1309">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="79766-1309">Application Initialization Module</span></span>

<span data-ttu-id="79766-1310">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="79766-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="79766-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="79766-1312">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1312">The request triggers the app to start.</span></span> <span data-ttu-id="79766-1313">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="79766-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="79766-1314">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="79766-1315">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="79766-1316">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="79766-1317">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="79766-1318">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="79766-1319">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="79766-1320">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="79766-1321">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="79766-1322">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="79766-1323">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="79766-1324">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="79766-1325">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="79766-1326">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="79766-1327">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="79766-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="79766-1328">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="79766-1329">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="79766-1330">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="79766-1331">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="79766-1332">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="79766-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="79766-1333">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="79766-1334">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1334">Select **OK**.</span></span>

* <span data-ttu-id="79766-1335">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="79766-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="79766-1336">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="79766-1336">Idle Timeout</span></span>

<span data-ttu-id="79766-1337">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="79766-1338">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="79766-1339">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="79766-1340">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="79766-1341">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="79766-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="79766-1342">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="79766-1343">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1343">Select **OK**.</span></span>
1. <span data-ttu-id="79766-1344">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="79766-1344">Recycle the worker process.</span></span>

<span data-ttu-id="79766-1345">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="79766-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="79766-1346">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="79766-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="79766-1347">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="79766-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="79766-1348">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79766-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="79766-1349">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="79766-1349">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="79766-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="79766-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="79766-1351">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="79766-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="79766-1352">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="79766-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="79766-1353">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="79766-1353">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="79766-1354">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="79766-1354">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="79766-1355">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="79766-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="79766-1356">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79766-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="79766-1357">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="79766-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="79766-1358">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="79766-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="79766-1359">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="79766-1359">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="79766-1360">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="79766-1361">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="79766-1362">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="79766-1362">Supported operating systems</span></span>

<span data-ttu-id="79766-1363">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="79766-1364">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="79766-1364">Windows 7 or later</span></span>
* <span data-ttu-id="79766-1365">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="79766-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="79766-1366">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="79766-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="79766-1367">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="79766-1368">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="79766-1369">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="79766-1370">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="79766-1370">Supported platforms</span></span>

<span data-ttu-id="79766-1371">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="79766-1372">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="79766-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="79766-1373">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="79766-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="79766-1374">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="79766-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="79766-1375">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="79766-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="79766-1376">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="79766-1377">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="79766-1378">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="79766-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="79766-1379">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。</span><span class="sxs-lookup"><span data-stu-id="79766-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="79766-1380">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="79766-1381">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="79766-1382">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="79766-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="79766-1383">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="79766-1385">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="79766-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="79766-1386">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="79766-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="79766-1387">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="79766-1388">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="79766-1389">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="79766-1390">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="79766-1391">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="79766-1392">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="79766-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="79766-1393">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="79766-1394">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="79766-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="79766-1395">`CreateDefaultBuilder` は [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成し、ベース パスとポートを [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に構成することで、IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="79766-1396">ASP.NET Core モジュールでは、バックエンド プロセスに割り当てる動的なポートが生成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="79766-1397">`CreateDefaultBuilder` では <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="79766-1398">`UseIISIntegration` では、localhost の IP アドレス (`127.0.0.1`) で動的なポートをリッスンするように Kestrel が構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="79766-1399">動的なポートが 1234 である場合、Kestrel は `127.0.0.1:1234` でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="79766-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="79766-1400">この構成によって、以下から提供されるその他の URL 構成が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="79766-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="79766-1401">Kestrel の Listen API</span><span class="sxs-lookup"><span data-stu-id="79766-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="79766-1402">[構成](xref:fundamentals/configuration/index) (または[コマンド ラインの --urls オプション](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="79766-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="79766-1403">モジュールを使用する場合、`UseUrls` または Kestrel の `Listen` API を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="79766-1404">`UseUrls` または `Listen` が呼び出されると、IIS なしでアプリを実行しているときのみ、Kestrel で指定したポートがリッスンされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="79766-1405">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="79766-1406">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="79766-1407">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="79766-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="79766-1408">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="79766-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="79766-1409">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="79766-1410">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="79766-1411">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="79766-1411">IIS options</span></span>

| <span data-ttu-id="79766-1412">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-1412">Option</span></span>                         | <span data-ttu-id="79766-1413">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-1413">Default</span></span> | <span data-ttu-id="79766-1414">設定</span><span class="sxs-lookup"><span data-stu-id="79766-1414">Setting</span></span> |
| ---
<span data-ttu-id="79766-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1416">'Blazor'</span></span>
- <span data-ttu-id="79766-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1417">'Identity'</span></span>
- <span data-ttu-id="79766-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1419">'Razor'</span></span>
- <span data-ttu-id="79766-1420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1422">'Blazor'</span></span>
- <span data-ttu-id="79766-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1423">'Identity'</span></span>
- <span data-ttu-id="79766-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1425">'Razor'</span></span>
- <span data-ttu-id="79766-1426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1428">'Blazor'</span></span>
- <span data-ttu-id="79766-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1429">'Identity'</span></span>
- <span data-ttu-id="79766-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1431">'Razor'</span></span>
- <span data-ttu-id="79766-1432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1434">'Blazor'</span></span>
- <span data-ttu-id="79766-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1435">'Identity'</span></span>
- <span data-ttu-id="79766-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1437">'Razor'</span></span>
- <span data-ttu-id="79766-1438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1440">'Blazor'</span></span>
- <span data-ttu-id="79766-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1441">'Identity'</span></span>
- <span data-ttu-id="79766-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1443">'Razor'</span></span>
- <span data-ttu-id="79766-1444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1446">'Blazor'</span></span>
- <span data-ttu-id="79766-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1447">'Identity'</span></span>
- <span data-ttu-id="79766-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1449">'Razor'</span></span>
- <span data-ttu-id="79766-1450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1452">'Blazor'</span></span>
- <span data-ttu-id="79766-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1453">'Identity'</span></span>
- <span data-ttu-id="79766-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1455">'Razor'</span></span>
- <span data-ttu-id="79766-1456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1458">'Blazor'</span></span>
- <span data-ttu-id="79766-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1459">'Identity'</span></span>
- <span data-ttu-id="79766-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1461">'Razor'</span></span>
- <span data-ttu-id="79766-1462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1464">'Blazor'</span></span>
- <span data-ttu-id="79766-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1465">'Identity'</span></span>
- <span data-ttu-id="79766-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1467">'Razor'</span></span>
- <span data-ttu-id="79766-1468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1470">'Blazor'</span></span>
- <span data-ttu-id="79766-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1471">'Identity'</span></span>
- <span data-ttu-id="79766-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1473">'Razor'</span></span>
- <span data-ttu-id="79766-1474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1476">'Blazor'</span></span>
- <span data-ttu-id="79766-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1477">'Identity'</span></span>
- <span data-ttu-id="79766-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1479">'Razor'</span></span>
- <span data-ttu-id="79766-1480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1482">'Blazor'</span></span>
- <span data-ttu-id="79766-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1483">'Identity'</span></span>
- <span data-ttu-id="79766-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1485">'Razor'</span></span>
- <span data-ttu-id="79766-1486">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1488">'Blazor'</span></span>
- <span data-ttu-id="79766-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1489">'Identity'</span></span>
- <span data-ttu-id="79766-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1491">'Razor'</span></span>
- <span data-ttu-id="79766-1492">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1494">'Blazor'</span></span>
- <span data-ttu-id="79766-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1495">'Identity'</span></span>
- <span data-ttu-id="79766-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1497">'Razor'</span></span>
- <span data-ttu-id="79766-1498">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-1499">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が IIS サーバーによって設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-1500">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-1501">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-1502">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-1503">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="79766-1504">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="79766-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="79766-1505">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="79766-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="79766-1506">オプション</span><span class="sxs-lookup"><span data-stu-id="79766-1506">Option</span></span>                         | <span data-ttu-id="79766-1507">既定値</span><span class="sxs-lookup"><span data-stu-id="79766-1507">Default</span></span> | <span data-ttu-id="79766-1508">設定</span><span class="sxs-lookup"><span data-stu-id="79766-1508">Setting</span></span> |
| ---
<span data-ttu-id="79766-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1510">'Blazor'</span></span>
- <span data-ttu-id="79766-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1511">'Identity'</span></span>
- <span data-ttu-id="79766-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1513">'Razor'</span></span>
- <span data-ttu-id="79766-1514">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1516">'Blazor'</span></span>
- <span data-ttu-id="79766-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1517">'Identity'</span></span>
- <span data-ttu-id="79766-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1519">'Razor'</span></span>
- <span data-ttu-id="79766-1520">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1522">'Blazor'</span></span>
- <span data-ttu-id="79766-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1523">'Identity'</span></span>
- <span data-ttu-id="79766-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1525">'Razor'</span></span>
- <span data-ttu-id="79766-1526">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1528">'Blazor'</span></span>
- <span data-ttu-id="79766-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1529">'Identity'</span></span>
- <span data-ttu-id="79766-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1531">'Razor'</span></span>
- <span data-ttu-id="79766-1532">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1534">'Blazor'</span></span>
- <span data-ttu-id="79766-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1535">'Identity'</span></span>
- <span data-ttu-id="79766-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1537">'Razor'</span></span>
- <span data-ttu-id="79766-1538">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1540">'Blazor'</span></span>
- <span data-ttu-id="79766-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1541">'Identity'</span></span>
- <span data-ttu-id="79766-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1543">'Razor'</span></span>
- <span data-ttu-id="79766-1544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1546">'Blazor'</span></span>
- <span data-ttu-id="79766-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1547">'Identity'</span></span>
- <span data-ttu-id="79766-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1549">'Razor'</span></span>
- <span data-ttu-id="79766-1550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1552">'Blazor'</span></span>
- <span data-ttu-id="79766-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1553">'Identity'</span></span>
- <span data-ttu-id="79766-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1555">'Razor'</span></span>
- <span data-ttu-id="79766-1556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1558">'Blazor'</span></span>
- <span data-ttu-id="79766-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1559">'Identity'</span></span>
- <span data-ttu-id="79766-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1561">'Razor'</span></span>
- <span data-ttu-id="79766-1562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1564">'Blazor'</span></span>
- <span data-ttu-id="79766-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1565">'Identity'</span></span>
- <span data-ttu-id="79766-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1567">'Razor'</span></span>
- <span data-ttu-id="79766-1568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1570">'Blazor'</span></span>
- <span data-ttu-id="79766-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1571">'Identity'</span></span>
- <span data-ttu-id="79766-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1573">'Razor'</span></span>
- <span data-ttu-id="79766-1574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1576">'Blazor'</span></span>
- <span data-ttu-id="79766-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1577">'Identity'</span></span>
- <span data-ttu-id="79766-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1579">'Razor'</span></span>
- <span data-ttu-id="79766-1580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="79766-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1582">'Blazor'</span></span>
- <span data-ttu-id="79766-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1583">'Identity'</span></span>
- <span data-ttu-id="79766-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1585">'Razor'</span></span>
- <span data-ttu-id="79766-1586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="79766-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="79766-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="79766-1588">'Blazor'</span></span>
- <span data-ttu-id="79766-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="79766-1589">'Identity'</span></span>
- <span data-ttu-id="79766-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="79766-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="79766-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="79766-1591">'Razor'</span></span>
- <span data-ttu-id="79766-1592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="79766-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="79766-1593">---- | | `AutomaticAuthentication`      | `true`  | `true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="79766-1594">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="79766-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="79766-1595">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="79766-1596">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="79766-1597">| | `AuthenticationDisplayName`    | `null`  | ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="79766-1598">| | `ForwardClientCertificate`     | `true`  | `true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="79766-1599">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="79766-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="79766-1600">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="79766-1601">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="79766-1602">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="79766-1603">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-1603">web.config file</span></span>

<span data-ttu-id="79766-1604">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-1605">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="79766-1606">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="79766-1607">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="79766-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="79766-1608">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="79766-1609">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="79766-1610">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="79766-1611">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="79766-1612">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="79766-1613">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="79766-1614">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="79766-1615">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="79766-1616">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="79766-1616">web.config file location</span></span>

<span data-ttu-id="79766-1617">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="79766-1618">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="79766-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="79766-1619">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="79766-1620">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="79766-1621">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="79766-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="79766-1622">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="79766-1623">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="79766-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="79766-1624">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="79766-1624">Transform web.config</span></span>

<span data-ttu-id="79766-1625">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="79766-1626">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="79766-1626">IIS configuration</span></span>

<span data-ttu-id="79766-1627">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="79766-1628">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="79766-1629">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="79766-1630">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="79766-1632">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="79766-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="79766-1633">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="79766-1635">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-1636">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="79766-1637">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-1638">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-1639">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-1640">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-1641">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="79766-1642">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-1643">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-1644">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="79766-1645">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="79766-1646">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="79766-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="79766-1647">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-1648">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="79766-1649">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="79766-1650">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="79766-1651">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="79766-1652">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="79766-1653">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="79766-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="79766-1654">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="79766-1655">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="79766-1656">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="79766-1657">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="79766-1658">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="79766-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="79766-1659">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="79766-1660">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="79766-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="79766-1661">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="79766-1662">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="79766-1663">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1663">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="79766-1665">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="79766-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="79766-1666">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="79766-1667">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="79766-1668">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="79766-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="79766-1669">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="79766-1670">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="79766-1671">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="79766-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="79766-1672">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="79766-1673">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="79766-1673">Download</span></span>

1. <span data-ttu-id="79766-1674">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="79766-1675">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="79766-1676">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="79766-1677">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="79766-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="79766-1678">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="79766-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="79766-1679">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="79766-1680">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="79766-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="79766-1681">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1681">Run the installer on the server.</span></span> <span data-ttu-id="79766-1682">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="79766-1683">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="79766-1684">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="79766-1685">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-1686">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="79766-1687">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="79766-1688">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="79766-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="79766-1689">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="79766-1690">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="79766-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="79766-1692">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="79766-1693">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="79766-1694">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="79766-1695">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="79766-1696">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="79766-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="79766-1697">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="79766-1698">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="79766-1699">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="79766-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="79766-1700">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="79766-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="79766-1701">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="79766-1702">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="79766-1703">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="79766-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="79766-1704">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="79766-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="79766-1705">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="79766-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="79766-1706">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="79766-1707">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="79766-1708">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="79766-1708">Create the IIS site</span></span>

1. <span data-ttu-id="79766-1709">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="79766-1710">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="79766-1711">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="79766-1712">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="79766-1713">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="79766-1714">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-1715">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="79766-1716">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="79766-1718">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="79766-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="79766-1719">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="79766-1720">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="79766-1721">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="79766-1722">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="79766-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="79766-1723">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="79766-1724">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="79766-1725">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="79766-1726">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="79766-1728">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="79766-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="79766-1729">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="79766-1730">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="79766-1731">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="79766-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="79766-1732">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="79766-1733">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="79766-1734">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="79766-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="79766-1735">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="79766-1736">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="79766-1737">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="79766-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="79766-1738">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="79766-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="79766-1739">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="79766-1740">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="79766-1740">Deploy the app</span></span>

<span data-ttu-id="79766-1741">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="79766-1742">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="79766-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="79766-1743">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="79766-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="79766-1744">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="79766-1745">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="79766-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="79766-1747">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="79766-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="79766-1748">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="79766-1749">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="79766-1750">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="79766-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="79766-1751">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="79766-1752">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="79766-1753">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="79766-1753">Browse the website</span></span>

<span data-ttu-id="79766-1754">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="79766-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="79766-1755">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="79766-1756">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-1756">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="79766-1758">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="79766-1758">Locked deployment files</span></span>

<span data-ttu-id="79766-1759">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="79766-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="79766-1760">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="79766-1761">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-1762">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="79766-1763">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="79766-1764">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="79766-1765">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="79766-1766">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="79766-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="79766-1767">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="79766-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="79766-1768">データの保護</span><span class="sxs-lookup"><span data-stu-id="79766-1768">Data protection</span></span>

<span data-ttu-id="79766-1769">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="79766-1770">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="79766-1771">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="79766-1772">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="79766-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="79766-1773">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="79766-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="79766-1774">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="79766-1775">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="79766-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="79766-1776">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="79766-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="79766-1777">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="79766-1778">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="79766-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="79766-1779">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="79766-1780">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="79766-1781">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="79766-1782">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="79766-1783">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="79766-1784">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="79766-1785">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="79766-1786">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="79766-1787">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="79766-1788">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="79766-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="79766-1789">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="79766-1790">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="79766-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="79766-1791">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="79766-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="79766-1792">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="79766-1793">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="79766-1794">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="79766-1795">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="79766-1796">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="79766-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="79766-1797">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="79766-1798">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="79766-1799">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="79766-1800">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="79766-1801">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="79766-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="79766-1802">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="79766-1803">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="79766-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="79766-1804">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="79766-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="79766-1805">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="79766-1806">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="79766-1807">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="79766-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="79766-1808">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="79766-1809">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="79766-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="79766-1810">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="79766-1811">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="79766-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="79766-1812">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="79766-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="79766-1813">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="79766-1814">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="79766-1814">Virtual Directories</span></span>

<span data-ttu-id="79766-1815">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="79766-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="79766-1816">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="79766-1817">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="79766-1817">Sub-applications</span></span>

<span data-ttu-id="79766-1818">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="79766-1819">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="79766-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="79766-1820">サブアプリに、ハンドラーとして ASP.NET Core モジュールを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="79766-1821">このモジュールをサブアプリの *web.config* ファイルにハンドラーとして追加すると、サブアプリを閲覧しようとすると、エラーのある構成ファイルを参照する *500.19 内部サーバー エラー* が返されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="79766-1822">次の例は、ASP.NET Core サブアプリの発行済み *web.config* ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="79766-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="79766-1823">ASP.NET Core アプリの下に ASP.NET Core 以外のサブアプリをホスティングする場合は、サブアプリの *web.config* ファイルにある継承されたハンドラーを明示的に削除します。</span><span class="sxs-lookup"><span data-stu-id="79766-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="79766-1824">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="79766-1825">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="79766-1826">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="79766-1827">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="79766-1828">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="79766-1829">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="79766-1830">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="79766-1831">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="79766-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="79766-1832">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="79766-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="79766-1833">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="79766-1834">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="79766-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="79766-1835">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="79766-1836">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="79766-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="79766-1837">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1837">Select **OK**.</span></span>

<span data-ttu-id="79766-1838">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="79766-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="79766-1839">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="79766-1840">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="79766-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="79766-1841">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="79766-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="79766-1842">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="79766-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="79766-1843">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="79766-1844">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="79766-1845">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="79766-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="79766-1846">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables> ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある "*AppCmd.exe コマンド*" のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="79766-1847">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="79766-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="79766-1848">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="79766-1849">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="79766-1850">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="79766-1851">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="79766-1851">Application Pools</span></span>

<span data-ttu-id="79766-1852">サーバーで複数の Web サイトをホストする場合は、アプリをそれぞれ専用のアプリ プールで実行して、アプリを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="79766-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="79766-1853">IIS の **[Web サイトの追加]** ダイアログはこの構成の既定です。</span><span class="sxs-lookup"><span data-stu-id="79766-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="79766-1854">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="79766-1855">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="79766-1856">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="79766-1856">Application Pool Identity</span></span>

<span data-ttu-id="79766-1857">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="79766-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="79766-1858">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="79766-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="79766-1859">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="79766-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="79766-1861">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="79766-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="79766-1862">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="79766-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="79766-1863">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="79766-1864">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="79766-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="79766-1865">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="79766-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="79766-1866">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="79766-1867">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="79766-1868">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="79766-1869">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="79766-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="79766-1870">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="79766-1871">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="79766-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="79766-1872">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="79766-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="79766-1873">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="79766-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="79766-1874">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="79766-1876">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="79766-1876">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="79766-1878">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="79766-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="79766-1879">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="79766-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="79766-1880">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="79766-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="79766-1881">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="79766-1882">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="79766-1883">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="79766-1883">HTTP/2 support</span></span>

<span data-ttu-id="79766-1884">次の基本要件を満たすアウトプロセスの展開には、[HTTP/2](https://httpwg.org/specs/rfc7540.html) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="79766-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="79766-1885">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="79766-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="79766-1886">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="79766-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="79766-1887">ターゲット フレームワーク:HTTP/2 接続は IIS によって完全に処理されるため、アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="79766-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="79766-1888">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="79766-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="79766-1889">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="79766-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="79766-1890">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="79766-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="79766-1891">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="79766-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="79766-1892">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="79766-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="79766-1893">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="79766-1893">CORS preflight requests</span></span>

<span data-ttu-id="79766-1894">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="79766-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="79766-1895">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="79766-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="79766-1896">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="79766-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="79766-1897">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="79766-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="79766-1898">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="79766-1898">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="79766-1899">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="79766-1899">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="79766-1900">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="79766-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="79766-1901">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="79766-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="79766-1902">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="79766-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="79766-1903">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="79766-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="79766-1904">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="79766-1904">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
