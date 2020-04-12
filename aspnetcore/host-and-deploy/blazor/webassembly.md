---
title: ASP.NET Core Blazor WebAssembly をホストしてデプロイする
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751120"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="287ef-103">ASP.NET Core Blazor WebAssembly をホストしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="287ef-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="287ef-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="287ef-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="287ef-105">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="287ef-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="287ef-106">Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="287ef-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="287ef-107">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="287ef-108">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="287ef-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="287ef-109">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="287ef-110">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="287ef-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="287ef-111">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="287ef-112">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="287ef-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="287ef-113">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="287ef-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="287ef-114">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="287ef-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="287ef-115">2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="287ef-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="287ef-116">*Main.razor* &ndash; アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="287ef-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="287ef-117">*About.razor* &ndash; `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="287ef-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="287ef-118">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="287ef-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="287ef-119">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-119">The browser makes a request.</span></span>
1. <span data-ttu-id="287ef-120">既定のページ (通常は *index.html*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="287ef-121">*index.html* によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="287ef-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="287ef-122"> のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="287ef-123">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="287ef-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="287ef-124">" *Blazor WebAssembly アプリ*" 内にある内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="287ef-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="287ef-125">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-125">The router handles the requests internally.</span></span>

<span data-ttu-id="287ef-126">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="287ef-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="287ef-127">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="287ef-128">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="287ef-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="287ef-129">*index.html* が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="287ef-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="287ef-130">IIS サーバーに展開する場合は、アプリの発行される *web.config* ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="287ef-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="287ef-131">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="287ef-132">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="287ef-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="287ef-133">"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="287ef-134">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="287ef-135">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-135">The two apps are deployed together.</span></span> <span data-ttu-id="287ef-136">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="287ef-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="287ef-137">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **[ホスト]** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="287ef-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="287ef-138">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="287ef-139">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="287ef-140">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="287ef-140">Standalone deployment</span></span>

<span data-ttu-id="287ef-141">"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="287ef-142">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="287ef-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="287ef-143">スタンドアロン展開のアセットは " */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="287ef-144">IIS</span><span class="sxs-lookup"><span data-stu-id="287ef-144">IIS</span></span>

<span data-ttu-id="287ef-145">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="287ef-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="287ef-146">Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="287ef-147">発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="287ef-148">*publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="287ef-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="287ef-149">web.config</span><span class="sxs-lookup"><span data-stu-id="287ef-149">web.config</span></span>

<span data-ttu-id="287ef-150">Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="287ef-151">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="287ef-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="287ef-152">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="287ef-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="287ef-153">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="287ef-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="287ef-154">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="287ef-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="287ef-155">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="287ef-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="287ef-156">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="287ef-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="287ef-157">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="287ef-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="287ef-158">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="287ef-159">アプリの静的なアセットが存在するサブディレクトリ ("*wwwroot/{要求されたパス}* ") が提供されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="287ef-160">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ("*wwwroot/index.html*") にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="287ef-161">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="287ef-161">Use a custom web.config</span></span>

<span data-ttu-id="287ef-162">カスタム *web.config* ファイルを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="287ef-162">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="287ef-163">カスタム *web.config* ファイルをプロジェクト フォルダーのルートに配置します。</span><span class="sxs-lookup"><span data-stu-id="287ef-163">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="287ef-164">次のターゲットをプロジェクト ファイル ( *.csproj*) に追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-164">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="287ef-165">`true` に設定されている MSBuild プロパティ `<IsWebConfigTransformDisabled>` の使用は、[IIS に展開された ASP.NET Core アプリの場合と同様に](xref:host-and-deploy/iis/index#webconfig-file)、Blazor WebAssembly アプリではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="287ef-165">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="287ef-166">詳細については、「[Copy target required to provide custom Blazor WASM web.config](https://github.com/dotnet/aspnetcore/issues/20569)」(カスタム Blazor WASM web.config を提供するために必要なターゲットのコピー) (dotnet/aspnetcore #20569) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-166">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="287ef-167">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="287ef-167">Install the URL Rewrite Module</span></span>

<span data-ttu-id="287ef-168">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="287ef-168">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="287ef-169">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="287ef-169">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="287ef-170">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="287ef-170">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="287ef-171">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="287ef-171">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="287ef-172">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="287ef-172">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="287ef-173">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="287ef-173">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="287ef-174">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="287ef-174">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="287ef-175">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="287ef-175">Copy the installer to the server.</span></span> <span data-ttu-id="287ef-176">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="287ef-176">Run the installer.</span></span> <span data-ttu-id="287ef-177">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="287ef-177">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="287ef-178">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="287ef-178">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="287ef-179">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="287ef-179">Configure the website</span></span>

<span data-ttu-id="287ef-180">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-180">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="287ef-181">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="287ef-181">The folder contains:</span></span>

* <span data-ttu-id="287ef-182">*web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="287ef-182">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="287ef-183">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="287ef-183">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="287ef-184">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="287ef-184">Host as an IIS sub-app</span></span>

<span data-ttu-id="287ef-185">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="287ef-185">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="287ef-186">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="287ef-186">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="287ef-187">Blazor アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="287ef-187">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="287ef-188">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="287ef-188">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="287ef-189">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:host-and-deploy/blazor/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="287ef-189">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="287ef-190">IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="287ef-191">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="287ef-191">Troubleshooting</span></span>

<span data-ttu-id="287ef-192">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="287ef-192">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="287ef-193">モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="287ef-193">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="287ef-194">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="287ef-194">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="287ef-195">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-195">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="287ef-196">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="287ef-196">Azure Storage</span></span>

<span data-ttu-id="287ef-197">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="287ef-197">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="287ef-198">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="287ef-198">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="287ef-199">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="287ef-199">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="287ef-200">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-200">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="287ef-201">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-201">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="287ef-202">Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="287ef-202">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="287ef-203">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-203">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="287ef-204">*index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-204">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="287ef-205">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-205">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="287ef-206">Nginx</span><span class="sxs-lookup"><span data-stu-id="287ef-206">Nginx</span></span>

<span data-ttu-id="287ef-207">以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。</span><span class="sxs-lookup"><span data-stu-id="287ef-207">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="287ef-208">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-208">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="287ef-209">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="287ef-209">Nginx in Docker</span></span>

<span data-ttu-id="287ef-210">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="287ef-210">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="287ef-211">*nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="287ef-211">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="287ef-212">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-212">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="287ef-213">Apache</span><span class="sxs-lookup"><span data-stu-id="287ef-213">Apache</span></span>

<span data-ttu-id="287ef-214">Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:</span><span class="sxs-lookup"><span data-stu-id="287ef-214">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="287ef-215">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="287ef-215">Create the Apache configuration file.</span></span> <span data-ttu-id="287ef-216">次の例は、簡略化された構成ファイル (*blazorapp.config*) です。</span><span class="sxs-lookup"><span data-stu-id="287ef-216">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="287ef-217">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="287ef-217">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="287ef-218">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="287ef-218">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="287ef-219">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="287ef-219">Restart the Apache service.</span></span>

<span data-ttu-id="287ef-220">詳細については、「[mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)」と「[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-220">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="287ef-221">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="287ef-221">GitHub Pages</span></span>

<span data-ttu-id="287ef-222">URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-222">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="287ef-223">コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-223">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="287ef-224">コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-224">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="287ef-225">組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。</span><span class="sxs-lookup"><span data-stu-id="287ef-225">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="287ef-226">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-226">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="287ef-227">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="287ef-227">Host configuration values</span></span>

<span data-ttu-id="287ef-228">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="287ef-228">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="287ef-229">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="287ef-229">Content root</span></span>

<span data-ttu-id="287ef-230">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="287ef-230">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="287ef-231">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="287ef-231">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="287ef-232">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="287ef-232">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="287ef-233">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="287ef-233">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="287ef-234">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-234">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="287ef-235">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-235">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="287ef-236">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-236">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="287ef-237">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-237">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="287ef-238">パス ベース</span><span class="sxs-lookup"><span data-stu-id="287ef-238">Path base</span></span>

<span data-ttu-id="287ef-239">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="287ef-239">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="287ef-240">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="287ef-240">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="287ef-241">詳細については、「[アプリのベースパス](xref:host-and-deploy/blazor/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-241">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="287ef-242">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="287ef-242">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="287ef-243">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="287ef-243">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="287ef-244">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="287ef-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="287ef-245">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="287ef-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="287ef-246">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="287ef-247">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-247">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="287ef-248">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="287ef-249">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="287ef-250">URL</span><span class="sxs-lookup"><span data-stu-id="287ef-250">URLs</span></span>

<span data-ttu-id="287ef-251">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-251">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="287ef-252">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="287ef-252">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="287ef-253">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="287ef-253">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="287ef-254">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="287ef-254">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="287ef-255">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-255">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="287ef-256">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="287ef-256">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="287ef-257">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-257">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="287ef-258">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="287ef-258">Configure the Linker</span></span>

Blazor<span data-ttu-id="287ef-259"> では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="287ef-259"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="287ef-260">詳細については、「<xref:host-and-deploy/blazor/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="287ef-260">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
