---
title: ASP.NET Core Blazor WebAssembly をホストしてデプロイする
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: e136a401beffe9cc7e29906b3631ab3f068b30fd
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967598"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="30ab0-103">ASP.NET Core Blazor WebAssembly をホストして展開する</span><span class="sxs-lookup"><span data-stu-id="30ab0-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="30ab0-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="30ab0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="30ab0-105">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="30ab0-106">Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="30ab0-107">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="30ab0-108">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="30ab0-109">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="30ab0-110">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="30ab0-111">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="30ab0-112">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法などについて含まれています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="30ab0-113">Brotli 事前圧縮</span><span class="sxs-lookup"><span data-stu-id="30ab0-113">Brotli precompression</span></span>

<span data-ttu-id="30ab0-114">Blazor WebAssembly アプリが発行されると、最上位レベルの [Brotli 圧縮アルゴリズム](https://tools.ietf.org/html/rfc7932)を使用して、出力が事前圧縮されます。このことで、アプリのサイズが縮小され、実行時の圧縮が不要になります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="30ab0-115">IIS の *web.config* の圧縮構成については、[「IIS」の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="30ab0-116">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="30ab0-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="30ab0-117">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="30ab0-118">2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="30ab0-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="30ab0-119">*Main.razor* &ndash; アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="30ab0-120">*About.razor* &ndash; `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="30ab0-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="30ab0-121">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="30ab0-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="30ab0-122">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-122">The browser makes a request.</span></span>
1. <span data-ttu-id="30ab0-123">既定のページ (通常は *index.html*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="30ab0-124">*index.html* によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="30ab0-125">Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="30ab0-126">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="30ab0-127">*Blazor WebAssembly アプリ内にある*内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="30ab0-128">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-128">The router handles the requests internally.</span></span>

<span data-ttu-id="30ab0-129">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="30ab0-130">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="30ab0-131">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="30ab0-132">*index.html* が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="30ab0-133">IIS サーバーに展開する場合は、アプリの発行される *web.config* ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="30ab0-134">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="30ab0-135">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="30ab0-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="30ab0-136">*ホストされた展開*により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="30ab0-137">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="30ab0-138">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-138">The two apps are deployed together.</span></span> <span data-ttu-id="30ab0-139">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="30ab0-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="30ab0-140">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **[ホスト]** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="30ab0-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="30ab0-141">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="30ab0-142">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="30ab0-143">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="30ab0-143">Standalone deployment</span></span>

<span data-ttu-id="30ab0-144">*スタンドアロン展開*により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="30ab0-145">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="30ab0-146">スタンドアロン展開のアセットは " */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="30ab0-147">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="30ab0-147">Azure App Service</span></span>

<span data-ttu-id="30ab0-148">Blazor WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Services にデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-148">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="30ab0-149">スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-149">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="30ab0-150">現時点では、アプリをホストする Linux サーバー イメージは使用できません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-150">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="30ab0-151">このシナリオを可能にするための取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="30ab0-151">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="30ab0-152">IIS</span><span class="sxs-lookup"><span data-stu-id="30ab0-152">IIS</span></span>

<span data-ttu-id="30ab0-153">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-153">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="30ab0-154">Blazor をホストするよう IIS を構成する方法については、「[Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」 (IIS で静的 Web サイトを構築する) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-154">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="30ab0-155">発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-155">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="30ab0-156">*publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-156">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="30ab0-157">web.config</span><span class="sxs-lookup"><span data-stu-id="30ab0-157">web.config</span></span>

<span data-ttu-id="30ab0-158">Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-158">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="30ab0-159">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-159">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="30ab0-160">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="30ab0-160">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="30ab0-161">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="30ab0-161">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="30ab0-162">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="30ab0-162">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="30ab0-163">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="30ab0-163">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="30ab0-164">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="30ab0-164">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="30ab0-165">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-165">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="30ab0-166">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-166">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="30ab0-167">アプリの静的なアセットが存在するサブディレクトリ ("*wwwroot/{要求されたパス}* ") が提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-167">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="30ab0-168">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ("*wwwroot/index.html*") にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-168">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="30ab0-169">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="30ab0-169">Use a custom web.config</span></span>

<span data-ttu-id="30ab0-170">カスタムの *web.config* ファイルを使用するには、カスタムの *web.config* ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-170">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="30ab0-171">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="30ab0-171">Install the URL Rewrite Module</span></span>

<span data-ttu-id="30ab0-172">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-172">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="30ab0-173">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-173">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="30ab0-174">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-174">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="30ab0-175">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-175">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="30ab0-176">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-176">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="30ab0-177">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-177">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="30ab0-178">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-178">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="30ab0-179">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-179">Copy the installer to the server.</span></span> <span data-ttu-id="30ab0-180">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-180">Run the installer.</span></span> <span data-ttu-id="30ab0-181">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-181">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="30ab0-182">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-182">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="30ab0-183">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="30ab0-183">Configure the website</span></span>

<span data-ttu-id="30ab0-184">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-184">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="30ab0-185">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-185">The folder contains:</span></span>

* <span data-ttu-id="30ab0-186">*web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-186">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="30ab0-187">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="30ab0-187">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="30ab0-188">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="30ab0-188">Host as an IIS sub-app</span></span>

<span data-ttu-id="30ab0-189">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-189">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="30ab0-190">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-190">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="30ab0-191">Blazor アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-191">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="30ab0-192">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-192">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="30ab0-193">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:host-and-deploy/blazor/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-193">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="30ab0-194">IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-194">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="30ab0-195">Brotli と Gzip の圧縮</span><span class="sxs-lookup"><span data-stu-id="30ab0-195">Brotli and Gzip compression</span></span>

<span data-ttu-id="30ab0-196">*web.config* を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-196">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="30ab0-197">構成の例については、[web.config](webassembly/_samples/web.config?raw=true) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-197">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="30ab0-198">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="30ab0-198">Troubleshooting</span></span>

<span data-ttu-id="30ab0-199">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-199">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="30ab0-200">モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-200">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="30ab0-201">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-201">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="30ab0-202">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-202">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="30ab0-203">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="30ab0-203">Azure Storage</span></span>

<span data-ttu-id="30ab0-204">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-204">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="30ab0-205">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-205">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="30ab0-206">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="30ab0-206">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="30ab0-207">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-207">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="30ab0-208">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-208">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="30ab0-209">Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="30ab0-209">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="30ab0-210">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-210">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="30ab0-211">*index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-211">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="30ab0-212">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-212">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="30ab0-213">Nginx</span><span class="sxs-lookup"><span data-stu-id="30ab0-213">Nginx</span></span>

<span data-ttu-id="30ab0-214">以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-214">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="30ab0-215">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-215">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="30ab0-216">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="30ab0-216">Nginx in Docker</span></span>

<span data-ttu-id="30ab0-217">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="30ab0-217">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="30ab0-218">*nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-218">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="30ab0-219">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-219">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="30ab0-220">Apache</span><span class="sxs-lookup"><span data-stu-id="30ab0-220">Apache</span></span>

<span data-ttu-id="30ab0-221">Blazor WebAssembly アプリを CentOS 7 以降に展開するには:</span><span class="sxs-lookup"><span data-stu-id="30ab0-221">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="30ab0-222">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-222">Create the Apache configuration file.</span></span> <span data-ttu-id="30ab0-223">次の例は、簡略化された構成ファイル (*blazorapp.config*) です。</span><span class="sxs-lookup"><span data-stu-id="30ab0-223">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="30ab0-224">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-224">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="30ab0-225">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-225">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="30ab0-226">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-226">Restart the Apache service.</span></span>

<span data-ttu-id="30ab0-227">詳細については、「[mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)」と「[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-227">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="30ab0-228">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="30ab0-228">GitHub Pages</span></span>

<span data-ttu-id="30ab0-229">URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-229">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="30ab0-230">コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-230">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="30ab0-231">コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-231">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="30ab0-232">組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-232">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="30ab0-233">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-233">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="30ab0-234">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="30ab0-234">Host configuration values</span></span>

<span data-ttu-id="30ab0-235">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-235">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="30ab0-236">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="30ab0-236">Content root</span></span>

<span data-ttu-id="30ab0-237">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="30ab0-237">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="30ab0-238">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-238">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="30ab0-239">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-239">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="30ab0-240">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-240">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="30ab0-241">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-241">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="30ab0-242">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-242">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="30ab0-243">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-243">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="30ab0-244">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-244">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="30ab0-245">パス ベース</span><span class="sxs-lookup"><span data-stu-id="30ab0-245">Path base</span></span>

<span data-ttu-id="30ab0-246">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="30ab0-246">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="30ab0-247">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="30ab0-247">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="30ab0-248">詳細については、「[アプリのベースパス](xref:host-and-deploy/blazor/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-248">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="30ab0-249">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-249">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="30ab0-250">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="30ab0-250">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="30ab0-251">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-251">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="30ab0-252">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-252">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="30ab0-253">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-253">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="30ab0-254">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-254">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="30ab0-255">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-255">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="30ab0-256">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-256">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="30ab0-257">URL</span><span class="sxs-lookup"><span data-stu-id="30ab0-257">URLs</span></span>

<span data-ttu-id="30ab0-258">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-258">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="30ab0-259">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-259">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="30ab0-260">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-260">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="30ab0-261">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-261">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="30ab0-262">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-262">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="30ab0-263">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-263">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="30ab0-264">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-264">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="30ab0-265">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="30ab0-265">Configure the Linker</span></span>

<span data-ttu-id="30ab0-266">Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-266">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="30ab0-267">詳細については、「<xref:host-and-deploy/blazor/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-267">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="30ab0-268">カスタム ブート リソースの読み込み</span><span class="sxs-lookup"><span data-stu-id="30ab0-268">Custom boot resource loading</span></span>

<span data-ttu-id="30ab0-269">Blazor WebAssembly を `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-269">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="30ab0-270">次のシナリオで `loadBootResource` を使用します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-270">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="30ab0-271">ユーザーが、タイムゾーン データや *dotnet.wasm* などの静的なリソースを CDN から読み込むことができるようにする。</span><span class="sxs-lookup"><span data-stu-id="30ab0-271">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="30ab0-272">HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。</span><span class="sxs-lookup"><span data-stu-id="30ab0-272">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="30ab0-273">各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="30ab0-273">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="30ab0-274">`loadBootResource` パラメーターは次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-274">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="30ab0-275">パラメーター</span><span class="sxs-lookup"><span data-stu-id="30ab0-275">Parameter</span></span>    | <span data-ttu-id="30ab0-276">説明</span><span class="sxs-lookup"><span data-stu-id="30ab0-276">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="30ab0-277">リソースの型。</span><span class="sxs-lookup"><span data-stu-id="30ab0-277">The type of the resource.</span></span> <span data-ttu-id="30ab0-278">許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="30ab0-278">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="30ab0-279">リソースの名前。</span><span class="sxs-lookup"><span data-stu-id="30ab0-279">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="30ab0-280">リソースの相対 URI または絶対 URI。</span><span class="sxs-lookup"><span data-stu-id="30ab0-280">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="30ab0-281">応答で予想されるコンテンツを表す整合性文字列。</span><span class="sxs-lookup"><span data-stu-id="30ab0-281">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="30ab0-282">`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-282">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="30ab0-283">URI 文字列。</span><span class="sxs-lookup"><span data-stu-id="30ab0-283">URI string.</span></span> <span data-ttu-id="30ab0-284">次の例 (*wwwroot/index.html*) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-284">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="30ab0-285">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="30ab0-285">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="30ab0-286">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="30ab0-286">*dotnet.wasm*</span></span>
  * <span data-ttu-id="30ab0-287">タイムゾーン データ</span><span class="sxs-lookup"><span data-stu-id="30ab0-287">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="30ab0-288">`Promise<Response>`。</span><span class="sxs-lookup"><span data-stu-id="30ab0-288">`Promise<Response>`.</span></span> <span data-ttu-id="30ab0-289">ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-289">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="30ab0-290">次の例 (*wwwroot/index.html*) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-290">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="30ab0-291">`null`/`undefined`。既定の読み込み動作になります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-291">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="30ab0-292">外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-292">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="30ab0-293">通常、既定では、必要なヘッダーが CDN によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-293">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="30ab0-294">カスタム動作の型のみ指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="30ab0-294">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="30ab0-295">`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-295">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="30ab0-296">DLL ファイルのファイル名拡張子を変更する</span><span class="sxs-lookup"><span data-stu-id="30ab0-296">Change the filename extension of DLL files</span></span>

<span data-ttu-id="30ab0-297">アプリで発行されている " *.dll*" ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-297">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="30ab0-298">アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように " *.dll*" ファイルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-298">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="30ab0-299">アプリの発行された出力の "*wwwroot*" ディレクトリ内の " *.dll*" ファイルをターゲットにします (たとえば、" *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*")。</span><span class="sxs-lookup"><span data-stu-id="30ab0-299">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="30ab0-300">次の例では、" *.dll*" ファイルの名前が " *.bin*" ファイル拡張子を使用するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-300">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="30ab0-301">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="30ab0-301">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="30ab0-302">Linux または macOS の場合:</span><span class="sxs-lookup"><span data-stu-id="30ab0-302">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```
   
<span data-ttu-id="30ab0-303">" *.bin*" とは異なるファイル拡張子を使用するには、前のコマンドで " *.bin*" を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-303">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="30ab0-304">圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルに対処するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-304">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="30ab0-305">圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-305">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="30ab0-306">このアプローチでは、圧縮は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-306">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="30ab0-307">更新された "*blazor.boot.json*" ファイルを再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="30ab0-307">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="30ab0-308">次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="30ab0-308">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="30ab0-309">"*ChangeDLLExtensions.ps1:* :"</span><span class="sxs-lookup"><span data-stu-id="30ab0-309">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="30ab0-310">プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="30ab0-310">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="30ab0-311">フィードバックを提供するには、[aspnetcore/issue #5477](https://github.com/dotnet/aspnetcore/issues/5477) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="30ab0-311">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
