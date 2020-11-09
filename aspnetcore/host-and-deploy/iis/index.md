---
title: IIS を使用した Windows での ASP.NET Core のホスト
author: rick-anderson
description: Windows Server インターネット インフォメーション サービス (IIS) での ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/index
ms.openlocfilehash: e4a94ca9e3607868f3eb25d88338e8156f7f5206
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061523"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="7b29a-103">IIS を使用した Windows での ASP.NET Core のホスト</span><span class="sxs-lookup"><span data-stu-id="7b29a-103">Host ASP.NET Core on Windows with IIS</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7b29a-104">インターネット インフォメーション サービス (IIS) は、Web アプリ (ASP.NET Core を含む) をホストするための、柔軟で安全で管理しやすい Web サーバーです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-104">Internet Information Services (IIS) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7b29a-105">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="7b29a-105">Supported platforms</span></span>

<span data-ttu-id="7b29a-106">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-106">The following operating systems are supported:</span></span>

* <span data-ttu-id="7b29a-107">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-107">Windows 7 or later</span></span>
* <span data-ttu-id="7b29a-108">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-108">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="7b29a-109">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-109">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7b29a-110">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="7b29a-110">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7b29a-111">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-111">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7b29a-112">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-112">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7b29a-113">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="7b29a-113">Has 64-bit native dependencies.</span></span>

## <a name="install-the-aspnet-core-modulehosting-bundle"></a><span data-ttu-id="7b29a-114">ASP.NET Core モジュール/ホスティング モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="7b29a-114">Install the ASP.NET Core Module/Hosting Bundle</span></span>

<span data-ttu-id="7b29a-115">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-115">Download the installer using the following link:</span></span>

[<span data-ttu-id="7b29a-116">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="7b29a-116">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="7b29a-117">ASP.NET Core モジュールの詳しいインストール手順については、「[.NET Core ホスティング バンドルのインストール](xref:host-and-deploy/iis/hosting-bundle)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-117">For more details instructions on how to install the ASP.NET Core Module, or installing different versions, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

## <a name="get-started"></a><span data-ttu-id="7b29a-118">はじめに</span><span class="sxs-lookup"><span data-stu-id="7b29a-118">Get started</span></span>

<span data-ttu-id="7b29a-119">IIS での Web サイトのホスティングの概要については、[ファースト ステップ ガイド](xref:tutorials/publish-to-iis)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-119">For getting started with hosting a website on IIS, see our [getting started guide](xref:tutorials/publish-to-iis).</span></span>

<span data-ttu-id="7b29a-120">Azure App Services での Web サイトのホスティングの概要については、[Azure App Service へのデプロイのガイド](xref:host-and-deploy/azure-apps/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-120">For getting started with hosting a website on Azure App Services, see our [deploying to Azure App Service guide](xref:host-and-deploy/azure-apps/index).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7b29a-121">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="7b29a-121">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7b29a-122">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="7b29a-122">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7b29a-123">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="7b29a-123">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7b29a-124">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-124">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7b29a-125">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-125">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7b29a-126">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="7b29a-126">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7b29a-127">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-127">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7b29a-128">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7b29a-128">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7b29a-129">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-129">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7b29a-130">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-130">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7b29a-131">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="7b29a-131">Supported operating systems</span></span>

<span data-ttu-id="7b29a-132">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-132">The following operating systems are supported:</span></span>

* <span data-ttu-id="7b29a-133">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-133">Windows 7 or later</span></span>
* <span data-ttu-id="7b29a-134">Windows Server 2012 R2 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-134">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="7b29a-135">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-135">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7b29a-136">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-136">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7b29a-137">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-137">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7b29a-138">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-138">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7b29a-139">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="7b29a-139">Supported platforms</span></span>

<span data-ttu-id="7b29a-140">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-140">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7b29a-141">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="7b29a-141">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7b29a-142">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-142">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7b29a-143">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-143">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7b29a-144">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="7b29a-144">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7b29a-145">32 ビット (x86) 用に公開されたアプリでは、IIS アプリケーション プールで 32 ビットが有効になっている必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-145">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="7b29a-146">詳細については、「[IIS サイトを作成する](#create-the-iis-site)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-146">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="7b29a-147">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-147">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7b29a-148">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-148">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7b29a-149">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-149">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="7b29a-150">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-150">In-process hosting model</span></span>

<span data-ttu-id="7b29a-151">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-151">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7b29a-152">インプロセス ホスティングは、パフォーマンスの点でアウトプロセス ホスティングよりも優れています。要求がループバック アダプター (発信されたネットワーク トラフィックを同じコンピューターに送り返すネットワーク インターフェイス) を介してプロキシされることがないからです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-152">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="7b29a-153">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-153">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b29a-154">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="7b29a-154">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="7b29a-155">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-155">Performs app initialization.</span></span>
  * <span data-ttu-id="7b29a-156">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-156">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="7b29a-157">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="7b29a-157">Calls `Program.Main`.</span></span>
* <span data-ttu-id="7b29a-158">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-158">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="7b29a-159">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-159">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="7b29a-161">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-161">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="7b29a-162">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-162">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="7b29a-163">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-163">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="7b29a-164">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-164">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="7b29a-165">IIS HTTP サーバーによって要求が処理された後は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-165">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="7b29a-166">要求は ASP.NET Core ミドルウェア パイプラインに送信されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-166">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="7b29a-167">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-167">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="7b29a-168">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-168">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="7b29a-169">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-169">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="7b29a-170">既存のアプリではインプロセス ホスティングがオプトインされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-170">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="7b29a-171">ASP.NET Core Web テンプレートでは、インプロセス ホスティング モデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-171">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="7b29a-172">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> メソッドを呼び出し、[CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス (`w3wp.exe` または `iisexpress.exe`) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-172">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="7b29a-173">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) へのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-173">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7b29a-174">単一ファイルの実行可能ファイルとして公開されたアプリは、インプロセス ホスティング モデルで読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-174">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7b29a-175">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-175">Out-of-process hosting model</span></span>

<span data-ttu-id="7b29a-176">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-176">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="7b29a-177">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-177">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7b29a-178">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-178">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b29a-179">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-179">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="7b29a-181">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-181">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="7b29a-182">ドライバーは、Web サイトの構成ポートで IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-182">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="7b29a-183">構成されるポートは、通常 80 (HTTP) または 443 (HTTPS) です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-183">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="7b29a-184">モジュールでは、アプリのランダムなポートで Kestrel に要求を転送します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-184">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="7b29a-185">ランダムなポートは 80 または 443 ではありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-185">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="7b29a-186">ASP.NET Core モジュールは、起動時に環境変数によってポートを指定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-186">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="7b29a-187"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> 拡張機能は、`http://localhost:{PORT}` でリッスンするようにサーバーを構成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-187">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7b29a-188">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-188">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7b29a-189">モジュールは HTTPS 転送をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-189">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="7b29a-190">要求は HTTPS を介して IIS によって受信された場合でも HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-190">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7b29a-191">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインに転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-191">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b29a-192">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-192">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b29a-193">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-193">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7b29a-194">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-194">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7b29a-195">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-195">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7b29a-196">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-196">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7b29a-197">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-197">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7b29a-198">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="7b29a-198">Enable the IISIntegration components</span></span>

<span data-ttu-id="7b29a-199">`CreateHostBuilder` (`Program.cs`) でホストを構築する場合は、<xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-199">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7b29a-200">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/generic-host#default-builder-settings>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-200">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7b29a-201">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-201">IIS options</span></span>

<span data-ttu-id="7b29a-202">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="7b29a-202">**In-process hosting model**</span></span>

<span data-ttu-id="7b29a-203">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-203">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7b29a-204">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-204">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="7b29a-205">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-205">Option</span></span>                         | <span data-ttu-id="7b29a-206">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-206">Default</span></span> | <span data-ttu-id="7b29a-207">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-207">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-208">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-208">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-209">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-209">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-210">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-210">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-211">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-211">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-212">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-212">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="7b29a-213">`HttpContext.Request` および `HttpContext.Response` に対して同期 I/O が許可されるか。</span><span class="sxs-lookup"><span data-stu-id="7b29a-213">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="7b29a-214">`HttpRequest` の最大要求本文サイズを取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-214">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="7b29a-215">IIS 自体に、`IISServerOptions` に設定された `MaxRequestBodySize` の前に処理される上限 `maxAllowedContentLength` があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-215">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="7b29a-216">`MaxRequestBodySize` を変更しても、`maxAllowedContentLength` に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-216">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="7b29a-217">`maxAllowedContentLength`を引き上げるには、`web.config` 内にエントリを追加して `maxAllowedContentLength` をより高い値に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-217">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="7b29a-218">詳細については、「[Configuration (構成)](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-218">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="7b29a-219">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="7b29a-219">**Out-of-process hosting model**</span></span>

<span data-ttu-id="7b29a-220">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-220">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7b29a-221">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-221">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7b29a-222">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-222">Option</span></span>                         | <span data-ttu-id="7b29a-223">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-223">Default</span></span> | <span data-ttu-id="7b29a-224">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-224">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-225">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-225">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-226">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-226">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-227">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-227">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-228">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-228">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-229">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-229">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7b29a-230">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-230">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7b29a-231">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="7b29a-231">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7b29a-232">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)と ASP.NET Core モジュールは、次を転送するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-232">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="7b29a-233">スキーム (HTTP/HTTPS)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-233">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="7b29a-234">要求元のリモート IP アドレス。</span><span class="sxs-lookup"><span data-stu-id="7b29a-234">Remote IP address where the request originated.</span></span>

<span data-ttu-id="7b29a-235">[IIS 統合ミドルウェア](#enable-the-iisintegration-components)は、Forwarded Headers Middleware を構成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-235">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="7b29a-236">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-236">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7b29a-237">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-237">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7b29a-238">`web.config` ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-238">`web.config` file</span></span>

<span data-ttu-id="7b29a-239">`web.config` ファイルでは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-239">The `web.config` file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-240">`web.config` ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-240">Creating, transforming, and publishing the `web.config` file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7b29a-241">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-241">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7b29a-242">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-242">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7b29a-243">プロジェクト内に `web.config` ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な `processPath` と `arguments` を使用して作成され、[発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-243">If a `web.config` file isn't present in the project, the file is created with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7b29a-244">プロジェクトに `web.config` ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい `processPath` と `arguments` を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-244">If a `web.config` file is present in the project, the file is transformed with the correct `processPath` and `arguments` to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7b29a-245">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-245">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7b29a-246">`web.config` ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-246">The `web.config` file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7b29a-247">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-247">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7b29a-248">Web SDK によって `web.config` ファイルが変換されないようにするため、`<IsTransformWebConfigDisabled>` プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-248">To prevent the Web SDK from transforming the `web.config` file, use the `<IsTransformWebConfigDisabled>` property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7b29a-249">Web SDK ファイルの変換を無効にすると、 `processPath`と`arguments`開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-249">When disabling the Web SDK from transforming the file, the `processPath` and `arguments` should be manually set by the developer.</span></span> <span data-ttu-id="7b29a-250">詳細については、<xref:host-and-deploy/aspnet-core-module> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-250">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7b29a-251">`web.config` ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="7b29a-251">`web.config` file location</span></span>

<span data-ttu-id="7b29a-252">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、`web.config` ファイルが、展開されるアプリの[コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-252">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the `web.config` file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7b29a-253">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-253">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7b29a-254">`web.config` ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-254">The `web.config` file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7b29a-255">アプリの物理パスには、`{ASSEMBLY}.runtimeconfig.json`、`{ASSEMBLY}.xml` (XML ドキュメントのコメント)、`{ASSEMBLY}.deps.json` (プレースホルダー `{ASSEMBLY}` はアセンブリ名) などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-255">Sensitive files exist on the app's physical path, such as `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (XML Documentation comments), and `{ASSEMBLY}.deps.json`, where the placeholder `{ASSEMBLY}` is the assembly name.</span></span> <span data-ttu-id="7b29a-256">`web.config` ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-256">When the `web.config` file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7b29a-257">`web.config`ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-257">If the `web.config` file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7b29a-258">**`web.config`ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。`web.config` ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="7b29a-258">**The `web.config` file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the `web.config` file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7b29a-259">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="7b29a-259">Transform web.config</span></span>

<span data-ttu-id="7b29a-260">発行時に "`web.config`" を変換する場合は、「<xref:host-and-deploy/iis/transform-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-260">If you need to transform `web.config` on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="7b29a-261">発行時に "`web.config`" を変換して、構成、プロファイル、環境に基づいた環境変数を設定しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-261">You might need to transform `web.config` on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7b29a-262">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-262">IIS configuration</span></span>

<span data-ttu-id="7b29a-263">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-263">**Windows Server operating systems**</span></span>

<span data-ttu-id="7b29a-264">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-264">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7b29a-265">**[管理]** メニューから **役割と機能の追加** ウィザードを使用するか、 **サーバー マネージャー** にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-265">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7b29a-266">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-266">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7b29a-268">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-268">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7b29a-269">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-269">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7b29a-271">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-271">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-272">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-272">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7b29a-273">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-273">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-274">詳細については、「[Windows 認証 `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-274">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-275">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-275">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-276">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-276">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-277">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-277">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7b29a-278">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-278">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-279">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-279">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-280">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-280">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7b29a-281">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-281">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7b29a-282">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-282">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7b29a-283">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-283">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-284">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-284">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7b29a-285">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-285">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7b29a-286">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-286">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7b29a-287">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-287">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7b29a-288">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-288">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-289">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-289">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7b29a-290">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-290">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-291">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-291">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7b29a-292">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-292">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-293">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-293">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-294">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-294">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-295">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-295">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-296">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-296">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7b29a-297">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-297">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-298">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-298">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-299">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-299">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7b29a-301">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-301">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7b29a-302">ホスティング システムに *.NET Core ホスティング バンドル* をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-302">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7b29a-303">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-303">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-304">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-304">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7b29a-305">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-305">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7b29a-306">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-306">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7b29a-307">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="7b29a-307">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7b29a-308">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-308">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="7b29a-309">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="7b29a-309">Direct download (current version)</span></span>

<span data-ttu-id="7b29a-310">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-310">Download the installer using the following link:</span></span>

[<span data-ttu-id="7b29a-311">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="7b29a-311">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="7b29a-312">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="7b29a-312">Earlier versions of the installer</span></span>

<span data-ttu-id="7b29a-313">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="7b29a-313">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="7b29a-314">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-314">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7b29a-315">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-315">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7b29a-316">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-316">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7b29a-317">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-317">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7b29a-318">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-318">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7b29a-319">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-319">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7b29a-320">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="7b29a-320">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7b29a-321">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-321">Run the installer on the server.</span></span> <span data-ttu-id="7b29a-322">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-322">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7b29a-323">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-323">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7b29a-324">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-324">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7b29a-325">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-325">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-326">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-326">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7b29a-327">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-327">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-328">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-328">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7b29a-329">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-329">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7b29a-330">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-330">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7b29a-331">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (`applicationHost.config`) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-331">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7b29a-332">" *ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-332">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7b29a-333">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-333">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7b29a-334">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-334">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7b29a-335">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-335">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7b29a-336">ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-336">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7b29a-337">新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-337">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="7b29a-338">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-338">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7b29a-339">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-339">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7b29a-340">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-340">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7b29a-341">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-341">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7b29a-342">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-342">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7b29a-343">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-343">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7b29a-344">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="7b29a-344">Create the IIS site</span></span>

1. <span data-ttu-id="7b29a-345">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-345">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7b29a-346">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-346">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7b29a-347">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-347">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7b29a-348">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-348">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7b29a-349">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-349">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7b29a-350">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-350">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-351">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-351">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7b29a-352">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-352">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7b29a-354">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="7b29a-354">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7b29a-355">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-355">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7b29a-356">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-356">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7b29a-357">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-357">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7b29a-358">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-358">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7b29a-359">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-359">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7b29a-360">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-360">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7b29a-361">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-361">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-362">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-362">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7b29a-364">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-364">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7b29a-365">ASP.NET Core を使用するためにデスクトップ CLR (.NET CLR) を読み込む必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-365">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="7b29a-366">.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-366">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7b29a-367">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-367">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7b29a-368">*ASP.NET Core 2.2 以降* :</span><span class="sxs-lookup"><span data-stu-id="7b29a-368">*ASP.NET Core 2.2 or later* :</span></span>

   * <span data-ttu-id="7b29a-369">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 32 ビット SDK で公開される 32 ビット (x86) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット用のアプリケーション プールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-369">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="7b29a-370">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-370">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="7b29a-371">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-371">Select the app's Application Pool.</span></span> <span data-ttu-id="7b29a-372">**[操作]** サイドバーで、[ **詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-372">In the **Actions** sidebar, select **Advanced Settings** .</span></span> <span data-ttu-id="7b29a-373">**[32 ビット アプリケーションの有効化]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-373">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="7b29a-374">[インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の[自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-374">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="7b29a-375">IIS マネージャーで、 **[接続]** サイドバーの **[アプリケーション プール]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-375">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="7b29a-376">アプリのアプリケーション プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-376">Select the app's Application Pool.</span></span> <span data-ttu-id="7b29a-377">**[操作]** サイドバーで、[ **詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-377">In the **Actions** sidebar, select **Advanced Settings** .</span></span> <span data-ttu-id="7b29a-378">**[32 ビット アプリケーションの有効化]** を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-378">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="7b29a-379">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-379">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7b29a-380">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-380">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7b29a-381">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-381">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7b29a-382">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-382">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7b29a-383">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-383">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7b29a-384">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="7b29a-384">Deploy the app</span></span>

<span data-ttu-id="7b29a-385">「 [IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-385">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7b29a-386">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの `publish` フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-386">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's `publish` folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7b29a-387">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-387">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7b29a-388">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-388">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7b29a-389">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-389">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7b29a-391">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="7b29a-391">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7b29a-392">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-392">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7b29a-393">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-393">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7b29a-394">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="7b29a-394">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7b29a-395">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-395">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7b29a-396">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-396">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7b29a-397">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="7b29a-397">Browse the website</span></span>

<span data-ttu-id="7b29a-398">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-398">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7b29a-399">次の例では、サイトは IIS の **ホスト名**`www.mysite.com` に **ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-399">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7b29a-400">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-400">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7b29a-402">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-402">Locked deployment files</span></span>

<span data-ttu-id="7b29a-403">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-403">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7b29a-404">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-404">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7b29a-405">展開でロックされているファイルをリリースするには、次の **いずれか** の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-405">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-406">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-406">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7b29a-407">`app_offline.htm` ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-407">An `app_offline.htm` file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7b29a-408">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に `app_offline.htm` ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-408">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the `app_offline.htm` file during the deployment.</span></span> <span data-ttu-id="7b29a-409">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-409">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7b29a-410">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-410">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7b29a-411">PowerShell を使用して `app_offline.htm` をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-411">Use PowerShell to drop `app_offline.htm` (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a><span data-ttu-id="7b29a-412">データの保護</span><span class="sxs-lookup"><span data-stu-id="7b29a-412">Data protection</span></span>

<span data-ttu-id="7b29a-413">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-413">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7b29a-414">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-414">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7b29a-415">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-415">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7b29a-416">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-416">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7b29a-417">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-417">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7b29a-418">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-418">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7b29a-419">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-419">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7b29a-420">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-420">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7b29a-421">キーリングを保持するために IIS でのデータ保護を構成するには、次の **いずれか** の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-421">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-422">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-422">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7b29a-423">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-423">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7b29a-424">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-424">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7b29a-425">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-425">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7b29a-426">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-426">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7b29a-427">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-427">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7b29a-428">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-428">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7b29a-429">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-429">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7b29a-430">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-430">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7b29a-431">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-431">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7b29a-432">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-432">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7b29a-433">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-433">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7b29a-434">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-434">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7b29a-435">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-435">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7b29a-436">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-436">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7b29a-437">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-437">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7b29a-438">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-438">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7b29a-439">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-439">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7b29a-440">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-440">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7b29a-441">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-441">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7b29a-442">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-442">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7b29a-443">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-443">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7b29a-444">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-444">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7b29a-445">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-445">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7b29a-446">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-446">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7b29a-447">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-447">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7b29a-448">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-448">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7b29a-449">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-449">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7b29a-450">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-450">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7b29a-451">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-451">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7b29a-452">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-452">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7b29a-453">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-453">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7b29a-454">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-454">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7b29a-455">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-455">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7b29a-456">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-456">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7b29a-457">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-457">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7b29a-458">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-458">Virtual Directories</span></span>

<span data-ttu-id="7b29a-459">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-459">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7b29a-460">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-460">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7b29a-461">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="7b29a-461">Sub-applications</span></span>

<span data-ttu-id="7b29a-462">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-462">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7b29a-463">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-463">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7b29a-464">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-464">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7b29a-465">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-465">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7b29a-466">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-466">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7b29a-467">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-467">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7b29a-468">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-468">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7b29a-469">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-469">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7b29a-470">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-470">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7b29a-471">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="7b29a-471">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7b29a-472">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-472">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7b29a-473">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-473">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7b29a-474">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-474">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7b29a-475">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-475">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7b29a-476">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-476">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7b29a-477">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-477">Select **OK** .</span></span>

<span data-ttu-id="7b29a-478">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-478">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7b29a-479">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-479">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7b29a-480">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-480">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7b29a-481">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-481">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7b29a-482">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-482">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7b29a-483">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-483">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7b29a-484">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-484">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7b29a-485">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="7b29a-485">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7b29a-486">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、 [環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある " *AppCmd.exe コマンド* " のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-486">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7b29a-487">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="7b29a-487">Configuration sections of web.config</span></span>

<span data-ttu-id="7b29a-488">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-488">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7b29a-489">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-489">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7b29a-490">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-490">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7b29a-491">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="7b29a-491">Application Pools</span></span>

<span data-ttu-id="7b29a-492">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-492">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="7b29a-493">インプロセス ホスティング: 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-493">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="7b29a-494">アウトプロセス ホスティング: アプリケーションをそれぞれ専用のアプリケーション プールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-494">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="7b29a-495">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-495">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="7b29a-496">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-496">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7b29a-497">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-497">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7b29a-498">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="7b29a-498">Application Pool Identity</span></span>

<span data-ttu-id="7b29a-499">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-499">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7b29a-500">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-500">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7b29a-501">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、 **ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-501">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="7b29a-503">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-503">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7b29a-504">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-504">Resources can be secured using this identity.</span></span> <span data-ttu-id="7b29a-505">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-505">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7b29a-506">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-506">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7b29a-507">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-507">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7b29a-508">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-508">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7b29a-509">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-509">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7b29a-510">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-510">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7b29a-511">**[選択するオブジェクト名を入力してください]** 領域に、「`IIS AppPool\{APP POOL NAME}`」(プレースホルダー `{APP POOL NAME}` はアプリ プール名) を入力します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-511">Enter `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="7b29a-512">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-512">Select the **Check Names** button.</span></span> <span data-ttu-id="7b29a-513">*DefaultAppPool* で、`IIS AppPool\DefaultAppPool` を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-513">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="7b29a-514">**[名前の確認]** ボタンが選択されているときには、`DefaultAppPool` の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-514">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="7b29a-515">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-515">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7b29a-516">`IIS AppPool\{APP POOL NAME}` 形式を使用します。ここで、プレースホルダー `{APP POOL NAME}` は、オブジェクト名を確認するときのアプリ プール名です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-516">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7b29a-518">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-518">Select **OK** .</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7b29a-520">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-520">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7b29a-521">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-521">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7b29a-522">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-522">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7b29a-523">たとえば、`DefaultAppPool` を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-523">Using the `DefaultAppPool` as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7b29a-524">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-524">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7b29a-525">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="7b29a-525">HTTP/2 support</span></span>

<span data-ttu-id="7b29a-526">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-526">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="7b29a-527">インプロセス</span><span class="sxs-lookup"><span data-stu-id="7b29a-527">In-process</span></span>
  * <span data-ttu-id="7b29a-528">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-528">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b29a-529">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="7b29a-529">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="7b29a-530">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="7b29a-530">Out-of-process</span></span>
  * <span data-ttu-id="7b29a-531">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-531">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b29a-532">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-532">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7b29a-533">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="7b29a-533">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7b29a-534">インプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-534">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="7b29a-535">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-535">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7b29a-536">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-536">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7b29a-537">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-537">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7b29a-538">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-538">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7b29a-539">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-539">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7b29a-540">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="7b29a-540">CORS preflight requests</span></span>

<span data-ttu-id="7b29a-541">" *このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-541">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7b29a-542">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-542">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7b29a-543">OPTIONS 要求を渡すように `web.config` でアプリの IIS のハンドラーを構成する方法については、[ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-543">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="7b29a-544">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="7b29a-544">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="7b29a-545">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-545">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="7b29a-546">[Application Initialization モジュール](#application-initialization-module): アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-546">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="7b29a-547">[アイドル タイムアウト](#idle-timeout): アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-547">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="7b29a-548">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="7b29a-548">Application Initialization Module</span></span>

<span data-ttu-id="7b29a-549">" *アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-549">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="7b29a-550">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-550">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="7b29a-551">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-551">The request triggers the app to start.</span></span> <span data-ttu-id="7b29a-552">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-552">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="7b29a-553">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-553">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="7b29a-554">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-554">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="7b29a-555">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-555">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7b29a-556">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-556">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="7b29a-557">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-557">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7b29a-558">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-558">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="7b29a-559">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-559">Open the **Add Roles and Features Wizard** .</span></span>
1. <span data-ttu-id="7b29a-560">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-560">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="7b29a-561">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-561">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7b29a-562">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-562">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="7b29a-563">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-563">Using IIS Manager:</span></span>

  1. <span data-ttu-id="7b29a-564">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-564">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7b29a-565">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-565">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7b29a-566">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-566">The default **Start Mode** is **OnDemand** .</span></span> <span data-ttu-id="7b29a-567">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-567">Set the **Start Mode** to **AlwaysRunning** .</span></span> <span data-ttu-id="7b29a-568">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-568">Select **OK** .</span></span>
  1. <span data-ttu-id="7b29a-569">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-569">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7b29a-570">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-570">Right-click the app and select **Manage Website** > **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7b29a-571">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-571">The default **Preload Enabled** setting is **False** .</span></span> <span data-ttu-id="7b29a-572">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-572">Set **Preload Enabled** to **True** .</span></span> <span data-ttu-id="7b29a-573">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-573">Select **OK** .</span></span>

* <span data-ttu-id="7b29a-574">`web.config` を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの web.config ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-574">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's web.config\` file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="7b29a-575">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="7b29a-575">Idle Timeout</span></span>

<span data-ttu-id="7b29a-576">" *アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-576">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="7b29a-577">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-577">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="7b29a-578">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-578">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="7b29a-579">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-579">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
1. <span data-ttu-id="7b29a-580">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-580">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="7b29a-581">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-581">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="7b29a-582">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-582">Select **OK** .</span></span>
1. <span data-ttu-id="7b29a-583">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-583">Recycle the worker process.</span></span>

<span data-ttu-id="7b29a-584">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-584">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="7b29a-585">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-585">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="7b29a-586">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-586">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="7b29a-587">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-587">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="7b29a-588">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="7b29a-588">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="7b29a-589">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="7b29a-589">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="7b29a-590">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-590">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7b29a-591">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="7b29a-591">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7b29a-592">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="7b29a-592">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7b29a-593">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="7b29a-593">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7b29a-594">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-594">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7b29a-595">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-595">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7b29a-596">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="7b29a-596">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7b29a-597">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-597">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7b29a-598">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7b29a-598">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7b29a-599">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-599">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7b29a-600">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-600">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7b29a-601">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="7b29a-601">Supported operating systems</span></span>

<span data-ttu-id="7b29a-602">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-602">The following operating systems are supported:</span></span>

* <span data-ttu-id="7b29a-603">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-603">Windows 7 or later</span></span>
* <span data-ttu-id="7b29a-604">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-604">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7b29a-605">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-605">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7b29a-606">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-606">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7b29a-607">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-607">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7b29a-608">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-608">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7b29a-609">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="7b29a-609">Supported platforms</span></span>

<span data-ttu-id="7b29a-610">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-610">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7b29a-611">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="7b29a-611">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7b29a-612">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-612">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7b29a-613">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-613">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7b29a-614">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="7b29a-614">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7b29a-615">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-615">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7b29a-616">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-616">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7b29a-617">ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-617">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="7b29a-618">インプロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-618">In-process hosting model</span></span>

<span data-ttu-id="7b29a-619">インプロセス ホスティング モデルを使用する場合、ASP.NET Core アプリはその IIS ワーカー プロセスと同じプロセスで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-619">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7b29a-620">インプロセス ホスティングでは、次の理由により、アウトプロセス ホスティングよりもパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-620">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="7b29a-621">要求はループバック アダプター経由でプロキシ化されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-621">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="7b29a-622">ループバック アダプターは、同じマシンに送信ネットワーク トラフィックを戻すネットワーク インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-622">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="7b29a-623">IIS では [Windows プロセス アクティブ化サービス (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) を使用してプロセス管理が処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-623">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b29a-624">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="7b29a-624">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="7b29a-625">アプリの初期化を実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-625">Performs app initialization.</span></span>
  * <span data-ttu-id="7b29a-626">[CoreCLR](/dotnet/standard/glossary#coreclr) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-626">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="7b29a-627">`Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="7b29a-627">Calls `Program.Main`.</span></span>
* <span data-ttu-id="7b29a-628">IIS ネイティブ要求の有効期間を処理します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-628">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="7b29a-629">.NET Framework をターゲットにする ASP.NET Core アプリではインプロセス ホスティング モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-629">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="7b29a-630">次の図は、IIS (ASP.NET Core モジュール) とインプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-630">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![インプロセス ホスティング シナリオの ASP.NET Core モジュール](index/_static/ancm-inprocess.png)

<span data-ttu-id="7b29a-632">Web からカーネル モードの HTTP.sys ドライバーに要求が届きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-632">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b29a-633">このドライバーによって、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) 上で IIS へのネイティブ要求がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-633">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b29a-634">ASP.NET Core モジュールは、ネイティブ要求を受け取り、それを IIS HTTP サーバー (`IISHttpServer`) に渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-634">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="7b29a-635">IIS HTTP サーバーは IIS 用のインプロセス サーバーの実装であり、要求がネイティブからマネージドに変換されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-635">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="7b29a-636">IIS HTTP サーバーによって要求が処理された後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-636">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b29a-637">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-637">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b29a-638">アプリの応答は、IIS の HTTP サーバーを経由して IIS に戻されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-638">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="7b29a-639">IIS は、要求を開始したクライアントに応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-639">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="7b29a-640">インプロセス ホスティングは既存のアプリではオプトインになっていますが、[dotnet new](/dotnet/core/tools/dotnet-new) テンプレートは既定ではすべての IIS および IIS Express シナリオにおいてインプロセス ホスティング モデルに設定されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-640">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="7b29a-641">`CreateDefaultBuilder` では、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> メソッドを呼び出し、 [CoreCLR](/dotnet/standard/glossary#coreclr) を起動して IIS ワーカー プロセス ( *w3wp.exe* または *iisexpress.exe* ) 内のアプリをホストすることで、<xref:Microsoft.AspNetCore.Hosting.Server.IServer> インスタンスを追加します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-641">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process ( *w3wp.exe* or *iisexpress.exe* ).</span></span> <span data-ttu-id="7b29a-642">パフォーマンス テストは、.NET Core アプリのインプロセス ホスティングでは、アプリのアウトプロセス ホスティングや [Kestrel](xref:fundamentals/servers/kestrel) サーバーへのプロキシ要求に比べ、スループットの要求が大幅に高くなることを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-642">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="7b29a-643">アウト プロセス ホスティング モデル</span><span class="sxs-lookup"><span data-stu-id="7b29a-643">Out-of-process hosting model</span></span>

<span data-ttu-id="7b29a-644">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理は ASP.NET Core モジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-644">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="7b29a-645">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-645">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7b29a-646">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-646">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b29a-647">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-647">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![アウト プロセス ホスティングのシナリオの ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="7b29a-649">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-649">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b29a-650">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-650">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b29a-651">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-651">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7b29a-652">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> 拡張機能によって `http://localhost:{PORT}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-652">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="7b29a-653">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-653">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7b29a-654">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-654">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7b29a-655">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-655">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b29a-656">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-656">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b29a-657">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-657">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7b29a-658">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-658">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7b29a-659">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-659">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7b29a-660">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-660">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7b29a-661">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-661">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7b29a-662">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="7b29a-662">Enable the IISIntegration components</span></span>

<span data-ttu-id="7b29a-663">`CreateWebHostBuilder` ( *Program.cs* ) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-663">When building a host in `CreateWebHostBuilder` ( *Program.cs* ), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7b29a-664">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-664">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7b29a-665">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-665">IIS options</span></span>

<span data-ttu-id="7b29a-666">**インプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="7b29a-666">**In-process hosting model**</span></span>

<span data-ttu-id="7b29a-667">IIS サーバーのオプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISServerOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-667">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="7b29a-668">次の例では、AutomaticAuthentication が無効になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-668">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="7b29a-669">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-669">Option</span></span>                         | <span data-ttu-id="7b29a-670">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-670">Default</span></span> | <span data-ttu-id="7b29a-671">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-671">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-672">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-672">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-673">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-673">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-674">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-674">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-675">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-675">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-676">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-676">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="7b29a-677">**アウトプロセス ホスティング モデル**</span><span class="sxs-lookup"><span data-stu-id="7b29a-677">**Out-of-process hosting model**</span></span>

<span data-ttu-id="7b29a-678">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-678">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="7b29a-679">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-679">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7b29a-680">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-680">Option</span></span>                         | <span data-ttu-id="7b29a-681">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-681">Default</span></span> | <span data-ttu-id="7b29a-682">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-682">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-683">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-683">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-684">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-684">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-685">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-685">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-686">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-686">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-687">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-687">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7b29a-688">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-688">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7b29a-689">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="7b29a-689">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7b29a-690">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-690">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7b29a-691">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-691">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7b29a-692">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-692">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7b29a-693">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-693">web.config file</span></span>

<span data-ttu-id="7b29a-694">*web.config* ファイルでは、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-694">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-695">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-695">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7b29a-696">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-696">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7b29a-697">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-697">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7b29a-698">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、 [発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-698">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7b29a-699">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-699">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7b29a-700">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-700">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7b29a-701">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-701">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7b29a-702">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-702">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7b29a-703">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-703">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7b29a-704">Web SDK ファイルの変換を無効にすると、 *processPath* と *引数* 開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-704">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="7b29a-705">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-705">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7b29a-706">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="7b29a-706">web.config file location</span></span>

<span data-ttu-id="7b29a-707">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、 *web.config* ファイルが、展開されるアプリの [コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-707">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7b29a-708">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-708">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7b29a-709">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-709">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7b29a-710">アプリの物理パスには、 *\<assembly>.runtimeconfig.json* 、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-710">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json* , *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json* .</span></span> <span data-ttu-id="7b29a-711">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-711">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7b29a-712">*web.config* ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-712">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7b29a-713">***web.config* ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。 *web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="7b29a-713">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7b29a-714">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="7b29a-714">Transform web.config</span></span>

<span data-ttu-id="7b29a-715">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-715">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7b29a-716">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-716">IIS configuration</span></span>

<span data-ttu-id="7b29a-717">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-717">**Windows Server operating systems**</span></span>

<span data-ttu-id="7b29a-718">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-718">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7b29a-719">**[管理]** メニューから **役割と機能の追加** ウィザードを使用するか、 **サーバー マネージャー** にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-719">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7b29a-720">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-720">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7b29a-722">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-722">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7b29a-723">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-723">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7b29a-725">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-725">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-726">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-726">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7b29a-727">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-727">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-728">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-728">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-729">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-729">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-730">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-730">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-731">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-731">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7b29a-732">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-732">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-733">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-733">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-734">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-734">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7b29a-735">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-735">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7b29a-736">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-736">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7b29a-737">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-737">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-738">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-738">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7b29a-739">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-739">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7b29a-740">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-740">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7b29a-741">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-741">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7b29a-742">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-742">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-743">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-743">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7b29a-744">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-744">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-745">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-745">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7b29a-746">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-746">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-747">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-747">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-748">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-748">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-749">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-749">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-750">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-750">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7b29a-751">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-751">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-752">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-752">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-753">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-753">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7b29a-755">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-755">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7b29a-756">ホスティング システムに *.NET Core ホスティング バンドル* をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-756">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7b29a-757">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-757">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-758">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-758">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7b29a-759">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-759">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7b29a-760">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-760">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7b29a-761">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="7b29a-761">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7b29a-762">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-762">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="7b29a-763">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="7b29a-763">Download</span></span>

1. <span data-ttu-id="7b29a-764">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-764">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7b29a-765">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-765">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7b29a-766">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-766">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7b29a-767">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-767">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7b29a-768">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-768">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7b29a-769">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-769">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7b29a-770">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="7b29a-770">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7b29a-771">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-771">Run the installer on the server.</span></span> <span data-ttu-id="7b29a-772">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-772">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7b29a-773">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-773">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7b29a-774">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-774">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7b29a-775">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-775">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-776">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-776">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7b29a-777">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-777">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-778">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-778">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7b29a-779">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-779">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7b29a-780">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-780">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7b29a-781">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 ( *applicationHost.config* ) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-781">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration ( *applicationHost.config* ) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7b29a-782">" *ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-782">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7b29a-783">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-783">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7b29a-784">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-784">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7b29a-785">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-785">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7b29a-786">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-786">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="7b29a-787">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-787">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="7b29a-788">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-788">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="7b29a-789">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-789">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7b29a-790">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-790">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="7b29a-791">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-791">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="7b29a-792">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-792">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7b29a-793">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-793">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7b29a-794">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-794">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7b29a-795">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-795">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7b29a-796">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-796">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7b29a-797">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-797">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7b29a-798">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="7b29a-798">Create the IIS site</span></span>

1. <span data-ttu-id="7b29a-799">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-799">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7b29a-800">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-800">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7b29a-801">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-801">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7b29a-802">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-802">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7b29a-803">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-803">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7b29a-804">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-804">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-805">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-805">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7b29a-806">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-806">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7b29a-808">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="7b29a-808">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7b29a-809">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-809">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7b29a-810">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-810">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7b29a-811">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-811">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7b29a-812">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-812">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7b29a-813">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-813">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7b29a-814">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-814">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7b29a-815">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-815">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-816">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-816">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7b29a-818">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-818">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7b29a-819">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-819">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7b29a-820">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-820">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7b29a-821">*ASP.NET Core 2.2 以降* : [インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の [自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-821">*ASP.NET Core 2.2 or later* : For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="7b29a-822">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-822">In the **Actions** sidebar of IIS Manager > **Application Pools** , select **Set Application Pool Defaults** or **Advanced Settings** .</span></span> <span data-ttu-id="7b29a-823">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-823">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="7b29a-824">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-824">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="7b29a-825">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-825">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7b29a-826">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-826">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7b29a-827">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-827">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7b29a-828">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-828">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7b29a-829">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-829">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7b29a-830">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="7b29a-830">Deploy the app</span></span>

<span data-ttu-id="7b29a-831">「 [IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-831">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7b29a-832">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-832">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7b29a-833">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-833">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7b29a-834">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-834">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7b29a-835">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-835">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7b29a-837">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="7b29a-837">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7b29a-838">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-838">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7b29a-839">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-839">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7b29a-840">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="7b29a-840">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7b29a-841">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-841">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7b29a-842">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-842">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7b29a-843">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="7b29a-843">Browse the website</span></span>

<span data-ttu-id="7b29a-844">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-844">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7b29a-845">次の例では、サイトは IIS の **ホスト名**`www.mysite.com` に **ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-845">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7b29a-846">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-846">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7b29a-848">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-848">Locked deployment files</span></span>

<span data-ttu-id="7b29a-849">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-849">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7b29a-850">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-850">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7b29a-851">展開でロックされているファイルをリリースするには、次の **いずれか** の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-851">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-852">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-852">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7b29a-853">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-853">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7b29a-854">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-854">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="7b29a-855">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-855">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7b29a-856">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-856">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7b29a-857">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-857">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="7b29a-858">データの保護</span><span class="sxs-lookup"><span data-stu-id="7b29a-858">Data protection</span></span>

<span data-ttu-id="7b29a-859">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-859">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7b29a-860">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-860">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7b29a-861">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-861">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7b29a-862">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-862">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7b29a-863">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-863">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7b29a-864">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-864">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7b29a-865">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-865">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7b29a-866">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-866">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7b29a-867">キーリングを保持するために IIS でのデータ保護を構成するには、次の **いずれか** の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-867">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-868">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-868">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7b29a-869">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-869">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7b29a-870">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-870">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7b29a-871">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-871">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7b29a-872">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-872">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7b29a-873">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-873">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7b29a-874">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-874">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7b29a-875">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-875">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7b29a-876">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-876">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7b29a-877">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-877">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7b29a-878">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-878">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7b29a-879">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-879">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7b29a-880">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-880">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7b29a-881">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-881">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7b29a-882">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-882">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7b29a-883">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-883">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7b29a-884">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-884">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7b29a-885">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-885">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7b29a-886">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-886">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7b29a-887">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-887">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7b29a-888">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-888">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7b29a-889">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-889">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7b29a-890">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-890">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7b29a-891">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-891">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7b29a-892">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-892">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7b29a-893">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-893">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7b29a-894">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-894">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7b29a-895">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-895">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7b29a-896">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-896">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7b29a-897">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-897">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7b29a-898">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-898">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7b29a-899">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-899">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7b29a-900">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-900">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7b29a-901">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-901">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7b29a-902">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-902">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7b29a-903">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-903">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7b29a-904">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-904">Virtual Directories</span></span>

<span data-ttu-id="7b29a-905">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-905">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7b29a-906">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-906">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7b29a-907">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="7b29a-907">Sub-applications</span></span>

<span data-ttu-id="7b29a-908">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-908">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7b29a-909">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-909">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7b29a-910">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-910">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7b29a-911">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-911">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7b29a-912">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-912">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7b29a-913">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-913">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7b29a-914">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-914">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7b29a-915">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-915">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7b29a-916">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-916">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7b29a-917">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="7b29a-917">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7b29a-918">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-918">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7b29a-919">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-919">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7b29a-920">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-920">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7b29a-921">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-921">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7b29a-922">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-922">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7b29a-923">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-923">Select **OK** .</span></span>

<span data-ttu-id="7b29a-924">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-924">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7b29a-925">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-925">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7b29a-926">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-926">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7b29a-927">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-927">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7b29a-928">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-928">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7b29a-929">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-929">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7b29a-930">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-930">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7b29a-931">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="7b29a-931">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7b29a-932">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、 [環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある " *AppCmd.exe コマンド* " のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-932">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7b29a-933">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="7b29a-933">Configuration sections of web.config</span></span>

<span data-ttu-id="7b29a-934">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-934">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7b29a-935">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-935">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7b29a-936">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-936">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7b29a-937">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="7b29a-937">Application Pools</span></span>

<span data-ttu-id="7b29a-938">アプリケーション プールの分離は、ホスティング モデルによって決定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-938">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="7b29a-939">インプロセス ホスティング: 別のアプリケーション プールでアプリケーションを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-939">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="7b29a-940">アウトプロセス ホスティング: アプリケーションをそれぞれ専用のアプリケーション プールで実行して、アプリケーションを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-940">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="7b29a-941">IIS の **[Web サイトの追加]** ダイアログの既定では、アプリケーションごとに 1 つのアプリケーション プールです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-941">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="7b29a-942">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-942">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7b29a-943">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-943">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7b29a-944">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="7b29a-944">Application Pool Identity</span></span>

<span data-ttu-id="7b29a-945">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-945">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7b29a-946">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-946">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7b29a-947">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、 **ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-947">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="7b29a-949">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-949">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7b29a-950">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-950">Resources can be secured using this identity.</span></span> <span data-ttu-id="7b29a-951">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-951">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7b29a-952">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-952">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7b29a-953">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-953">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7b29a-954">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-954">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7b29a-955">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-955">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7b29a-956">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-956">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7b29a-957">**[選択するオブジェクト名を入力してください]** 領域に、「 **IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-957">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="7b29a-958">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-958">Select the **Check Names** button.</span></span> <span data-ttu-id="7b29a-959">*DefaultAppPool* で、 **IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-959">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool** .</span></span> <span data-ttu-id="7b29a-960">**[名前の確認]** ボタンが選択されているときには、 **DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-960">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="7b29a-961">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-961">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7b29a-962">オブジェクト名を確認するときには、 **IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-962">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7b29a-964">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-964">Select **OK** .</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7b29a-966">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-966">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7b29a-967">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-967">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7b29a-968">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-968">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7b29a-969">たとえば、 *DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-969">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7b29a-970">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-970">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7b29a-971">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="7b29a-971">HTTP/2 support</span></span>

<span data-ttu-id="7b29a-972">[HTTP/2](https://httpwg.org/specs/rfc7540.html) は、次の IIS 展開シナリオでの ASP.NET Core でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-972">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="7b29a-973">インプロセス</span><span class="sxs-lookup"><span data-stu-id="7b29a-973">In-process</span></span>
  * <span data-ttu-id="7b29a-974">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-974">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b29a-975">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="7b29a-975">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="7b29a-976">アウトプロセス</span><span class="sxs-lookup"><span data-stu-id="7b29a-976">Out-of-process</span></span>
  * <span data-ttu-id="7b29a-977">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-977">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7b29a-978">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-978">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7b29a-979">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="7b29a-979">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7b29a-980">インプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/2` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-980">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="7b29a-981">アウトプロセスの展開の場合、HTTP/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) によって `HTTP/1.1` が報告されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-981">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7b29a-982">インプロセスおよびアウトプロセス ホスティング モデルの詳細については、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-982">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7b29a-983">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-983">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7b29a-984">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-984">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7b29a-985">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-985">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7b29a-986">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="7b29a-986">CORS preflight requests</span></span>

<span data-ttu-id="7b29a-987">" *このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-987">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7b29a-988">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-988">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7b29a-989">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、 [ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-989">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="7b29a-990">Application Initialization モジュールとアイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="7b29a-990">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="7b29a-991">IIS 内で ASP.NET Core モジュール バージョン 2 によってホストされている場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-991">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="7b29a-992">[Application Initialization モジュール](#application-initialization-module): アプリのホストされている[インプロセス](#in-process-hosting-model)または[アウトプロセス](#out-of-process-hosting-model)は、ワーカー プロセスの再起動時またはサーバーの再起動時に自動的に起動するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-992">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="7b29a-993">[アイドル タイムアウト](#idle-timeout): アプリのホストされている[インプロセス](#in-process-hosting-model)は、非アクティブ期間中にタイムアウトしないように構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-993">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="7b29a-994">Application Initialization モジュール</span><span class="sxs-lookup"><span data-stu-id="7b29a-994">Application Initialization Module</span></span>

<span data-ttu-id="7b29a-995">" *アプリのホストされているインプロセスとアウトプロセスに適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-995">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="7b29a-996">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) は、アプリ プールが開始するときまたはリサイクルされるときに、アプリに HTTP 要求を送信する IIS 機能です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-996">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="7b29a-997">要求によってアプリの起動がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-997">The request triggers the app to start.</span></span> <span data-ttu-id="7b29a-998">既定では、IIS ではアプリのルート URL (`/`) に対して要求が発行され、アプリが初期化されます (構成の詳細については[その他の技術情報](#application-initialization-module-and-idle-timeout-additional-resources)を参照)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-998">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="7b29a-999">IIS Application Initialization 役割の機能が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-999">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="7b29a-1000">Windows 7 以降のデスクトップ システム上で、IIS をローカルで使う場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1000">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="7b29a-1001">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1001">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="7b29a-1002">**[インターネット インフォメーション サービス]** > **[World Wide Web サービス]** > **[アプリケーション開発機能]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1002">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features** .</span></span>
1. <span data-ttu-id="7b29a-1003">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1003">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7b29a-1004">Windows Server 2008 R2 以降の場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1004">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="7b29a-1005">**[役割と機能の追加ウィザード]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1005">Open the **Add Roles and Features Wizard** .</span></span>
1. <span data-ttu-id="7b29a-1006">**[役割サービスの選択]** パネルで、 **[アプリケーション開発]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1006">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="7b29a-1007">**[Application Initialization]** のチェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1007">Select the check box for **Application Initialization** .</span></span>

<span data-ttu-id="7b29a-1008">次の方法のいずれかを使って、サイトの Application Initialization モジュールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1008">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="7b29a-1009">IIS マネージャーを使う場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1009">Using IIS Manager:</span></span>

  1. <span data-ttu-id="7b29a-1010">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1010">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7b29a-1011">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1011">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7b29a-1012">既定の **[開始モード]** は **[オンデマンド]** です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1012">The default **Start Mode** is **OnDemand** .</span></span> <span data-ttu-id="7b29a-1013">**[開始モード]** を **[常時実行]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1013">Set the **Start Mode** to **AlwaysRunning** .</span></span> <span data-ttu-id="7b29a-1014">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1014">Select **OK** .</span></span>
  1. <span data-ttu-id="7b29a-1015">**[接続]** パネルの **[サイト]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1015">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="7b29a-1016">アプリを右クリックして、 **[Web サイトの管理]** > **[詳細設定]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1016">Right-click the app and select **Manage Website** > **Advanced Settings** .</span></span>
  1. <span data-ttu-id="7b29a-1017">既定の **[有効化されたプリロード]** 設定は **[False]** です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1017">The default **Preload Enabled** setting is **False** .</span></span> <span data-ttu-id="7b29a-1018">**[有効化されたプリロード]** を **True** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1018">Set **Preload Enabled** to **True** .</span></span> <span data-ttu-id="7b29a-1019">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1019">Select **OK** .</span></span>

* <span data-ttu-id="7b29a-1020">*web.config* を使う場合、`doAppInitAfterRestart` を `true` に設定した `<applicationInitialization>` 要素を、アプリの *web.config* ファイル内の `<system.webServer>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1020">Using *web.config* , add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

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

### <a name="idle-timeout"></a><span data-ttu-id="7b29a-1021">アイドル タイムアウト</span><span class="sxs-lookup"><span data-stu-id="7b29a-1021">Idle Timeout</span></span>

<span data-ttu-id="7b29a-1022">" *アプリのホストされているインプロセスにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-1022">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="7b29a-1023">アプリがアイドル状態にならないようにするには、IIS マネージャーを使ってアプリ プールのアイドル タイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1023">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="7b29a-1024">**[接続]** パネルの **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1024">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="7b29a-1025">一覧でアプリのアプリ プールを右クリックして、 **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1025">Right-click the app's app pool in the list and select **Advanced Settings** .</span></span>
1. <span data-ttu-id="7b29a-1026">既定の **[アイドル状態のタイムアウト (分)]** は **[20]** 分です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1026">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="7b29a-1027">**[アイドル状態のタイムアウト (分)]** を **[0]** (ゼロ) に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1027">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="7b29a-1028">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1028">Select **OK** .</span></span>
1. <span data-ttu-id="7b29a-1029">ワーカー プロセスをリサイクルします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1029">Recycle the worker process.</span></span>

<span data-ttu-id="7b29a-1030">アプリのホストされている[アウトプロセス](#out-of-process-hosting-model)がタイムアウトにならないようにするには、次の方法のいずれかを使います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1030">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="7b29a-1031">実行させ続けるために、外部サービスからアプリに ping を送信します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1031">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="7b29a-1032">アプリでバックグラウンド サービスのみをホストしている場合は、IIS ホスティングを回避し、[ASP.NET Core アプリをホストするための Windows サービス](xref:host-and-deploy/windows-service)を使います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1032">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="7b29a-1033">Application Initialization モジュールとアイドル タイムアウトに関するその他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-1033">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="7b29a-1034">IIS 8.0 Application Initialization</span><span class="sxs-lookup"><span data-stu-id="7b29a-1034">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="7b29a-1035">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="7b29a-1035">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="7b29a-1036">[アプリケーション プールのプロセス モデルの設定 \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1036">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7b29a-1037">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="7b29a-1037">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7b29a-1038">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="7b29a-1038">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7b29a-1039">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="7b29a-1039">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7b29a-1040">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-1040">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7b29a-1041">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-1041">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7b29a-1042">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="7b29a-1042">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7b29a-1043">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-1043">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7b29a-1044">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7b29a-1044">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7b29a-1045">ASP.NET Core アプリを IIS サーバーに発行する手順のチュートリアルについては、<xref:tutorials/publish-to-iis> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1045">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="7b29a-1046">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-1046">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="7b29a-1047">サポートされるオペレーティング システム</span><span class="sxs-lookup"><span data-stu-id="7b29a-1047">Supported operating systems</span></span>

<span data-ttu-id="7b29a-1048">次のオペレーティング システムがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1048">The following operating systems are supported:</span></span>

* <span data-ttu-id="7b29a-1049">Windows 7 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-1049">Windows 7 or later</span></span>
* <span data-ttu-id="7b29a-1050">Windows Server 2008 R2 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-1050">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="7b29a-1051">[HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) は、IIS が含まれるリバース プロキシ構成では動作しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1051">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="7b29a-1052">[Kestrel サーバー](xref:fundamentals/servers/kestrel)を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1052">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="7b29a-1053">Azure でのホスティングの情報については、「<xref:host-and-deploy/azure-apps/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1053">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="7b29a-1054">トラブルシューティング ガイダンスについては、「<xref:test/troubleshoot>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1054">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="7b29a-1055">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="7b29a-1055">Supported platforms</span></span>

<span data-ttu-id="7b29a-1056">32 ビット (x86) または 64 ビット (x64) デプロイ用に発行されるアプリがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1056">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="7b29a-1057">アプリが次の条件を満たさない限り、32 ビット (x86) .NET Core SDK を使う 32 ビット アプリをデプロイします:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1057">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="7b29a-1058">64 ビット アプリ用の大規模な仮想メモリ アドレス空間が必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1058">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="7b29a-1059">大規模な IIS スタック サイズが必要。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1059">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="7b29a-1060">64 ビットのネイティブの依存関係がある。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1060">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="7b29a-1061">64 ビット (x64) .NET Core SDK を使って 64 ビット アプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1061">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="7b29a-1062">ホスト システム上に 64 ビット ランタイムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1062">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="7b29a-1063">ASP.NET Core には、既定のクロスプラットフォーム HTTP サーバーである [Kestrel サーバー](xref:fundamentals/servers/kestrel)が付属しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1063">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="7b29a-1064">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) または [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) を使用すると、アプリは [Kestrel サーバー](xref:fundamentals/servers/index#kestrel)を使用して IIS ワーカー プロセスとは異なるプロセス内で実行されます (" *プロセス外* ")。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1064">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process ( *out-of-process* ) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="7b29a-1065">ASP.NET Core アプリは IIS ワーカー プロセスとは独立したプロセスで実行されるため、プロセス管理はモジュールによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1065">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="7b29a-1066">モジュールでは、最初の要求が届いたときに ASP.NET Core アプリのプロセスが開始され、プロセスがシャットダウンまたはクラッシュした場合はアプリが再起動されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1066">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7b29a-1067">この動作は、インプロセスで実行されるアプリであり、[WAS (Windows プロセス アクティブ化サービス)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) によって管理されるアプリと基本的に同じです。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1067">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7b29a-1068">次の図は、IIS (ASP.NET Core モジュール) とアウトプロセスでホストされるアプリとの間のリレーションシップを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1068">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core モジュール](index/_static/ancm-outofprocess.png)

<span data-ttu-id="7b29a-1070">要求は、Web からカーネル モードの HTTP.sys ドライバーに到着します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1070">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7b29a-1071">ドライバーは、Web サイトの構成ポート (通常は 80 (HTTP) または 443 (HTTPS)) で IIS への要求をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1071">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7b29a-1072">モジュールでは、アプリのランダムなポート (ポート 80 または 443 ではありません) で Kestrel に要求が転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1072">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7b29a-1073">モジュールによってスタートアップ時に環境変数を介してポートが指定されると、[IIS 統合ミドルウェア](xref:host-and-deploy/iis/index#enable-the-iisintegration-components)によって `http://localhost:{port}` をリッスンするようにサーバーが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1073">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="7b29a-1074">追加のチェックが実行され、モジュールから発生していない要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1074">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7b29a-1075">モジュールでは HTTPS 転送がサポートされていないため、要求は HTTPS を介して IIS によって受信された場合でも、HTTP を介して転送されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1075">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7b29a-1076">Kestrel によってモジュールから要求が取り込まれた後、その要求は ASP.NET Core ミドルウェア パイプラインにプッシュされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1076">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7b29a-1077">ミドルウェア パイプラインは要求を処理し、`HttpContext` インスタンスとしてアプリのロジックに渡します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1077">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7b29a-1078">IIS 統合によって追加されたミドルウェアでは、カーネルへの要求の転送を考慮して、スキーム、リモート IP、およびパスベースが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1078">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7b29a-1079">アプリの応答が IIS に戻され、IIS はその応答を、要求を開始した HTTP クライアントに返します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1079">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7b29a-1080">`CreateDefaultBuilder` は [Kestrel](xref:fundamentals/servers/kestrel) サーバーを Web サーバーとして構成し、ベース パスとポートを [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)に構成することで、IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1080">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7b29a-1081">ASP.NET Core モジュールでは、バックエンド プロセスに割り当てる動的なポートが生成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1081">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="7b29a-1082">`CreateDefaultBuilder` では <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1082">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> method.</span></span> <span data-ttu-id="7b29a-1083">`UseIISIntegration` では、localhost の IP アドレス (`127.0.0.1`) で動的なポートをリッスンするように Kestrel が構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1083">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="7b29a-1084">動的なポートが 1234 である場合、Kestrel は `127.0.0.1:1234` でリッスンします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1084">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="7b29a-1085">この構成によって、以下から提供されるその他の URL 構成が置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1085">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="7b29a-1086">Kestrel の Listen API</span><span class="sxs-lookup"><span data-stu-id="7b29a-1086">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="7b29a-1087">[構成](xref:fundamentals/configuration/index) (または[コマンド ラインの --urls オプション](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="7b29a-1087">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="7b29a-1088">モジュールを使用する場合、`UseUrls` または Kestrel の `Listen` API を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1088">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="7b29a-1089">`UseUrls` または `Listen` が呼び出されると、IIS なしでアプリを実行しているときのみ、Kestrel で指定したポートがリッスンされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1089">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="7b29a-1090">ASP.NET Core モジュール構成のガイダンスについては、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1090">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="7b29a-1091">ホスティングの詳細については、「[Hosting in ASP.NET Core](xref:fundamentals/index#host)」(ASP.NET Core でのホスティング) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1091">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="7b29a-1092">アプリケーション構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-1092">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="7b29a-1093">IISIntegration コンポーネントを有効にする</span><span class="sxs-lookup"><span data-stu-id="7b29a-1093">Enable the IISIntegration components</span></span>

<span data-ttu-id="7b29a-1094">`CreateWebHostBuilder` ( *Program.cs* ) でホストを構築する場合は、<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> を呼び出して IIS 統合を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1094">When building a host in `CreateWebHostBuilder` ( *Program.cs* ), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="7b29a-1095">`CreateDefaultBuilder` の詳細については、「<xref:fundamentals/host/web-host#set-up-a-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1095">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="7b29a-1096">IIS のオプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-1096">IIS options</span></span>

| <span data-ttu-id="7b29a-1097">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-1097">Option</span></span>                         | <span data-ttu-id="7b29a-1098">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-1098">Default</span></span> | <span data-ttu-id="7b29a-1099">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-1099">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-1100">`true` の場合、IIS サーバーが [Windows 認証](xref:security/authentication/windowsauth)によって認証された `HttpContext.User` を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1100">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-1101">`false` の場合、サーバーは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときにチャレンジに応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1101">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-1102">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1102">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-1103">詳細については、[Windows 認証](xref:security/authentication/windowsauth)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1103">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-1104">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1104">Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="7b29a-1105">IIS オプションを構成するには、<xref:Microsoft.AspNetCore.Builder.IISOptions> 用のサービス構成を <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> に含めます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1105">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="7b29a-1106">次の例では、アプリが `HttpContext.Connection.ClientCertificate` を設定できません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1106">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="7b29a-1107">オプション</span><span class="sxs-lookup"><span data-stu-id="7b29a-1107">Option</span></span>                         | <span data-ttu-id="7b29a-1108">Default</span><span class="sxs-lookup"><span data-stu-id="7b29a-1108">Default</span></span> | <span data-ttu-id="7b29a-1109">設定</span><span class="sxs-lookup"><span data-stu-id="7b29a-1109">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="7b29a-1110">`true` の場合、[IIS 統合ミドルウェア](#enable-the-iisintegration-components)によって、[Windows 認証](xref:security/authentication/windowsauth)で認証された `HttpContext.User` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1110">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="7b29a-1111">`false` の場合、ミドルウェアは `HttpContext.User` の ID を提供するだけで、`AuthenticationScheme` によって明示的に要求されたときに課題に応答します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1111">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="7b29a-1112">`AutomaticAuthentication` を機能させるためには、IIS で Windows 認証を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1112">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="7b29a-1113">詳細については、「[Windows 認証](xref:security/authentication/windowsauth)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1113">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="7b29a-1114">ログイン ページでユーザーに表示名が表示されるように設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1114">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="7b29a-1115">`true` の場合、`MS-ASPNETCORE-CLIENTCERT` 要求ヘッダーが指定されていると、`HttpContext.Connection.ClientCertificate` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1115">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="7b29a-1116">プロキシ サーバーとロード バランサーのシナリオ</span><span class="sxs-lookup"><span data-stu-id="7b29a-1116">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="7b29a-1117">要求が発生したスキーム (HTTP/HTTPS) とリモート IP アドレスを転送するために、Forwarded Headers Middleware を構成する [IIS 統合ミドルウェア](#enable-the-iisintegration-components)と、ASP.NET Core モジュールが構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1117">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="7b29a-1118">追加のプロキシ サーバーとロード バランサーの背後でホストされているアプリでは、追加の構成が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1118">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="7b29a-1119">詳細については、「[プロキシ サーバーとロード バランサーを使用するために ASP.NET Core を構成する](xref:host-and-deploy/proxy-load-balancer)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1119">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="7b29a-1120">web.config ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-1120">web.config file</span></span>

<span data-ttu-id="7b29a-1121">*web.config* ファイルでは、 [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1121">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-1122">*web.config* ファイルの作成、変換、および公開は、プロジェクトの公開時に、MSBuild ターゲット (`_TransformWebConfig`) によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1122">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="7b29a-1123">このターゲットは、Web SDK ターゲット (`Microsoft.NET.Sdk.Web`) に存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1123">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="7b29a-1124">SDK は、プロジェクト ファイルの先頭で設定されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1124">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7b29a-1125">プロジェクト内に *web.config* ファイルが存在しない場合、そのファイルは ASP.NET Core モジュールを構成するための適切な *processPath* と *arguments* を使用して作成され、 [発行された出力](xref:host-and-deploy/directory-structure)に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1125">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="7b29a-1126">プロジェクトに *web.config* ファイルが含まれていない場合、そのファイルは ASP.NET Core モジュールを構成するための正しい *processPath* と *arguments* を使用して作成され、発行された出力に移行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1126">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="7b29a-1127">変換によりファイル内の IIS 構成の設定が変わることはありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1127">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="7b29a-1128">*web.config* ファイルは、アクティブな IIS モジュールを制御する追加の IIS 構成設定を提供する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1128">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="7b29a-1129">ASP.NET Core アプリを使用して要求を処理できる IIS モジュールの詳細については、[IIS モジュール](xref:host-and-deploy/iis/modules)のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1129">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="7b29a-1130">Web SDK によって *web.config* ファイルが変換されないようにするため、 **\<IsTransformWebConfigDisabled>** プロパティをプロジェクト ファイルで使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1130">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="7b29a-1131">Web SDK ファイルの変換を無効にすると、 *processPath* と *引数* 開発者によって手動で設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1131">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="7b29a-1132">詳細については、「<xref:host-and-deploy/aspnet-core-module>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1132">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="7b29a-1133">web.config ファイルの場所</span><span class="sxs-lookup"><span data-stu-id="7b29a-1133">web.config file location</span></span>

<span data-ttu-id="7b29a-1134">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)を正しく設定するためには、 *web.config* ファイルが、展開されるアプリの [コンテンツ ルート](xref:fundamentals/index#content-root) パス (通常はアプリ ベース パス) に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1134">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="7b29a-1135">これは、IIS に提供される web サイトの物理パスと同じ場所です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1135">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="7b29a-1136">*Web.config* ファイルは、Web 配置を使用して複数のアプリの発行を有効にするため、アプリのルートで必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1136">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="7b29a-1137">アプリの物理パスには、 *\<assembly>.runtimeconfig.json* 、 *\<assembly>.xml* (XML ドキュメントのコメント)、 *\<assembly>.deps.json* などの機密性の高いファイルが存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1137">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json* , *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json* .</span></span> <span data-ttu-id="7b29a-1138">*web.config* ファイルが存在し、サイトは通常どおり起動した場合、IIS は、これらの機密性の高いファイルが要求された場合にファイルを提供しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1138">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="7b29a-1139">*web.config* ファイルが存在しないか、不適切な名前が付けられているか、または通常の起動用にサイトを構成できない場合、IIS が機密性の高いファイルを公開する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1139">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="7b29a-1140">***web.config* ファイルは、展開環境に常に存在し、適切な名前が付けられ、通常の起動用にサイトを構成できる必要があります。 *web.config* ファイルを運用環境の展開から削除しないでください。**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1140">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="7b29a-1141">web.config を変換する</span><span class="sxs-lookup"><span data-stu-id="7b29a-1141">Transform web.config</span></span>

<span data-ttu-id="7b29a-1142">発行時に *web.config* を変換する必要がある場合 (たとえば、構成、プロファイル、環境に基づいて環境変数を設定する場合) は、<xref:host-and-deploy/iis/transform-webconfig> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1142">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="7b29a-1143">IIS 構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-1143">IIS configuration</span></span>

<span data-ttu-id="7b29a-1144">**Windows Server オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1144">**Windows Server operating systems**</span></span>

<span data-ttu-id="7b29a-1145">**Web サーバー (IIS)** サーバーの役割を有効にし、役割のサービスを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1145">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="7b29a-1146">**[管理]** メニューから **役割と機能の追加** ウィザードを使用するか、 **サーバー マネージャー** にあるリンクを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1146">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager** .</span></span> <span data-ttu-id="7b29a-1147">**[サーバーの役割]** のステップで、 **[Web サーバー (IIS)]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1147">On the **Server Roles** step, check the box for **Web Server (IIS)** .</span></span>

   ![[サーバーの役割の選択] のステップで Web サーバー IIS の役割を選択します。](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="7b29a-1149">**[機能]** のステップの後に、 **[役割サービスの]** のステップで Web サーバー (IIS) を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1149">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="7b29a-1150">希望する IIS の役割サービスを選択するか、既定の役割サービスをそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1150">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![[役割サービスの選択] のステップで既定の役割サービスを選択します。](index/_static/role-services-ws2016.png)

   <span data-ttu-id="7b29a-1152">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1152">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-1153">Windows 認証を有効にするには、 **[Web サーバー]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1153">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security** .</span></span> <span data-ttu-id="7b29a-1154">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1154">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-1155">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1155">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-1156">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1156">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-1157">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1157">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-1158">WebSocket を有効にするには、 **[Web サーバー]**  >  **[アプリケーション開発]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1158">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development** .</span></span> <span data-ttu-id="7b29a-1159">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1159">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-1160">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1160">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-1161">**[確認]** のステップを経て Web サーバーの役割とサービスをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1161">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="7b29a-1162">**Web サーバー (IIS)** の役割をインストールした後、サーバーと IIS の再起動は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1162">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="7b29a-1163">**Windows デスクトップ オペレーティング システム**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1163">**Windows desktop operating systems**</span></span>

<span data-ttu-id="7b29a-1164">**[IIS 管理コンソール]** と **[World Wide Web サービス]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1164">Enable the **IIS Management Console** and **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-1165">**[コントロール パネル]**  > **[プログラム]** > **[プログラムと機能]** >  **[Windows の機能の有効化または無効化]** (画面の左側) に移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1165">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="7b29a-1166">**インターネット インフォメーション サービス (IIS)** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1166">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="7b29a-1167">**[Web 管理ツール]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1167">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="7b29a-1168">**[IIS 管理コンソール]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1168">Check the box for **IIS Management Console** .</span></span>

1. <span data-ttu-id="7b29a-1169">**[World Wide Web サービス]** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1169">Check the box for **World Wide Web Services** .</span></span>

1. <span data-ttu-id="7b29a-1170">**[World Wide Web サービス]** の既定の機能をそのまま使用するか、IIS 機能をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1170">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="7b29a-1171">**Windows 認証 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1171">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="7b29a-1172">Windows 認証を有効にするには、 **[World Wide Web サービス]**  >  **[セキュリティ]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1172">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security** .</span></span> <span data-ttu-id="7b29a-1173">**[Windows 認証]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1173">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="7b29a-1174">詳細については、「[Windows 認証 \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)」および [Windows 認証の構成](xref:security/authentication/windowsauth)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1174">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="7b29a-1175">**Websocket (省略可能)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1175">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="7b29a-1176">WebSockets は、ASP.NET Core 1.1 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1176">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="7b29a-1177">WebSocket を有効にするには、 **[World Wide Web サービス]**  >  **[アプリケーション開発機能]** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1177">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features** .</span></span> <span data-ttu-id="7b29a-1178">**[WebSocket プロトコル]** 機能を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1178">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="7b29a-1179">詳細については、[WebSockets](xref:fundamentals/websockets) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1179">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="7b29a-1180">IIS のインストールに再起動が必要な場合は、システムを再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1180">If the IIS installation requires a restart, restart the system.</span></span>

![[Windows の機能] で [IIS 管理コンソール] と [World Wide Web サービス] を選択します。](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7b29a-1182">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-1182">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7b29a-1183">ホスティング システムに *.NET Core ホスティング バンドル* をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1183">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="7b29a-1184">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1184">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7b29a-1185">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1185">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7b29a-1186">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1186">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7b29a-1187">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1187">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7b29a-1188">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1188">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7b29a-1189">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1189">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="7b29a-1190">ダウンロード</span><span class="sxs-lookup"><span data-stu-id="7b29a-1190">Download</span></span>

1. <span data-ttu-id="7b29a-1191">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1191">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7b29a-1192">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1192">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7b29a-1193">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1193">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7b29a-1194">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1194">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7b29a-1195">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1195">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7b29a-1196">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1196">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="7b29a-1197">ホスティング バンドルをインストールする</span><span class="sxs-lookup"><span data-stu-id="7b29a-1197">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="7b29a-1198">サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1198">Run the installer on the server.</span></span> <span data-ttu-id="7b29a-1199">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1199">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7b29a-1200">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1200">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7b29a-1201">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1201">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7b29a-1202">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1202">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-1203">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1203">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7b29a-1204">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1204">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7b29a-1205">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1205">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7b29a-1206">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1206">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7b29a-1207">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1207">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7b29a-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 ( *applicationHost.config* ) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1208">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration ( *applicationHost.config* ) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7b29a-1209">" *ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-1209">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7b29a-1210">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1210">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="7b29a-1211">システムを再起動するか、コマンドシェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1211">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="7b29a-1212">IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1212">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="7b29a-1213">ホスティング バンドルをインストールするときに、IIS 内の個々のサイトを手動で停止する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1213">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="7b29a-1214">ホストされているアプリ (IIS サイト) は、IIS の再起動時に再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1214">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="7b29a-1215">アプリは、最初の要求 ([Application Initialization モジュール](#application-initialization-module-and-idle-timeout)からの要求など) を受信すると再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1215">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="7b29a-1216">ASP.NET Core では、共有フレームワーク パッケージの修正プログラムのリリースに対してロールフォワード動作が採用されています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1216">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="7b29a-1217">IIS によってホストされているアプリが IIS で再起動された場合、そのアプリで最初の要求を受け取ったときに、各自の参照されているパッケージに対する最新の修正プログラムのリリースが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1217">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="7b29a-1218">IIS が再起動されない場合は、アプリのワーカー プロセスがリサイクルされてアプリで最初の要求を受信したときに、アプリが再起動され、ロールフォワード動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1218">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="7b29a-1219">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1219">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="7b29a-1220">Visual Studio で発行する場合の Web 配置のインストール</span><span class="sxs-lookup"><span data-stu-id="7b29a-1220">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="7b29a-1221">[Web 配置](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)を使用してサーバーにアプリを展開する場合、Web 配置の最新バージョンをサーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1221">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="7b29a-1222">Web 配置をインストールするには、[Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) を使用するか、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=43717)からインストーラーを取得します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1222">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="7b29a-1223">WebPI を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1223">The preferred method is to use WebPI.</span></span> <span data-ttu-id="7b29a-1224">WebPI は、スタンドアロンのセットアップとホスティング プロバイダー向けの構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1224">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="7b29a-1225">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="7b29a-1225">Create the IIS site</span></span>

1. <span data-ttu-id="7b29a-1226">ホスト システムで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1226">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="7b29a-1227">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1227">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="7b29a-1228">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1228">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="7b29a-1229">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1229">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="7b29a-1230">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1230">Right-click the **Sites** folder.</span></span> <span data-ttu-id="7b29a-1231">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1231">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-1232">**[サイト名]** を指定し、 **[物理パス]** にはアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1232">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="7b29a-1233">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1233">Provide the **Binding** configuration and create the website by selecting **OK** :</span></span>

   ![[Web サイトの追加] のステップでサイト名、物理パス、ホスト名を指定します。](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="7b29a-1235">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1235">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="7b29a-1236">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1236">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="7b29a-1237">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1237">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="7b29a-1238">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1238">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="7b29a-1239">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1239">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="7b29a-1240">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1240">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="7b29a-1241">サーバーのノードでは、 **[アプリケーション プール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1241">Under the server's node, select **Application Pools** .</span></span>

1. <span data-ttu-id="7b29a-1242">サイトのアプリケーション プールを右クリックし、コンテキスト メニューから **[基本設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1242">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="7b29a-1243">**[アプリケーション プールの編集]** ウィンドウで、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1243">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code** :</span></span>

   ![.NET CLR バージョンとして [マネージド コードなし] を設定します。](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="7b29a-1245">ASP.NET Core は、別個のプロセスで実行され、ランタイムを管理します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1245">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="7b29a-1246">ASP.NET Core はデスクトップ CLR (.NET CLR) の読み込みに依存しません&mdash;.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動され、ワーカー プロセスでアプリがホストされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1246">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="7b29a-1247">**[.NET CLR バージョン]** の **[マネージド コードなし]** への設定は省略可能ですが、推奨されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1247">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="7b29a-1248">*ASP.NET Core 2.2 以降* : [インプロセス ホスティング モデル](#in-process-hosting-model)を使用する 64 ビット (x64) の [自己完結型展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合、32 ビット (x86) プロセス用のアプリケーション プールを無効にします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1248">*ASP.NET Core 2.2 or later* : For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="7b29a-1249">IIS マネージャー > **[アプリケーション プール]** の **[操作]** サイドバーで、 **[アプリケーション プールの既定値の設定]** または **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1249">In the **Actions** sidebar of IIS Manager > **Application Pools** , select **Set Application Pool Defaults** or **Advanced Settings** .</span></span> <span data-ttu-id="7b29a-1250">**[32 ビット アプリケーションの有効化]** を探し、値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1250">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="7b29a-1251">この設定は[アウトプロセス ホスティング](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)で展開されたアプリには影響しません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1251">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="7b29a-1252">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1252">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="7b29a-1253">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を **ApplicationPoolIdentity** から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1253">If the default identity of the app pool ( **Process Model** > **Identity** ) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="7b29a-1254">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1254">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="7b29a-1255">**Windows 認証の構成 (任意)**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1255">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="7b29a-1256">詳細については、「[Windows 認証を構成する](xref:security/authentication/windowsauth)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1256">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="7b29a-1257">アプリを配置する</span><span class="sxs-lookup"><span data-stu-id="7b29a-1257">Deploy the app</span></span>

<span data-ttu-id="7b29a-1258">「 [IIS サイトを作成する](#create-the-iis-site)」セクションで確立された IIS の **[物理パス]** フォルダーにアプリを配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1258">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="7b29a-1259">お勧めの配置方法は [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)ですが、プロジェクトの *publish* フォルダーからホスティング システムの配置フォルダーにアプリを移動するためのオプションはいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1259">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="7b29a-1260">Visual Studio からのアプリの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-1260">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="7b29a-1261">Web 配置に使用する発行プロファイルの作成方法については、「[Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」 (ASP.NET Core アプリ展開用の Visual Studio の発行プロファイル) のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1261">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="7b29a-1262">ホスティング プロバイダーから発行プロファイルまたはそれを作成するためのサポートが提供されている場合は、そのプロファイルをダウンロードし、Visual Studio の **[発行]** ダイアログを使用してインポートします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1262">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![[発行] ダイアログ ページ](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="7b29a-1264">Visual Studio 外部での Web 配置</span><span class="sxs-lookup"><span data-stu-id="7b29a-1264">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="7b29a-1265">Visual Studio の外部で、コマンド ラインから [Web 配置](/iis/publish/using-web-deploy/introduction-to-web-deploy)を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1265">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="7b29a-1266">詳細については、[Web 配置ツール](/iis/publish/using-web-deploy/use-the-web-deployment-tool)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1266">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="7b29a-1267">Web 配置の代替手段</span><span class="sxs-lookup"><span data-stu-id="7b29a-1267">Alternatives to Web Deploy</span></span>

<span data-ttu-id="7b29a-1268">手動コピー、[Xcopy](/windows-server/administration/windows-commands/xcopy)、[Robocopy](/windows-server/administration/windows-commands/robocopy)、または [PowerShell](/powershell/) など、いくつかあるうちの任意の方法を使って、ホスティング システムにアプリを移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1268">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="7b29a-1269">IIS への ASP.NET Core の展開の詳細については、「[IIS 管理者用の展開リソース](#deployment-resources-for-iis-administrators)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1269">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="7b29a-1270">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="7b29a-1270">Browse the website</span></span>

<span data-ttu-id="7b29a-1271">ホスティング システムにアプリを配置したら、アプリのパブリック エンドポイントの 1 つに要求を行います。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1271">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="7b29a-1272">次の例では、サイトは IIS の **ホスト名**`www.mysite.com` に **ポート** `80` でバインドされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1272">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="7b29a-1273">`http://www.mysite.com` に対して要求が行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1273">A request is made to `http://www.mysite.com`:</span></span>

![Microsoft Edge ブラウザーに IIS のスタートアップ ページが読み込まれています。](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="7b29a-1275">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="7b29a-1275">Locked deployment files</span></span>

<span data-ttu-id="7b29a-1276">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1276">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="7b29a-1277">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1277">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="7b29a-1278">展開でロックされているファイルをリリースするには、次の **いずれか** の方法を使用してアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1278">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-1279">Web 配置を使用し、プロジェクト ファイル内の `Microsoft.NET.Sdk.Web` を参照して停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1279">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="7b29a-1280">*app_offline.htm* ファイルは、Web アプリのディレクトリのルートに配置されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1280">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="7b29a-1281">ファイルが存在する場合、ASP.NET Core モジュールはアプリを正常にシャットダウンし、展開中に *app_offline.htm* ファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1281">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="7b29a-1282">詳細については、「[ASP.NET Core モジュール構成リファレンス](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1282">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="7b29a-1283">サーバー上の IIS マネージャーで手動によりアプリ プールを停止します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1283">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="7b29a-1284">PowerShell を使用して *app_offline.html* をドロップします (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1284">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="7b29a-1285">データの保護</span><span class="sxs-lookup"><span data-stu-id="7b29a-1285">Data protection</span></span>

<span data-ttu-id="7b29a-1286">[ASP.NET Core データ保護スタック](xref:security/data-protection/introduction)は、認証で使用されるミドルウェアを含め、いくつかの [ASP.NET Core](xref:fundamentals/middleware/index) ミドルウェアによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1286">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="7b29a-1287">データ保護 API がユーザーのコードから呼び出されない場合でも、配置スクリプトを使用するかユーザー コードで、永続的な暗号化[キー ストア](xref:security/data-protection/implementation/key-management)を作成するようにデータ保護を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1287">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="7b29a-1288">データ保護を構成しない場合、既定でキーはメモリ内に保持され、アプリが再起動すると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1288">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="7b29a-1289">キーリングがメモリに格納されている場合、アプリを再起動すると次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1289">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="7b29a-1290">すべての cookie ベースの認証トークンは無効になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1290">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="7b29a-1291">ユーザーは、次回の要求時に再度サインインする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1291">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="7b29a-1292">キーリングで保護されているデータは、いずれも復号化できなくなります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1292">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="7b29a-1293">これには、[CSRF トークン](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)と [ASP.NET Core MVC TempData cookie](xref:fundamentals/app-state#tempdata) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1293">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="7b29a-1294">キーリングを保持するために IIS でのデータ保護を構成するには、次の **いずれか** の方法を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1294">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="7b29a-1295">**データ保護のレジストリ キーを作成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1295">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="7b29a-1296">ASP.NET Core アプリで使用されるデータ保護キーは、アプリ外部のレジストリに格納されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1296">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="7b29a-1297">特定のアプリのキーを保持するには、アプリ プール用のレジストリ キーを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1297">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="7b29a-1298">非 Web ファーム IIS のスタンドアロン インストールの場合、ASP.NET Core アプリで使用するアプリ プールごとに、[データ保護の PowerShell スクリプト Provision-AutoGenKeys.ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1298">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="7b29a-1299">このスクリプトは、HKLM レジストリにレジストリ キーを作成します。そのキーは、アプリのアプリ プールのワーカー プロセス アカウントのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1299">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="7b29a-1300">キーは保存時に、DPAPI を使用して、コンピューター全体に適用するキーによって暗号化されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1300">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="7b29a-1301">Web ファームのシナリオでは、UNC パスを使用してデータ保護キー リングを格納するようにアプリを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1301">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="7b29a-1302">既定では、データ保護キーは暗号化されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1302">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="7b29a-1303">ネットワーク共有のファイルのアクセス許可が、アプリを実行する Windows アカウントに限定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1303">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="7b29a-1304">保存時のキーを保護するために、X509 証明書を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1304">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="7b29a-1305">ユーザーが証明書をアップロードできるメカニズムを検討している場合、ユーザーの信頼できる証明書ストアに証明書を配置し、ユーザーのアプリが実行されるすべてのコンピューターで証明書を利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1305">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="7b29a-1306">詳細については、「[ASP.NET Core データ保護の構成](xref:security/data-protection/configuration/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1306">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="7b29a-1307">**ユーザー プロファイルを読み込むための IIS アプリケーション プールを構成する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1307">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="7b29a-1308">この設定は、アプリ プールの **[詳細設定]** の **[プロセス モデル]** セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1308">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="7b29a-1309">**[ユーザー プロファイルの読み込み]** を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1309">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="7b29a-1310">`True` に設定すると、キーはユーザー プロファイル ディレクトリに格納され、ユーザー アカウントに固有のキーと共にデータ保護 API を使って保護されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1310">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="7b29a-1311">キーは *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* フォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1311">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="7b29a-1312">アプリ プールの [setProfileEnvironment 属性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)も有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1312">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="7b29a-1313">`setProfileEnvironment` の既定値は `true`です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1313">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="7b29a-1314">一部のシナリオ (たとえば、Windows OS) では、`setProfileEnvironment` は `false` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1314">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="7b29a-1315">キーが期待どおりにユーザー プロファイル ディレクトリに格納されていない場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1315">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="7b29a-1316">*%windir%/system32/inetsrv/config* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1316">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="7b29a-1317">*applicationHost.config* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1317">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="7b29a-1318">`<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 要素を探します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1318">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="7b29a-1319">`setProfileEnvironment` 属性 (その規定値は `true` です) が存在しないことを確認するか、属性の値を明示的に `true` に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1319">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="7b29a-1320">**ファイル システムをキー リング ストアとして使用する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1320">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="7b29a-1321">[ファイル システムをキー リング ストアとして使用](xref:security/data-protection/configuration/overview)するようにアプリ コードを調整します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1321">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="7b29a-1322">X509 証明書を使用してキー リングを保護し、その証明書が信頼された証明書であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1322">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="7b29a-1323">証明書が自己署名されている場合は、信頼されたルート ストアにその証明書を配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1323">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="7b29a-1324">Web ファームで IIS を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1324">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="7b29a-1325">すべてのコンピューターがアクセスできるファイル共有を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1325">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="7b29a-1326">X509 証明書を各コンピューターに配置します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1326">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="7b29a-1327">[コード内にデータ保護](xref:security/data-protection/configuration/overview)を構成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1327">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="7b29a-1328">**コンピューター全体に適用するデータ保護ポリシーを設定する**</span><span class="sxs-lookup"><span data-stu-id="7b29a-1328">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="7b29a-1329">データ保護システムでは、データ保護 API を使用するすべてのアプリに対して、[コンピューター全体に適用する既定のポリシー](xref:security/data-protection/configuration/machine-wide-policy)を設定するためのサポートは限定的です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1329">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="7b29a-1330">詳細については、「<xref:security/data-protection/introduction>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1330">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="7b29a-1331">仮想ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-1331">Virtual Directories</span></span>

<span data-ttu-id="7b29a-1332">ASP.NET Core アプリでは [IIS 仮想ディレクトリ](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1332">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="7b29a-1333">アプリは[サブアプリケーション](#sub-applications)としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1333">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="7b29a-1334">サブアプリケーション</span><span class="sxs-lookup"><span data-stu-id="7b29a-1334">Sub-applications</span></span>

<span data-ttu-id="7b29a-1335">ASP.NET Core アプリは [IIS サブアプリケーション (サブアプリ)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) としてホスティングできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1335">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="7b29a-1336">サブアプリのパスは、ルート アプリの URL の一部になります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1336">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="7b29a-1337">サブアプリに、ハンドラーとして ASP.NET Core モジュールを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1337">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="7b29a-1338">このモジュールをサブアプリの *web.config* ファイルにハンドラーとして追加すると、サブアプリを閲覧しようとすると、エラーのある構成ファイルを参照する *500.19 内部サーバー エラー* が返されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1338">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="7b29a-1339">次の例は、ASP.NET Core サブアプリの発行済み *web.config* ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1339">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

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

<span data-ttu-id="7b29a-1340">ASP.NET Core アプリの下に ASP.NET Core 以外のサブアプリをホスティングする場合は、サブアプリの *web.config* ファイルにある継承されたハンドラーを明示的に削除します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1340">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

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

<span data-ttu-id="7b29a-1341">サブアプリ内にある静的資産のリンクでは、チルダとスラッシュの表記 (`~/`) を使う必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1341">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="7b29a-1342">チルダとスラッシュの表記により[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)がトリガーされ、作成される相対リンクにサブアプリのパスベースが付加されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1342">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="7b29a-1343">`/subapp_path` にあるサブアプリの場合、`src="~/image.png"` を使ってリンクされる画像は `src="/subapp_path/image.png"` として作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1343">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="7b29a-1344">ルート アプリの静的ファイル ミドルウェアでは、静的ファイル要求は処理されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1344">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="7b29a-1345">この要求は、サブアプリの静的ファイル ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1345">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="7b29a-1346">静的資産の `src` 属性が絶対パス (たとえば `src="/image.png"`) に設定されている場合、リンクはサブアプリのパスベースなしで作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1346">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="7b29a-1347">ルート アプリの静的ファイル ミドルウェアでは、ルート アプリの [Web ルート](xref:fundamentals/index#web-root)から資産を提供しようとしますが、ルート アプリから静的資産を利用できる場合を除いて *404 - Not Found* 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1347">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="7b29a-1348">ある ASP.NET Core アプリを別の ASP.NET Core アプリの下でサブアプリとしてホスティングするには:</span><span class="sxs-lookup"><span data-stu-id="7b29a-1348">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="7b29a-1349">サブアプリ用のアプリ プールを確立します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1349">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="7b29a-1350">デスクトップ CLR (.NET CLR) ではなく、.NET Core 用の Core 共通言語ランタイム (CoreCLR) が起動されてワーカー プロセスでアプリがホストされるため、 **[.NET CLR バージョン]** を **[マネージド コードなし]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1350">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="7b29a-1351">ルート サイトを IIS マネージャーに追加し、サブアプリをルート サイトの下のフォルダー内に置きます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1351">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="7b29a-1352">IIS マネージャーでサブアプリのフォルダーを右クリックし、 **[アプリケーションへの変換]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1352">Right-click the sub-app folder in IIS Manager and select **Convert to Application** .</span></span>

1. <span data-ttu-id="7b29a-1353">**[アプリケーションの追加]** ダイアログ ボックスで、 **[アプリケーション プール]** に対して **[選択]** ボタンを使い、作成したアプリケーション プールをサブアプリ用に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1353">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="7b29a-1354">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1354">Select **OK** .</span></span>

<span data-ttu-id="7b29a-1355">サブアプリに対して個別のアプリ プールを割り当てることは、インプロセス ホスティング モデルを使用する場合必須となります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1355">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="7b29a-1356">インプロセス ホスティング モデルと ASP.NET Core モジュールの構成について詳しくは、<xref:host-and-deploy/aspnet-core-module> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1356">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="7b29a-1357">web.config による IIS の構成</span><span class="sxs-lookup"><span data-stu-id="7b29a-1357">Configuration of IIS with web.config</span></span>

<span data-ttu-id="7b29a-1358">IIS の構成は ASP.NET Core モジュールを使用した ASP.NET Core アプリで機能する IIS シナリオの *web.config* の `<system.webServer>` セクションによる影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1358">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="7b29a-1359">たとえば、IIS の構成は動的な圧縮で機能します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1359">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="7b29a-1360">IIS が動的な圧縮を使用するようにサーバー レベルで構成されている場合、アプリの *web.config* ファイルの `<urlCompression>` 要素を使用すると、それを ASP.NET Core アプリに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1360">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="7b29a-1361">詳細については、次のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1361">For more information, see the following topics:</span></span>

* [<span data-ttu-id="7b29a-1362">\<system.webServer> の構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="7b29a-1362">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="7b29a-1363">分離されたアプリ プール (IIS 10.0 以降でサポートされています) で実行する個別アプリに対して環境変数を設定するには、IIS のリファレンス ドキュメントで、 [環境変数\<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) のトピックにある " *AppCmd.exe コマンド* " のセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1363">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="7b29a-1364">web.config の構成のセクション</span><span class="sxs-lookup"><span data-stu-id="7b29a-1364">Configuration sections of web.config</span></span>

<span data-ttu-id="7b29a-1365">*web.config* の ASP.NET 4.x アプリの構成セクションは、ASP.NET Core アプリの構成では使用されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1365">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="7b29a-1366">ASP.NET Core アプリは、他の構成プロバイダーを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1366">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="7b29a-1367">詳細については、[構成](xref:fundamentals/configuration/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1367">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="7b29a-1368">アプリケーション プール</span><span class="sxs-lookup"><span data-stu-id="7b29a-1368">Application Pools</span></span>

<span data-ttu-id="7b29a-1369">サーバーで複数の Web サイトをホストする場合は、アプリをそれぞれ専用のアプリ プールで実行して、アプリを相互に分離することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1369">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="7b29a-1370">IIS の **[Web サイトの追加]** ダイアログはこの構成の既定です。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1370">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="7b29a-1371">**[サイト名]** を指定すると、入力したテキストが自動的に **[アプリケーション プール]** テキストボックスに設定されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1371">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="7b29a-1372">サイトが追加されるときに、そのサイト名を使用して新しいアプリ プールが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1372">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="7b29a-1373">アプリケーション プール Identity</span><span class="sxs-lookup"><span data-stu-id="7b29a-1373">Application Pool Identity</span></span>

<span data-ttu-id="7b29a-1374">アプリ プール ID アカウントを使用すると、ドメインやローカル アカウントを作成して管理する必要なく、一意のアカウントでアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1374">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="7b29a-1375">IIS 8.0 以降の IIS 管理者ワーカー プロセス (WAS) は、新しいアプリ プールの名前で仮想アカウントを作成し、既定によってアプリ プールのワーカー プロセスをこのアカウントで実行します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1375">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="7b29a-1376">IIS 管理コンソールにあるアプリ プールの **[詳細設定]** で、 **ApplicationPoolIdentity** が使用されるように **Identity** を確実に設定します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1376">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity** :</span></span>

![アプリケーション プールの [詳細設定] ダイアログ](index/_static/apppool-identity.png)

<span data-ttu-id="7b29a-1378">IIS 管理プロセスは、Windows セキュリティ システムでのアプリ プールの名前を使用して、セキュリティで保護された識別子を作成します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1378">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="7b29a-1379">この ID を使用してリソースを保護することができます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1379">Resources can be secured using this identity.</span></span> <span data-ttu-id="7b29a-1380">ただし、この ID は実際のユーザー アカウントではなく、Windows ユーザーの管理コンソールに表示されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1380">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="7b29a-1381">アプリに対する IIS ワーカー プロセスのアクセス許可を昇格させる必要がある場合は、アプリを含むディレクトリのアクセス制御リスト (ACL) を変更します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1381">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="7b29a-1382">エクスプローラーを開き、そのディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1382">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="7b29a-1383">そのディレクトリを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1383">Right-click on the directory and select **Properties** .</span></span>

1. <span data-ttu-id="7b29a-1384">**[セキュリティ]** タブの **[編集]** ボタンを選択し、 **[追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1384">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="7b29a-1385">**[場所]** ボタンを選択し、システムが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1385">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="7b29a-1386">**[選択するオブジェクト名を入力してください]** 領域に、「 **IIS AppPool\\<app_pool_name>** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1386">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="7b29a-1387">**[名前の確認]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1387">Select the **Check Names** button.</span></span> <span data-ttu-id="7b29a-1388">*DefaultAppPool* で、 **IIS AppPool\DefaultAppPool** を使用して名前を確認します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1388">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool** .</span></span> <span data-ttu-id="7b29a-1389">**[名前の確認]** ボタンが選択されているときには、 **DefaultAppPool** の値が、オブジェクト名領域に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1389">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="7b29a-1390">オブジェクト名の領域に直接アプリケーション プール名を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1390">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="7b29a-1391">オブジェクト名を確認するときには、 **IIS AppPool\\<app_pool_name>** の形式を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1391">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択する前に、オブジェクト名領域で "DefaultAppPool" のアプリ プール名が "IIS AppPool\" に適用されます。](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="7b29a-1393">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1393">Select **OK** .</span></span>

   ![アプリ フォルダーのユーザーまたはグループのダイアログを選択します。[名前の確認] を選択した後に、オブジェクト名 "DefaultAppPool" がオブジェクト名領域に表示されます。](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="7b29a-1395">読み取り &amp; 実行アクセス許可は、既定で付与される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1395">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="7b29a-1396">必要に応じて、追加のアクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1396">Provide additional permissions as needed.</span></span>

<span data-ttu-id="7b29a-1397">**ICACLS** ツールを使用してコマンド プロンプトでアクセス許可を付与することもできます。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1397">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="7b29a-1398">たとえば、 *DefaultAppPool* を使用する場合、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1398">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="7b29a-1399">詳細については、「[icacls](/windows-server/administration/windows-commands/icacls)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1399">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="7b29a-1400">HTTP/2 のサポート</span><span class="sxs-lookup"><span data-stu-id="7b29a-1400">HTTP/2 support</span></span>

<span data-ttu-id="7b29a-1401">次の基本要件を満たすアウトプロセスの展開には、[HTTP/2](https://httpwg.org/specs/rfc7540.html) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1401">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="7b29a-1402">Windows Server 2016/Windows 10 以降、IIS 10 以降</span><span class="sxs-lookup"><span data-stu-id="7b29a-1402">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="7b29a-1403">一般向けのエッジ サーバーでは HTTP/2 を使用しますが、[Kestrel サーバー](xref:fundamentals/servers/kestrel)へのリバース プロキシ接続では HTTP/1.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1403">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="7b29a-1404">ターゲット フレームワーク:HTTP/2 接続は IIS によって完全に処理されるため、アウトプロセスの展開には適用できません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1404">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="7b29a-1405">TLS 1.2 以降の接続</span><span class="sxs-lookup"><span data-stu-id="7b29a-1405">TLS 1.2 or later connection</span></span>

<span data-ttu-id="7b29a-1406">Http/2 接続が確立されると、[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) が `HTTP/1.1` を報告します。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1406">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="7b29a-1407">HTTP/2 は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1407">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="7b29a-1408">HTTP/2 接続が確立されない場合、接続は HTTP/1.1 にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1408">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="7b29a-1409">IIS 展開での HTTP/2 構成の詳細については、[IIS での HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis) に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1409">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="7b29a-1410">CORS プレフライト要求</span><span class="sxs-lookup"><span data-stu-id="7b29a-1410">CORS preflight requests</span></span>

<span data-ttu-id="7b29a-1411">" *このセクションは、.NET Framework をターゲットにした ASP.NET Core アプリにのみ適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="7b29a-1411">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="7b29a-1412">.NET Framework をターゲットにした ASP.NET Core アプリの場合、IIS では既定で OPTIONS 要求がアプリに渡されません。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1412">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="7b29a-1413">OPTIONS 要求を渡すように *web.config* でアプリの IIS のハンドラーを構成する方法については、 [ASP.NET Web API 2 でのクロスオリジン要求の有効化:CORS のしくみ](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7b29a-1413">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="7b29a-1414">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="7b29a-1414">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="7b29a-1415">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="7b29a-1415">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="7b29a-1416">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="7b29a-1416">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="7b29a-1417">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="7b29a-1417">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="7b29a-1418">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7b29a-1418">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="7b29a-1419">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="7b29a-1419">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="7b29a-1420">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="7b29a-1420">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="7b29a-1421">IIS での HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7b29a-1421">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
