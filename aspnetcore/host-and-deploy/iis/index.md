---
title: IIS を使用した Windows での ASP.NET Core のホスト
author: rick-anderson
description: Windows Server インターネット インフォメーション サービス (IIS) での ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/index
ms.openlocfilehash: c3841babe213a9a3f303b8f9b83a947fd33ad647
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003125"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="6dc95-103">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="6dc95-103">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6dc95-104">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-104">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="6dc95-105">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-105">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="6dc95-106">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="6dc95-106">Supported operating systems</span></span>

<span data-ttu-id="6dc95-107">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-107">The following operating systems are supported:</span></span>

* <span data-ttu-id="6dc95-108">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-108">Windows 7 or later</span></span>
* <span data-ttu-id="6dc95-109">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-109">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="6dc95-110">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-110">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="6dc95-111">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-111">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="6dc95-112">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-112">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="6dc95-113">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-113">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="6dc95-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="6dc95-114">Supported platforms</span></span>

<span data-ttu-id="6dc95-115">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-115">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="6dc95-116">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="6dc95-116">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="6dc95-117">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-117">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="6dc95-118">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-118">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="6dc95-119">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="6dc95-119">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="6dc95-120">32 ビット (x86) 用に公開されたアプリでは、IIS アプリケーション プールで 32 ビットが有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-120">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="6dc95-121">詳細については、「[IIS サイトを作成する](#create-the-iis-site)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-121">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="6dc95-122">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-122">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="6dc95-123">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-123">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="6dc95-124">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-124">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="6dc95-125">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-125">In-process hosting model</span></span>

<span data-ttu-id="6dc95-126">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-126">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="6dc95-127">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-127">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="6dc95-128">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-128">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6dc95-129">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module): </span><span class="sxs-lookup"><span data-stu-id="6dc95-129">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="6dc95-130">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-130">Performs app initialization.</span></span>
  * <span data-ttu-id="6dc95-131">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-131">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="6dc95-132">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="6dc95-132">Calls `Program.Main`.</span></span>
* <span data-ttu-id="6dc95-133">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-133">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="6dc95-134">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-134">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="6dc95-136">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-136">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="6dc95-137">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-137">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="6dc95-138">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-138">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="6dc95-139">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-139">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="6dc95-140">IIS HTTP サーバーによって要求が処理された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-140">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="6dc95-141">要求は ASP.NET Core ミドルウェア パイプラインに送信されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-141">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="6dc95-142">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-142">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="6dc95-143">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-143">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="6dc95-144">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-144">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="6dc95-145">既存のアプリではインプロセス ホスティングがオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-145">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="6dc95-146">ASP.NET Core Web テンプレートでは、インプロセス ホスティング モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-146">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="6dc95-147">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="6dc95-148">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) へのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="6dc95-149">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="6dc95-150">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-150">Out-of-process hosting model</span></span>

<span data-ttu-id="6dc95-151">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="6dc95-152">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="6dc95-153">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6dc95-154">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="6dc95-156">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="6dc95-157">ドライバーは、Web サイトの構成ポートで IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-157">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="6dc95-158">構成されるポートは、通常 80 (HTTP) または 443 (HTTPS) です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-158">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="6dc95-159">モジュールでは、アプリのランダムなポートで Kestrel に要求を転送します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-159">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="6dc95-160">ランダムなポートは 80 または 443 ではありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-160">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="6dc95-161">ASP.NET Core モジュールは、起動時に環境変数によってポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-161">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="6dc95-162"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能は、`http://localhost:{PORT}` でリッスンするようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-162">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="6dc95-163">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-163">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="6dc95-164">モジュールは HTTPS 転送をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-164">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="6dc95-165">要求は HTTPS を介して IIS によって受信された場合でも HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-165">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="6dc95-166">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインに転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-166">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="6dc95-167">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="6dc95-168">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-168">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="6dc95-169">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-169">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="6dc95-170">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-170">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6dc95-171">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-171">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="6dc95-172">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-172">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="6dc95-173">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="6dc95-173">Enable the IISIntegration components</span></span>

<span data-ttu-id="6dc95-174">`CreateHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-174">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="6dc95-175">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-175">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="6dc95-176">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-176">IIS options</span></span>

<span data-ttu-id="6dc95-177">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="6dc95-177">**In-process hosting model**</span></span>

<span data-ttu-id="6dc95-178">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-178">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="6dc95-179">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-179">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="6dc95-180">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-180">Option</span></span>                         | <span data-ttu-id="6dc95-181">既定値</span><span class="sxs-lookup"><span data-stu-id="6dc95-181">Default</span></span> | <span data-ttu-id="6dc95-182">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-182">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-183">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-183">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-184">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-184">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-185">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-185">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-186">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-186">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-187">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-187">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="6dc95-188">`HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるか。</span><span class="sxs-lookup"><span data-stu-id="6dc95-188">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="6dc95-189">`HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-189">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="6dc95-190">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-190">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="6dc95-191">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-191">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="6dc95-192">`maxAllowedContentLength`を引き上げるには、*web.config* 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-192">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="6dc95-193">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-193">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="6dc95-194">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="6dc95-194">**Out-of-process hosting model**</span></span>

<span data-ttu-id="6dc95-195">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-195">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="6dc95-196">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-196">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="6dc95-197">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-197">Option</span></span>                         | <span data-ttu-id="6dc95-198">既定値</span><span class="sxs-lookup"><span data-stu-id="6dc95-198">Default</span></span> | <span data-ttu-id="6dc95-199">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-199">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-200">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-200">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-201">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-201">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-202">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-202">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-203">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-203">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-204">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-204">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="6dc95-205">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-205">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6dc95-206">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="6dc95-206">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6dc95-207">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)と ASP.NET Core モジュールは、次を転送するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-207">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="6dc95-208">スキーム (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-208">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="6dc95-209">要求元のリモート IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="6dc95-209">Remote IP address where the request originated.</span></span>

<span data-ttu-id="6dc95-210">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)は、Forwarded Headers Middleware を構成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-210">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="6dc95-211">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-211">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="6dc95-212">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-212">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="6dc95-213">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-213">web.config file</span></span>

<span data-ttu-id="6dc95-214">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-214">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-215">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-215">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="6dc95-216">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-216">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="6dc95-217">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-217">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6dc95-218">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-218">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="6dc95-219">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-219">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="6dc95-220">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-220">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="6dc95-221">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-221">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="6dc95-222">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-222">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="6dc95-223">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-223">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="6dc95-224">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-224">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="6dc95-225">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-225">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="6dc95-226">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="6dc95-226">web.config file location</span></span>

<span data-ttu-id="6dc95-227">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-227">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="6dc95-228">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-228">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="6dc95-229">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-229">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="6dc95-230">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-230">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="6dc95-231">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-231">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="6dc95-232">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-232">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="6dc95-233">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="6dc95-233">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="6dc95-234">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="6dc95-234">Transform web.config</span></span>

<span data-ttu-id="6dc95-235">発行時に "*web.config*" を変換する場合は、「<xref:host-and-deploy/iis/transform-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-235">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="6dc95-236">発行時に "*web.config*" を変換して、構成、プロファイル、環境に基づいた環境変数を設定しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-236">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="6dc95-237">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-237">IIS configuration</span></span>

<span data-ttu-id="6dc95-238">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-238">**Windows Server operating systems**</span></span>

<span data-ttu-id="6dc95-239">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-239">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="6dc95-240">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-240">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="6dc95-241">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-241">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="6dc95-243">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-243">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="6dc95-244">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-244">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="6dc95-246">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-246">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-247">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-247">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="6dc95-248">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-248">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-249">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-249">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-250">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-250">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-251">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-251">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-252">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-252">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="6dc95-253">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-253">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-254">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-254">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-255">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-255">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="6dc95-256">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-256">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="6dc95-257">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-257">**Windows desktop operating systems**</span></span>

<span data-ttu-id="6dc95-258">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-258">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-259">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-259">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="6dc95-260">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-260">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="6dc95-261">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-261">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="6dc95-262">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-262">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="6dc95-263">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-263">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-264">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-264">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="6dc95-265">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-265">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-266">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-266">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="6dc95-267">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-267">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-268">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-268">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-269">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-269">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-270">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-270">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-271">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-271">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="6dc95-272">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-272">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-273">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-273">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-274">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-274">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6dc95-276">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-276">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6dc95-277">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-277">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="6dc95-278">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-278">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-279">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-279">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6dc95-280">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-280">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="6dc95-281">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-281">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="6dc95-282">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="6dc95-282">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6dc95-283">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-283">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="6dc95-284">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="6dc95-284">Direct download (current version)</span></span>

<span data-ttu-id="6dc95-285">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-285">Download the installer using the following link:</span></span>

[<span data-ttu-id="6dc95-286">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="6dc95-286">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="6dc95-287">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="6dc95-287">Earlier versions of the installer</span></span>

<span data-ttu-id="6dc95-288">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="6dc95-288">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="6dc95-289">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-289">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="6dc95-290">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-290">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="6dc95-291">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-291">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="6dc95-292">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-292">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="6dc95-293">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-293">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="6dc95-294">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-294">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="6dc95-295">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="6dc95-295">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="6dc95-296">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-296">Run the installer on the server.</span></span> <span data-ttu-id="6dc95-297">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-297">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="6dc95-298">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-298">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="6dc95-299">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-299">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="6dc95-300">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-300">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-301">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-301">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="6dc95-302">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-302">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-303">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-303">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="6dc95-304">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-304">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="6dc95-305">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-305">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="6dc95-306">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-306">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="6dc95-307">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-307">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="6dc95-308">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-308">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="6dc95-309">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-309">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="6dc95-310">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-310">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="6dc95-311">ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-311">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="6dc95-312">新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-312">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="6dc95-313">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-313">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="6dc95-314">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-314">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="6dc95-315">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-315">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="6dc95-316">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-316">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="6dc95-317">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-317">The preferred method is to use WebPI.</span></span> <span data-ttu-id="6dc95-318">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-318">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6dc95-319">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="6dc95-319">Create the IIS site</span></span>

1. <span data-ttu-id="6dc95-320">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-320">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6dc95-321">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-321">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="6dc95-322">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-322">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="6dc95-323">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-323">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6dc95-324">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-324">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6dc95-325">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-325">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-326">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-326">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="6dc95-327">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-327">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="6dc95-329">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="6dc95-329">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6dc95-330">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-330">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6dc95-331">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-331">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6dc95-332">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-332">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6dc95-333">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-333">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6dc95-334">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-334">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6dc95-335">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-335">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="6dc95-336">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-336">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-337">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-337">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="6dc95-339">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-339">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="6dc95-340">ASP.NET Core を使用するためにデスクトップ CLR (.NET CLR) を読み込む必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-340">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="6dc95-341">.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-341">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="6dc95-342">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-342">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="6dc95-343">*ASP.NET Core 2.2 以降*:</span><span class="sxs-lookup"><span data-stu-id="6dc95-343">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="6dc95-344">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 32 ビット SDK で公開される 32 ビット (x86) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット用のアプリケーション プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-344">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="6dc95-345">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-345">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="6dc95-346">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-346">Select the app's Application Pool.</span></span> <span data-ttu-id="6dc95-347">**[操作]** サイドバーで、[**詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-347">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="6dc95-348">**[32 ビット アプリケーションの有効化]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-348">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="6dc95-349">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-349">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="6dc95-350">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-350">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="6dc95-351">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-351">Select the app's Application Pool.</span></span> <span data-ttu-id="6dc95-352">**[操作]** サイドバーで、[**詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-352">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="6dc95-353">**[32 ビット アプリケーションの有効化]** を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-353">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="6dc95-354">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-354">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6dc95-355">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-355">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6dc95-356">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-356">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="6dc95-357">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-357">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="6dc95-358">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-358">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="6dc95-359">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="6dc95-359">Deploy the app</span></span>

<span data-ttu-id="6dc95-360">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-360">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="6dc95-361">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-361">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="6dc95-362">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-362">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="6dc95-363">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-363">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="6dc95-364">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-364">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="6dc95-366">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="6dc95-366">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="6dc95-367">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-367">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="6dc95-368">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-368">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="6dc95-369">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="6dc95-369">Alternatives to Web Deploy</span></span>

<span data-ttu-id="6dc95-370">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-370">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="6dc95-371">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-371">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="6dc95-372">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="6dc95-372">Browse the website</span></span>

<span data-ttu-id="6dc95-373">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-373">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="6dc95-374">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-374">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="6dc95-375">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-375">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="6dc95-377">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-377">Locked deployment files</span></span>

<span data-ttu-id="6dc95-378">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-378">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="6dc95-379">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-379">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="6dc95-380">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-380">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-381">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-381">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="6dc95-382">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-382">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="6dc95-383">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-383">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="6dc95-384">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-384">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="6dc95-385">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-385">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="6dc95-386">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-386">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="6dc95-387">データの保護</span><span class="sxs-lookup"><span data-stu-id="6dc95-387">Data protection</span></span>

<span data-ttu-id="6dc95-388">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-388">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="6dc95-389">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-389">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6dc95-390">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-390">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6dc95-391">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-391">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6dc95-392">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-392">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="6dc95-393">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-393">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="6dc95-394">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-394">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6dc95-395">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-395">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6dc95-396">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-396">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-397">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-397">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="6dc95-398">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-398">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="6dc95-399">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-399">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="6dc95-400">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-400">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="6dc95-401">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-401">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="6dc95-402">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-402">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="6dc95-403">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-403">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="6dc95-404">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-404">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="6dc95-405">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-405">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="6dc95-406">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-406">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="6dc95-407">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-407">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="6dc95-408">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-408">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="6dc95-409">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-409">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="6dc95-410">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-410">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="6dc95-411">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-411">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="6dc95-412">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-412">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="6dc95-413">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-413">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="6dc95-414">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-414">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="6dc95-415">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-415">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="6dc95-416">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-416">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="6dc95-417">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-417">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="6dc95-418">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-418">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="6dc95-419">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-419">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="6dc95-420">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-420">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="6dc95-421">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-421">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="6dc95-422">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-422">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="6dc95-423">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-423">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="6dc95-424">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-424">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="6dc95-425">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-425">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="6dc95-426">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-426">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="6dc95-427">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-427">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="6dc95-428">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-428">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="6dc95-429">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-429">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="6dc95-430">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-430">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="6dc95-431">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-431">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="6dc95-432">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-432">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="6dc95-433">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-433">Virtual Directories</span></span>

<span data-ttu-id="6dc95-434">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-434">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="6dc95-435">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-435">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="6dc95-436">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6dc95-436">Sub-applications</span></span>

<span data-ttu-id="6dc95-437">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-437">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="6dc95-438">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-438">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="6dc95-439">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-439">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="6dc95-440">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-440">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="6dc95-441">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-441">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="6dc95-442">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-442">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="6dc95-443">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-443">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="6dc95-444">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-444">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="6dc95-445">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-445">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="6dc95-446">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="6dc95-446">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="6dc95-447">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-447">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="6dc95-448">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-448">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="6dc95-449">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-449">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="6dc95-450">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-450">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="6dc95-451">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-451">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="6dc95-452">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-452">Select **OK**.</span></span>

<span data-ttu-id="6dc95-453">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-453">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="6dc95-454">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-454">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="6dc95-455">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-455">Configuration of IIS with web.config</span></span>

<span data-ttu-id="6dc95-456">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-456">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="6dc95-457">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-457">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="6dc95-458">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-458">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="6dc95-459">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-459">For more information, see the following topics:</span></span>

* [<span data-ttu-id="6dc95-460">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="6dc95-460">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="6dc95-461">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-461">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="6dc95-462">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="6dc95-462">Configuration sections of web.config</span></span>

<span data-ttu-id="6dc95-463">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-463">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="6dc95-464">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-464">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="6dc95-465">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-465">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="6dc95-466">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="6dc95-466">Application Pools</span></span>

<span data-ttu-id="6dc95-467">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-467">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="6dc95-468">インプロセス ホスティング &ndash; 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-468">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="6dc95-469">アウトプロセス ホスティング &ndash; アプリケーションをそれぞれ専用のアプリケーションプールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-469">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="6dc95-470">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-470">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="6dc95-471">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-471">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6dc95-472">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-472">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="6dc95-473">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="6dc95-473">Application Pool Identity</span></span>

<span data-ttu-id="6dc95-474">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-474">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="6dc95-475">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-475">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="6dc95-476">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-476">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="6dc95-478">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-478">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="6dc95-479">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-479">Resources can be secured using this identity.</span></span> <span data-ttu-id="6dc95-480">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-480">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="6dc95-481">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-481">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="6dc95-482">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-482">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="6dc95-483">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-483">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="6dc95-484">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-484">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="6dc95-485">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-485">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="6dc95-486">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-486">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="6dc95-487">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-487">Select the **Check Names** button.</span></span> <span data-ttu-id="6dc95-488">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-488">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="6dc95-489">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-489">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="6dc95-490">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-490">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="6dc95-491">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-491">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="6dc95-493">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-493">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="6dc95-495">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-495">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="6dc95-496">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-496">Provide additional permissions as needed.</span></span>

<span data-ttu-id="6dc95-497">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-497">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="6dc95-498">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-498">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="6dc95-499">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-499">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="6dc95-500">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="6dc95-500">HTTP/2 support</span></span>

<span data-ttu-id="6dc95-501">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-501">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="6dc95-502">インプロセス</span><span class="sxs-lookup"><span data-stu-id="6dc95-502">In-process</span></span>
  * <span data-ttu-id="6dc95-503">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-503">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6dc95-504">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="6dc95-504">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="6dc95-505">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="6dc95-505">Out-of-process</span></span>
  * <span data-ttu-id="6dc95-506">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-506">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6dc95-507">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-507">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="6dc95-508">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="6dc95-508">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6dc95-509">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-509">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="6dc95-510">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-510">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="6dc95-511">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-511">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6dc95-512">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-512">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="6dc95-513">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-513">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="6dc95-514">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-514">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="6dc95-515">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="6dc95-515">CORS preflight requests</span></span>

<span data-ttu-id="6dc95-516">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-516">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="6dc95-517">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-517">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="6dc95-518">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-518">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="6dc95-519">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="6dc95-519">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="6dc95-520">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-520">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="6dc95-521">[Application Initialization モジュール](#application-initialization-module) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-521">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="6dc95-522">[アイドル タイムアウト](#idle-timeout) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-522">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="6dc95-523">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="6dc95-523">Application Initialization Module</span></span>

<span data-ttu-id="6dc95-524">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-524">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="6dc95-525">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-525">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="6dc95-526">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-526">The request triggers the app to start.</span></span> <span data-ttu-id="6dc95-527">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-527">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="6dc95-528">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-528">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="6dc95-529">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-529">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="6dc95-530">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-530">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="6dc95-531">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-531">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="6dc95-532">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-532">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6dc95-533">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-533">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="6dc95-534">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-534">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="6dc95-535">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-535">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="6dc95-536">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-536">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6dc95-537">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-537">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="6dc95-538">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-538">Using IIS Manager:</span></span>

  1. <span data-ttu-id="6dc95-539">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-539">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6dc95-540">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-540">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6dc95-541">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-541">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="6dc95-542">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-542">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="6dc95-543">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-543">Select **OK**.</span></span>
  1. <span data-ttu-id="6dc95-544">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-544">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6dc95-545">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-545">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6dc95-546">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-546">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="6dc95-547">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-547">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="6dc95-548">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-548">Select **OK**.</span></span>

* <span data-ttu-id="6dc95-549">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-549">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="6dc95-550">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="6dc95-550">Idle Timeout</span></span>

<span data-ttu-id="6dc95-551">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-551">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="6dc95-552">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-552">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="6dc95-553">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-553">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="6dc95-554">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-554">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="6dc95-555">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-555">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="6dc95-556">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-556">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="6dc95-557">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-557">Select **OK**.</span></span>
1. <span data-ttu-id="6dc95-558">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-558">Recycle the worker process.</span></span>

<span data-ttu-id="6dc95-559">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-559">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="6dc95-560">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-560">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="6dc95-561">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-561">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="6dc95-562">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6dc95-562">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="6dc95-563">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="6dc95-563">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="6dc95-564">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-564">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="6dc95-565">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-565">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6dc95-566">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="6dc95-566">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="6dc95-567">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="6dc95-567">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="6dc95-568">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="6dc95-568">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="6dc95-569">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-569">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="6dc95-570">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6dc95-570">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="6dc95-571">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="6dc95-571">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6dc95-572">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-572">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="6dc95-573">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6dc95-573">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6dc95-574">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-574">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="6dc95-575">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-575">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="6dc95-576">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="6dc95-576">Supported operating systems</span></span>

<span data-ttu-id="6dc95-577">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-577">The following operating systems are supported:</span></span>

* <span data-ttu-id="6dc95-578">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-578">Windows 7 or later</span></span>
* <span data-ttu-id="6dc95-579">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-579">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6dc95-580">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-580">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="6dc95-581">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-581">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="6dc95-582">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-582">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="6dc95-583">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-583">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="6dc95-584">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="6dc95-584">Supported platforms</span></span>

<span data-ttu-id="6dc95-585">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-585">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="6dc95-586">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="6dc95-586">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="6dc95-587">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-587">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="6dc95-588">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-588">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="6dc95-589">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="6dc95-589">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="6dc95-590">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-590">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="6dc95-591">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-591">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="6dc95-592">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-592">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="6dc95-593">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-593">In-process hosting model</span></span>

<span data-ttu-id="6dc95-594">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-594">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="6dc95-595">インプロセス ホスティングでは、次の理由により、アウトプロセス ホスティングよりもパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-595">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="6dc95-596">要求はループバック アダプター経由でプロキシ化されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-596">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="6dc95-597">ループバック アダプターは、同じマシンに送信ネットワーク トラフィックを戻すネットワーク インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-597">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="6dc95-598">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-598">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6dc95-599">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module): </span><span class="sxs-lookup"><span data-stu-id="6dc95-599">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="6dc95-600">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-600">Performs app initialization.</span></span>
  * <span data-ttu-id="6dc95-601">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-601">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="6dc95-602">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="6dc95-602">Calls `Program.Main`.</span></span>
* <span data-ttu-id="6dc95-603">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-603">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="6dc95-604">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-604">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="6dc95-605">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-605">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="6dc95-607">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-607">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="6dc95-608">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-608">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="6dc95-609">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-609">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="6dc95-610">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-610">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="6dc95-611">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-611">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="6dc95-612">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-612">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="6dc95-613">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-613">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="6dc95-614">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-614">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="6dc95-615">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-615">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="6dc95-616">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (*w3wp.exe* または *iisexpress.exe*) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-616">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="6dc95-617">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-617">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="6dc95-618">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="6dc95-618">Out-of-process hosting model</span></span>

<span data-ttu-id="6dc95-619">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-619">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="6dc95-620">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-620">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="6dc95-621">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-621">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6dc95-622">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-622">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="6dc95-624">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-624">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="6dc95-625">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-625">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="6dc95-626">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-626">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="6dc95-627">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-627">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="6dc95-628">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-628">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="6dc95-629">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-629">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="6dc95-630">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-630">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="6dc95-631">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-631">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="6dc95-632">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-632">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="6dc95-633">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-633">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="6dc95-634">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-634">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6dc95-635">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-635">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="6dc95-636">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-636">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="6dc95-637">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="6dc95-637">Enable the IISIntegration components</span></span>

<span data-ttu-id="6dc95-638">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-638">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="6dc95-639">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-639">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="6dc95-640">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-640">IIS options</span></span>

<span data-ttu-id="6dc95-641">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="6dc95-641">**In-process hosting model**</span></span>

<span data-ttu-id="6dc95-642">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-642">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="6dc95-643">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-643">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="6dc95-644">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-644">Option</span></span>                         | <span data-ttu-id="6dc95-645">既定値</span><span class="sxs-lookup"><span data-stu-id="6dc95-645">Default</span></span> | <span data-ttu-id="6dc95-646">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-646">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-647">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-647">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-648">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-648">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-649">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-649">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-650">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-650">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-651">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-651">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="6dc95-652">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="6dc95-652">**Out-of-process hosting model**</span></span>

<span data-ttu-id="6dc95-653">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-653">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="6dc95-654">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-654">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="6dc95-655">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-655">Option</span></span>                         | <span data-ttu-id="6dc95-656">既定値</span><span class="sxs-lookup"><span data-stu-id="6dc95-656">Default</span></span> | <span data-ttu-id="6dc95-657">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-657">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-658">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-658">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-659">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-659">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-660">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-660">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-661">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-661">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-662">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-662">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="6dc95-663">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-663">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6dc95-664">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="6dc95-664">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6dc95-665">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-665">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="6dc95-666">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-666">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="6dc95-667">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-667">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="6dc95-668">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-668">web.config file</span></span>

<span data-ttu-id="6dc95-669">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-669">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-670">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-670">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="6dc95-671">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-671">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="6dc95-672">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-672">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6dc95-673">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-673">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="6dc95-674">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-674">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="6dc95-675">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-675">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="6dc95-676">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-676">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="6dc95-677">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-677">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="6dc95-678">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-678">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="6dc95-679">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-679">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="6dc95-680">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-680">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="6dc95-681">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="6dc95-681">web.config file location</span></span>

<span data-ttu-id="6dc95-682">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-682">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="6dc95-683">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-683">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="6dc95-684">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-684">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="6dc95-685">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-685">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="6dc95-686">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-686">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="6dc95-687">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-687">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="6dc95-688">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="6dc95-688">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="6dc95-689">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="6dc95-689">Transform web.config</span></span>

<span data-ttu-id="6dc95-690">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-690">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="6dc95-691">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-691">IIS configuration</span></span>

<span data-ttu-id="6dc95-692">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-692">**Windows Server operating systems**</span></span>

<span data-ttu-id="6dc95-693">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-693">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="6dc95-694">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-694">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="6dc95-695">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-695">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="6dc95-697">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-697">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="6dc95-698">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-698">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="6dc95-700">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-700">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-701">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-701">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="6dc95-702">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-702">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-703">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-703">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-704">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-704">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-705">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-705">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-706">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-706">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="6dc95-707">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-707">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-708">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-708">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-709">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-709">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="6dc95-710">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-710">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="6dc95-711">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-711">**Windows desktop operating systems**</span></span>

<span data-ttu-id="6dc95-712">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-712">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-713">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-713">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="6dc95-714">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-714">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="6dc95-715">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-715">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="6dc95-716">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-716">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="6dc95-717">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-717">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-718">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-718">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="6dc95-719">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-719">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-720">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-720">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="6dc95-721">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-721">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-722">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-722">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-723">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-723">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-724">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-724">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-725">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-725">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="6dc95-726">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-726">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-727">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-727">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-728">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-728">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6dc95-730">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-730">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6dc95-731">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-731">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="6dc95-732">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-732">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-733">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-733">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6dc95-734">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-734">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="6dc95-735">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-735">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="6dc95-736">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="6dc95-736">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6dc95-737">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-737">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="6dc95-738">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="6dc95-738">Download</span></span>

1. <span data-ttu-id="6dc95-739">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-739">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="6dc95-740">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-740">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="6dc95-741">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-741">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="6dc95-742">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-742">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="6dc95-743">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-743">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="6dc95-744">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-744">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="6dc95-745">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="6dc95-745">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="6dc95-746">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-746">Run the installer on the server.</span></span> <span data-ttu-id="6dc95-747">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-747">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="6dc95-748">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-748">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="6dc95-749">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-749">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="6dc95-750">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-750">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-751">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-751">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="6dc95-752">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-752">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-753">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-753">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="6dc95-754">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-754">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="6dc95-755">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-755">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="6dc95-756">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-756">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="6dc95-757">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-757">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="6dc95-758">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-758">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="6dc95-759">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-759">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="6dc95-760">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-760">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="6dc95-761">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-761">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="6dc95-762">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-762">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="6dc95-763">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-763">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="6dc95-764">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-764">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="6dc95-765">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-765">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="6dc95-766">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-766">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="6dc95-767">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-767">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="6dc95-768">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-768">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="6dc95-769">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-769">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="6dc95-770">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-770">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="6dc95-771">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-771">The preferred method is to use WebPI.</span></span> <span data-ttu-id="6dc95-772">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-772">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6dc95-773">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="6dc95-773">Create the IIS site</span></span>

1. <span data-ttu-id="6dc95-774">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-774">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6dc95-775">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-775">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="6dc95-776">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-776">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="6dc95-777">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-777">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6dc95-778">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-778">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6dc95-779">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-779">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-780">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-780">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="6dc95-781">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-781">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="6dc95-783">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="6dc95-783">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6dc95-784">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-784">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6dc95-785">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-785">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6dc95-786">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-786">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6dc95-787">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-787">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6dc95-788">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-788">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6dc95-789">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-789">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="6dc95-790">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-790">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-791">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-791">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="6dc95-793">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-793">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="6dc95-794">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-794">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="6dc95-795">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-795">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="6dc95-796">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-796">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="6dc95-797">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-797">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="6dc95-798">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-798">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="6dc95-799">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-799">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="6dc95-800">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-800">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6dc95-801">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[ID]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-801">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6dc95-802">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-802">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="6dc95-803">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-803">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="6dc95-804">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-804">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="6dc95-805">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="6dc95-805">Deploy the app</span></span>

<span data-ttu-id="6dc95-806">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-806">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="6dc95-807">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-807">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="6dc95-808">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-808">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="6dc95-809">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-809">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="6dc95-810">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-810">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="6dc95-812">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="6dc95-812">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="6dc95-813">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-813">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="6dc95-814">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-814">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="6dc95-815">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="6dc95-815">Alternatives to Web Deploy</span></span>

<span data-ttu-id="6dc95-816">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-816">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="6dc95-817">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-817">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="6dc95-818">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="6dc95-818">Browse the website</span></span>

<span data-ttu-id="6dc95-819">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-819">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="6dc95-820">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-820">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="6dc95-821">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-821">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="6dc95-823">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-823">Locked deployment files</span></span>

<span data-ttu-id="6dc95-824">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-824">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="6dc95-825">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-825">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="6dc95-826">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-826">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-827">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-827">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="6dc95-828">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-828">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="6dc95-829">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-829">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="6dc95-830">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-830">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="6dc95-831">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-831">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="6dc95-832">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-832">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="6dc95-833">データの保護</span><span class="sxs-lookup"><span data-stu-id="6dc95-833">Data protection</span></span>

<span data-ttu-id="6dc95-834">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-834">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="6dc95-835">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-835">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6dc95-836">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-836">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6dc95-837">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-837">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6dc95-838">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-838">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="6dc95-839">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-839">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="6dc95-840">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-840">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6dc95-841">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-841">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6dc95-842">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-842">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-843">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-843">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="6dc95-844">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-844">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="6dc95-845">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-845">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="6dc95-846">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-846">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="6dc95-847">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-847">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="6dc95-848">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-848">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="6dc95-849">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-849">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="6dc95-850">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-850">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="6dc95-851">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-851">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="6dc95-852">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-852">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="6dc95-853">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-853">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="6dc95-854">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-854">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="6dc95-855">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-855">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="6dc95-856">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-856">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="6dc95-857">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-857">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="6dc95-858">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-858">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="6dc95-859">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-859">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="6dc95-860">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-860">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="6dc95-861">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-861">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="6dc95-862">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-862">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="6dc95-863">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-863">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="6dc95-864">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-864">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="6dc95-865">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-865">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="6dc95-866">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-866">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="6dc95-867">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-867">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="6dc95-868">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-868">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="6dc95-869">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-869">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="6dc95-870">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-870">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="6dc95-871">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-871">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="6dc95-872">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-872">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="6dc95-873">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-873">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="6dc95-874">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-874">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="6dc95-875">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-875">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="6dc95-876">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-876">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="6dc95-877">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-877">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="6dc95-878">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-878">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="6dc95-879">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-879">Virtual Directories</span></span>

<span data-ttu-id="6dc95-880">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-880">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="6dc95-881">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-881">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="6dc95-882">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6dc95-882">Sub-applications</span></span>

<span data-ttu-id="6dc95-883">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-883">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="6dc95-884">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-884">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="6dc95-885">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-885">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="6dc95-886">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-886">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="6dc95-887">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-887">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="6dc95-888">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-888">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="6dc95-889">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-889">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="6dc95-890">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-890">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="6dc95-891">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-891">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="6dc95-892">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="6dc95-892">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="6dc95-893">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-893">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="6dc95-894">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-894">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="6dc95-895">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-895">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="6dc95-896">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-896">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="6dc95-897">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-897">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="6dc95-898">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-898">Select **OK**.</span></span>

<span data-ttu-id="6dc95-899">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-899">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="6dc95-900">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-900">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="6dc95-901">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-901">Configuration of IIS with web.config</span></span>

<span data-ttu-id="6dc95-902">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-902">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="6dc95-903">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-903">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="6dc95-904">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-904">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="6dc95-905">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-905">For more information, see the following topics:</span></span>

* [<span data-ttu-id="6dc95-906">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="6dc95-906">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="6dc95-907">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-907">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="6dc95-908">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="6dc95-908">Configuration sections of web.config</span></span>

<span data-ttu-id="6dc95-909">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-909">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="6dc95-910">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-910">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="6dc95-911">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-911">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="6dc95-912">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="6dc95-912">Application Pools</span></span>

<span data-ttu-id="6dc95-913">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-913">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="6dc95-914">インプロセス ホスティング &ndash; 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-914">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="6dc95-915">アウトプロセス ホスティング &ndash; アプリケーションをそれぞれ専用のアプリケーションプールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-915">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="6dc95-916">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-916">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="6dc95-917">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-917">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6dc95-918">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-918">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="6dc95-919">アプリケーション プール ID</span><span class="sxs-lookup"><span data-stu-id="6dc95-919">Application Pool Identity</span></span>

<span data-ttu-id="6dc95-920">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-920">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="6dc95-921">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-921">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="6dc95-922">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ID** が **ApplicationPoolIdentity** を使用するように設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-922">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="6dc95-924">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-924">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="6dc95-925">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-925">Resources can be secured using this identity.</span></span> <span data-ttu-id="6dc95-926">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-926">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="6dc95-927">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-927">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="6dc95-928">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-928">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="6dc95-929">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-929">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="6dc95-930">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-930">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="6dc95-931">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-931">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="6dc95-932">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-932">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="6dc95-933">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-933">Select the **Check Names** button.</span></span> <span data-ttu-id="6dc95-934">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-934">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="6dc95-935">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-935">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="6dc95-936">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-936">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="6dc95-937">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-937">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="6dc95-939">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-939">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="6dc95-941">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-941">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="6dc95-942">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-942">Provide additional permissions as needed.</span></span>

<span data-ttu-id="6dc95-943">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-943">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="6dc95-944">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-944">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="6dc95-945">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-945">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="6dc95-946">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="6dc95-946">HTTP/2 support</span></span>

<span data-ttu-id="6dc95-947">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-947">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="6dc95-948">インプロセス</span><span class="sxs-lookup"><span data-stu-id="6dc95-948">In-process</span></span>
  * <span data-ttu-id="6dc95-949">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-949">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6dc95-950">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="6dc95-950">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="6dc95-951">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="6dc95-951">Out-of-process</span></span>
  * <span data-ttu-id="6dc95-952">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-952">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="6dc95-953">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-953">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="6dc95-954">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="6dc95-954">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6dc95-955">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-955">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="6dc95-956">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-956">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="6dc95-957">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-957">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6dc95-958">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-958">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="6dc95-959">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-959">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="6dc95-960">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-960">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="6dc95-961">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="6dc95-961">CORS preflight requests</span></span>

<span data-ttu-id="6dc95-962">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-962">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="6dc95-963">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-963">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="6dc95-964">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-964">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="6dc95-965">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="6dc95-965">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="6dc95-966">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-966">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="6dc95-967">[Application Initialization モジュール](#application-initialization-module) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-967">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="6dc95-968">[アイドル タイムアウト](#idle-timeout) &ndash; アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-968">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="6dc95-969">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="6dc95-969">Application Initialization Module</span></span>

<span data-ttu-id="6dc95-970">"*アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-970">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="6dc95-971">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-971">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="6dc95-972">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-972">The request triggers the app to start.</span></span> <span data-ttu-id="6dc95-973">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-973">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="6dc95-974">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-974">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="6dc95-975">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-975">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="6dc95-976">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-976">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="6dc95-977">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-977">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="6dc95-978">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-978">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6dc95-979">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-979">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="6dc95-980">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-980">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="6dc95-981">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-981">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="6dc95-982">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-982">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="6dc95-983">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-983">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="6dc95-984">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-984">Using IIS Manager:</span></span>

  1. <span data-ttu-id="6dc95-985">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-985">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6dc95-986">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-986">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6dc95-987">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-987">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="6dc95-988">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-988">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="6dc95-989">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-989">Select **OK**.</span></span>
  1. <span data-ttu-id="6dc95-990">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-990">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="6dc95-991">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-991">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="6dc95-992">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-992">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="6dc95-993">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-993">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="6dc95-994">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-994">Select **OK**.</span></span>

* <span data-ttu-id="6dc95-995">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-995">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="6dc95-996">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="6dc95-996">Idle Timeout</span></span>

<span data-ttu-id="6dc95-997">"*アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-997">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="6dc95-998">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-998">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="6dc95-999">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-999">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="6dc95-1000">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1000">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="6dc95-1001">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1001">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="6dc95-1002">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1002">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="6dc95-1003">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1003">Select **OK**.</span></span>
1. <span data-ttu-id="6dc95-1004">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1004">Recycle the worker process.</span></span>

<span data-ttu-id="6dc95-1005">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1005">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="6dc95-1006">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1006">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="6dc95-1007">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1007">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="6dc95-1008">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6dc95-1008">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="6dc95-1009">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="6dc95-1009">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="6dc95-1010">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1010">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="6dc95-1011">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1011">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6dc95-1012">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="6dc95-1012">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="6dc95-1013">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="6dc95-1013">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="6dc95-1014">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="6dc95-1014">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="6dc95-1015">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-1015">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="6dc95-1016">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6dc95-1016">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="6dc95-1017">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="6dc95-1017">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6dc95-1018">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-1018">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="6dc95-1019">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6dc95-1019">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6dc95-1020">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1020">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="6dc95-1021">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-1021">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="6dc95-1022">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="6dc95-1022">Supported operating systems</span></span>

<span data-ttu-id="6dc95-1023">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1023">The following operating systems are supported:</span></span>

* <span data-ttu-id="6dc95-1024">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-1024">Windows 7 or later</span></span>
* <span data-ttu-id="6dc95-1025">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-1025">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="6dc95-1026">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1026">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="6dc95-1027">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1027">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="6dc95-1028">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1028">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="6dc95-1029">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1029">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="6dc95-1030">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="6dc95-1030">Supported platforms</span></span>

<span data-ttu-id="6dc95-1031">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1031">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="6dc95-1032">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="6dc95-1032">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="6dc95-1033">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1033">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="6dc95-1034">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1034">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="6dc95-1035">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1035">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="6dc95-1036">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1036">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="6dc95-1037">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1037">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="6dc95-1038">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1038">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="6dc95-1039">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます ("*プロセス外*")。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1039">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="6dc95-1040">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1040">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="6dc95-1041">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1041">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="6dc95-1042">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1042">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="6dc95-1043">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1043">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="6dc95-1045">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1045">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="6dc95-1046">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1046">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="6dc95-1047">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1047">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="6dc95-1048">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1048">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="6dc95-1049">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1049">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="6dc95-1050">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1050">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="6dc95-1051">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1051">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="6dc95-1052">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1052">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="6dc95-1053">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1053">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="6dc95-1054">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1054">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="6dc95-1055">`CreateDefaultBuilder` は [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成し、ベース パスとポートを [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に構成することで、IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1055">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="6dc95-1056">ASP.NET Core モジュールでは、バックエンド プロセスに割り当てる動的なポートが生成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1056">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="6dc95-1057">`CreateDefaultBuilder` では <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1057">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="6dc95-1058">`UseIISIntegration` では、localhost の IP アドレス (`127.0.0.1`) で動的なポートをリッスンするように Kestrel が構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1058">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="6dc95-1059">動的なポートが 1234 である場合、Kestrel は `127.0.0.1:1234` でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1059">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="6dc95-1060">この構成によって、以下から提供されるその他の URL 構成が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1060">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="6dc95-1061">Kestrel の Listen API</span><span class="sxs-lookup"><span data-stu-id="6dc95-1061">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="6dc95-1062">[構成](xref:fundamentals/configuration/index) (または[コマンド ラインの --urls オプション](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="6dc95-1062">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="6dc95-1063">モジュールを使用する場合、`UseUrls` または Kestrel の `Listen` API を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1063">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="6dc95-1064">`UseUrls` または `Listen` が呼び出されると、IIS なしでアプリを実行しているときのみ、Kestrel で指定したポートがリッスンされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1064">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="6dc95-1065">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1065">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="6dc95-1066">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1066">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="6dc95-1067">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-1067">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="6dc95-1068">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="6dc95-1068">Enable the IISIntegration components</span></span>

<span data-ttu-id="6dc95-1069">`CreateWebHostBuilder` (*Program.cs*) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1069">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="6dc95-1070">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1070">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="6dc95-1071">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-1071">IIS options</span></span>

| <span data-ttu-id="6dc95-1072">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-1072">Option</span></span>                         | <span data-ttu-id="6dc95-1073">既定値</span><span class="sxs-lookup"><span data-stu-id="6dc95-1073">Default</span></span> | <span data-ttu-id="6dc95-1074">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-1074">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-1075">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1075">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-1076">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1076">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-1077">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1077">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-1078">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1078">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-1079">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1079">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="6dc95-1080">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1080">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="6dc95-1081">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1081">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="6dc95-1082">オプション</span><span class="sxs-lookup"><span data-stu-id="6dc95-1082">Option</span></span>                         | <span data-ttu-id="6dc95-1083">Default</span><span class="sxs-lookup"><span data-stu-id="6dc95-1083">Default</span></span> | <span data-ttu-id="6dc95-1084">設定</span><span class="sxs-lookup"><span data-stu-id="6dc95-1084">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="6dc95-1085">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1085">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="6dc95-1086">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1086">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="6dc95-1087">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1087">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="6dc95-1088">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1088">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="6dc95-1089">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1089">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="6dc95-1090">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1090">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="6dc95-1091">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="6dc95-1091">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="6dc95-1092">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1092">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="6dc95-1093">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1093">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="6dc95-1094">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1094">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="6dc95-1095">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-1095">web.config file</span></span>

<span data-ttu-id="6dc95-1096">*web.config* ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1096">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-1097">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1097">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="6dc95-1098">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1098">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="6dc95-1099">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1099">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="6dc95-1100">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1100">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="6dc95-1101">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1101">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="6dc95-1102">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1102">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="6dc95-1103">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1103">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="6dc95-1104">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1104">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="6dc95-1105">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1105">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="6dc95-1106">Web SDK ファイルの変換を無効にすると、 *processPath*と*引数*開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1106">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="6dc95-1107">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1107">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="6dc95-1108">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="6dc95-1108">web.config file location</span></span>

<span data-ttu-id="6dc95-1109">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、*web.config* ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1109">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="6dc95-1110">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1110">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="6dc95-1111">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1111">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="6dc95-1112">アプリの物理パスには、 *\<assembly>.runtimeconfig.json*、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1112">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="6dc95-1113">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1113">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="6dc95-1114">*web.config*ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1114">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="6dc95-1115">***web.config*ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。*web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1115">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="6dc95-1116">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="6dc95-1116">Transform web.config</span></span>

<span data-ttu-id="6dc95-1117">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1117">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="6dc95-1118">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-1118">IIS configuration</span></span>

<span data-ttu-id="6dc95-1119">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1119">**Windows Server operating systems**</span></span>

<span data-ttu-id="6dc95-1120">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1120">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="6dc95-1121">**[管理]** メニューから**役割と機能の追加**ウィザードを使用するか、**サーバー マネージャー**にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1121">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="6dc95-1122">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1122">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="6dc95-1124">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1124">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="6dc95-1125">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1125">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="6dc95-1127">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1127">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-1128">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1128">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="6dc95-1129">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1129">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-1130">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1130">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-1131">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1131">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-1132">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1132">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-1133">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1133">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="6dc95-1134">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1134">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-1135">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1135">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-1136">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1136">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="6dc95-1137">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1137">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="6dc95-1138">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1138">**Windows desktop operating systems**</span></span>

<span data-ttu-id="6dc95-1139">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1139">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-1140">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1140">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="6dc95-1141">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1141">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="6dc95-1142">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1142">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="6dc95-1143">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1143">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="6dc95-1144">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1144">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="6dc95-1145">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1145">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="6dc95-1146">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1146">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="6dc95-1147">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1147">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="6dc95-1148">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1148">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="6dc95-1149">詳細については、「[Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および「[Configure Windows authentication](xref:security/authentication/windowsauth)」(Windows 認証の構成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1149">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="6dc95-1150">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1150">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="6dc95-1151">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1151">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="6dc95-1152">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1152">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="6dc95-1153">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1153">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="6dc95-1154">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1154">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="6dc95-1155">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1155">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6dc95-1157">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-1157">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6dc95-1158">ホスティング システムに *.NET Core ホスティング バンドル*をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1158">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="6dc95-1159">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1159">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6dc95-1160">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1160">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6dc95-1161">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1161">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="6dc95-1162">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1162">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="6dc95-1163">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1163">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6dc95-1164">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1164">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="6dc95-1165">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="6dc95-1165">Download</span></span>

1. <span data-ttu-id="6dc95-1166">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1166">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="6dc95-1167">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1167">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="6dc95-1168">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1168">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="6dc95-1169">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1169">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="6dc95-1170">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1170">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="6dc95-1171">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1171">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="6dc95-1172">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="6dc95-1172">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="6dc95-1173">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1173">Run the installer on the server.</span></span> <span data-ttu-id="6dc95-1174">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1174">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="6dc95-1175">`OPT_NO_ANCM=1` &ndash; ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1175">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="6dc95-1176">`OPT_NO_RUNTIME=1` &ndash; .NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1176">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="6dc95-1177">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1177">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-1178">`OPT_NO_SHAREDFX=1` &ndash; ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1178">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="6dc95-1179">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1179">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6dc95-1180">`OPT_NO_X86=1` &ndash; x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1180">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="6dc95-1181">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1181">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="6dc95-1182">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1182">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="6dc95-1183">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; 共有構成 (*applicationHost.config*) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使うためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1183">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="6dc95-1184">"*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-1184">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="6dc95-1185">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1185">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="6dc95-1186">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1186">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="6dc95-1187">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1187">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="6dc95-1188">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1188">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="6dc95-1189">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1189">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="6dc95-1190">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1190">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="6dc95-1191">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1191">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="6dc95-1192">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1192">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="6dc95-1193">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1193">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="6dc95-1194">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1194">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="6dc95-1195">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="6dc95-1195">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="6dc95-1196">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1196">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="6dc95-1197">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1197">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="6dc95-1198">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1198">The preferred method is to use WebPI.</span></span> <span data-ttu-id="6dc95-1199">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1199">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6dc95-1200">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="6dc95-1200">Create the IIS site</span></span>

1. <span data-ttu-id="6dc95-1201">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1201">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6dc95-1202">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1202">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="6dc95-1203">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1203">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="6dc95-1204">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1204">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6dc95-1205">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1205">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6dc95-1206">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1206">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-1207">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1207">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="6dc95-1208">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1208">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="6dc95-1210">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1210">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6dc95-1211">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1211">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6dc95-1212">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1212">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6dc95-1213">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1213">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6dc95-1214">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1214">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6dc95-1215">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1215">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6dc95-1216">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1216">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="6dc95-1217">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1217">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="6dc95-1218">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1218">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="6dc95-1220">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1220">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="6dc95-1221">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1221">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="6dc95-1222">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1222">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="6dc95-1223">*ASP.NET Core 2.2 以降*:[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1223">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="6dc95-1224">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1224">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="6dc95-1225">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1225">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="6dc95-1226">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1226">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="6dc95-1227">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1227">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6dc95-1228">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1228">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6dc95-1229">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1229">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="6dc95-1230">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1230">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="6dc95-1231">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1231">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="6dc95-1232">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="6dc95-1232">Deploy the app</span></span>

<span data-ttu-id="6dc95-1233">「[IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1233">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="6dc95-1234">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1234">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="6dc95-1235">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-1235">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="6dc95-1236">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1236">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="6dc95-1237">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1237">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="6dc95-1239">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="6dc95-1239">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="6dc95-1240">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1240">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="6dc95-1241">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1241">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="6dc95-1242">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="6dc95-1242">Alternatives to Web Deploy</span></span>

<span data-ttu-id="6dc95-1243">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1243">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="6dc95-1244">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1244">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="6dc95-1245">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="6dc95-1245">Browse the website</span></span>

<span data-ttu-id="6dc95-1246">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1246">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="6dc95-1247">次の例では、サイトは IIS の**ホスト名**`www.mysite.com` に**ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1247">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="6dc95-1248">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1248">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="6dc95-1250">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="6dc95-1250">Locked deployment files</span></span>

<span data-ttu-id="6dc95-1251">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1251">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="6dc95-1252">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1252">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="6dc95-1253">展開でロックされているファイルをリリースするには、次の**いずれか**の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1253">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-1254">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1254">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="6dc95-1255">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1255">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="6dc95-1256">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1256">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="6dc95-1257">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1257">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="6dc95-1258">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1258">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="6dc95-1259">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1259">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="6dc95-1260">データの保護</span><span class="sxs-lookup"><span data-stu-id="6dc95-1260">Data protection</span></span>

<span data-ttu-id="6dc95-1261">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1261">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="6dc95-1262">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1262">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="6dc95-1263">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1263">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="6dc95-1264">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1264">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="6dc95-1265">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1265">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="6dc95-1266">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1266">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="6dc95-1267">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1267">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="6dc95-1268">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1268">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="6dc95-1269">キーリングを保持するために IIS でのデータ保護を構成するには、次の**いずれか**の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1269">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="6dc95-1270">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1270">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="6dc95-1271">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1271">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="6dc95-1272">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1272">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="6dc95-1273">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1273">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="6dc95-1274">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1274">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="6dc95-1275">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1275">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="6dc95-1276">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1276">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="6dc95-1277">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1277">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="6dc95-1278">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1278">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="6dc95-1279">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1279">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="6dc95-1280">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1280">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="6dc95-1281">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1281">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="6dc95-1282">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1282">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="6dc95-1283">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1283">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="6dc95-1284">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1284">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="6dc95-1285">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1285">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="6dc95-1286">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1286">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="6dc95-1287">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1287">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="6dc95-1288">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1288">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="6dc95-1289">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1289">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="6dc95-1290">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-1290">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="6dc95-1291">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1291">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="6dc95-1292">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1292">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="6dc95-1293">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1293">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="6dc95-1294">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1294">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="6dc95-1295">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1295">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="6dc95-1296">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1296">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="6dc95-1297">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1297">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="6dc95-1298">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1298">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="6dc95-1299">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="6dc95-1299">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="6dc95-1300">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1300">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="6dc95-1301">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1301">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="6dc95-1302">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1302">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="6dc95-1303">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="6dc95-1303">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="6dc95-1304">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1304">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="6dc95-1305">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1305">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="6dc95-1306">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-1306">Virtual Directories</span></span>

<span data-ttu-id="6dc95-1307">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1307">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="6dc95-1308">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1308">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="6dc95-1309">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6dc95-1309">Sub-applications</span></span>

<span data-ttu-id="6dc95-1310">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1310">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="6dc95-1311">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1311">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="6dc95-1312">サブアプリに、ハンドラーとして ASP.NET Core モジュールを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1312">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="6dc95-1313">このモジュールをサブアプリの *web.config* ファイルにハンドラーとして追加すると、サブアプリを閲覧しようとすると、エラーのある構成ファイルを参照する *500.19 内部サーバー エラー* が返されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1313">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="6dc95-1314">次の例は、ASP.NET Core サブアプリの発行済み *web.config* ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1314">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="6dc95-1315">ASP.NET Core アプリの下に ASP.NET Core 以外のサブアプリをホスティングする場合は、サブアプリの *web.config* ファイルにある継承されたハンドラーを明示的に削除します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1315">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="6dc95-1316">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1316">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="6dc95-1317">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1317">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="6dc95-1318">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1318">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="6dc95-1319">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1319">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="6dc95-1320">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1320">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="6dc95-1321">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1321">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="6dc95-1322">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1322">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="6dc95-1323">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="6dc95-1323">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="6dc95-1324">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1324">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="6dc95-1325">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1325">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="6dc95-1326">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1326">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="6dc95-1327">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1327">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="6dc95-1328">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1328">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="6dc95-1329">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1329">Select **OK**.</span></span>

<span data-ttu-id="6dc95-1330">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1330">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="6dc95-1331">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1331">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="6dc95-1332">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="6dc95-1332">Configuration of IIS with web.config</span></span>

<span data-ttu-id="6dc95-1333">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1333">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="6dc95-1334">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1334">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="6dc95-1335">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1335">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="6dc95-1336">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1336">For more information, see the following topics:</span></span>

* [<span data-ttu-id="6dc95-1337">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="6dc95-1337">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="6dc95-1338">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、[環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある *AppCmd.exe コマンド*のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1338">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="6dc95-1339">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="6dc95-1339">Configuration sections of web.config</span></span>

<span data-ttu-id="6dc95-1340">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1340">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="6dc95-1341">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1341">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="6dc95-1342">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1342">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="6dc95-1343">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="6dc95-1343">Application Pools</span></span>

<span data-ttu-id="6dc95-1344">サーバーで複数の Web サイトをホストする場合は、アプリをそれぞれ専用のアプリ プールで実行して、アプリを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1344">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="6dc95-1345">IIS の **[Web サイトの追加]** ダイアログはこの構成の既定です。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1345">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="6dc95-1346">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1346">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="6dc95-1347">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1347">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="6dc95-1348">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="6dc95-1348">Application Pool Identity</span></span>

<span data-ttu-id="6dc95-1349">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1349">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="6dc95-1350">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1350">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="6dc95-1351">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、**ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1351">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="6dc95-1353">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1353">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="6dc95-1354">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1354">Resources can be secured using this identity.</span></span> <span data-ttu-id="6dc95-1355">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1355">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="6dc95-1356">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1356">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="6dc95-1357">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1357">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="6dc95-1358">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1358">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="6dc95-1359">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1359">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="6dc95-1360">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1360">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="6dc95-1361">**[選択するオブジェクト名を入力してください]** 領域に、「**IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1361">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="6dc95-1362">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1362">Select the **Check Names** button.</span></span> <span data-ttu-id="6dc95-1363">*DefaultAppPool* で、**IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1363">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="6dc95-1364">**[名前の確認]** ボタンが選択されているときには、**DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1364">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="6dc95-1365">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1365">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="6dc95-1366">オブジェクト名を確認するときには、**IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1366">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="6dc95-1368">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1368">Select **OK**.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="6dc95-1370">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1370">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="6dc95-1371">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1371">Provide additional permissions as needed.</span></span>

<span data-ttu-id="6dc95-1372">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1372">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="6dc95-1373">たとえば、*DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1373">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="6dc95-1374">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1374">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="6dc95-1375">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="6dc95-1375">HTTP/2 support</span></span>

<span data-ttu-id="6dc95-1376">次の基本要件を満たすアウトプロセスの展開には、[HTTP/2](https://httpwg.org/specs/rfc7540.html) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1376">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="6dc95-1377">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="6dc95-1377">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="6dc95-1378">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1378">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="6dc95-1379">ターゲット フレームワーク:HTTP/2 接続は IIS によって完全に処理されるため、アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1379">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="6dc95-1380">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="6dc95-1380">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6dc95-1381">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1381">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="6dc95-1382">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1382">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="6dc95-1383">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1383">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="6dc95-1384">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1384">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="6dc95-1385">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="6dc95-1385">CORS preflight requests</span></span>

<span data-ttu-id="6dc95-1386">"*このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="6dc95-1386">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="6dc95-1387">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1387">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="6dc95-1388">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6dc95-1388">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6dc95-1389">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="6dc95-1389">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="6dc95-1390">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="6dc95-1390">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="6dc95-1391">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="6dc95-1391">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="6dc95-1392">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="6dc95-1392">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="6dc95-1393">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6dc95-1393">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="6dc95-1394">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="6dc95-1394">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6dc95-1395">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="6dc95-1395">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="6dc95-1396">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6dc95-1396">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
