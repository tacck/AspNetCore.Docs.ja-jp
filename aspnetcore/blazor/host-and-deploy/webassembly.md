---
title: ASP.NET Core Blazor WebAssembly をホストしてデプロイする
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 7e0263200ebb9ce60f7234af3cbb18c5aeaa3e09
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243526"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="1fc6f-103">ASP.NET Core Blazor WebAssembly をホストしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="1fc6f-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="1fc6f-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="1fc6f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="1fc6f-105">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="1fc6f-106">Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="1fc6f-107">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="1fc6f-108">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="1fc6f-109">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="1fc6f-110">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="1fc6f-111">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="1fc6f-112">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="1fc6f-113">[圧縮]</span><span class="sxs-lookup"><span data-stu-id="1fc6f-113">Compression</span></span>

<span data-ttu-id="1fc6f-114">Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="1fc6f-115">次の圧縮アルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="1fc6f-116">[Brotli](https://tools.ietf.org/html/rfc7932) (最高レベル)</span><span class="sxs-lookup"><span data-stu-id="1fc6f-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="1fc6f-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="1fc6f-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="1fc6f-118"> は、適切な圧縮ファイルにサービスを提供するため、ホストに依存します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="1fc6f-119">ASP.NET Core でホストするプロジェクトを使用するとき、ホスト プロジェクトでは、コンテント ネゴシエーションを実行したり、静的に圧縮されたファイルにサービスを提供したりできます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="1fc6f-120">Blazor WebAssembly スタンドアロン アプリをホストするとき、静的に圧縮されたファイルにサービスが提供されるよう、追加の作業が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="1fc6f-121">IIS の `web.config` の圧縮構成については、[IIS の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="1fc6f-122">GitHub ページなど、静的に圧縮されたファイル コンテント ネゴシエーションをサポートしない静的ホスティング ソリューションでホストするとき、Brotli 圧縮ファイルをフェッチし、デコードするようにアプリを構成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="1fc6f-123">アプリの [google/brotli GitHub リポジトリ](https://github.com/google/brotli/)から Brotli デコーダーを参照します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="1fc6f-124">そのデコーダーを使用するようにアプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-124">Update the app to use the decoder.</span></span> <span data-ttu-id="1fc6f-125">`wwwroot/index.html` の終了 `<body>` タグの中にあるマークアップを次に変更します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
          'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
          { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
  </script>
  ```
   
<span data-ttu-id="1fc6f-126">圧縮を無効にするには、アプリケーションのプロジェクト ファイルに `BlazorEnableCompression` MSBuild プロパティを追加し、値を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="1fc6f-127">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="1fc6f-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="1fc6f-128">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="1fc6f-129">2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="1fc6f-130">`Main.razor`:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="1fc6f-131">`About.razor`: `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="1fc6f-132">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="1fc6f-133">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-133">The browser makes a request.</span></span>
1. <span data-ttu-id="1fc6f-134">既定のページ (通常は `index.html`) が返されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="1fc6f-135">`index.html` によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-135">`index.html` bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="1fc6f-136"> のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="1fc6f-137">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="1fc6f-138">" *Blazor WebAssembly アプリ*" 内にある内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="1fc6f-139">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-139">The router handles the requests internally.</span></span>

<span data-ttu-id="1fc6f-140">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="1fc6f-141">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="1fc6f-142">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する `index.html` ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="1fc6f-143">`index.html` が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="1fc6f-144">IIS サーバーに展開する場合は、アプリで発行される `web.config` ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="1fc6f-145">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="1fc6f-146">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="1fc6f-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="1fc6f-147">"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="1fc6f-148">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="1fc6f-149">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-149">The two apps are deployed together.</span></span> <span data-ttu-id="1fc6f-150">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="1fc6f-151">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **`Hosted`** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="1fc6f-152">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="1fc6f-153">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="1fc6f-154">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="1fc6f-154">Standalone deployment</span></span>

<span data-ttu-id="1fc6f-155">"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="1fc6f-156">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="1fc6f-157">スタンドアロンのデプロイ アセットは、`/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="1fc6f-158">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1fc6f-158">Azure App Service</span></span>

Blazor<span data-ttu-id="1fc6f-159"> WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-159"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="1fc6f-160">スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="1fc6f-161">現時点では、アプリをホストする Linux サーバー イメージは使用できません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="1fc6f-162">このシナリオを可能にするための取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="1fc6f-163">IIS</span><span class="sxs-lookup"><span data-stu-id="1fc6f-163">IIS</span></span>

<span data-ttu-id="1fc6f-164">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="1fc6f-165">Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="1fc6f-166">発行されたアセットは、`/bin/Release/{TARGET FRAMEWORK}/publish` フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="1fc6f-167">`publish` フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="1fc6f-168">web.config</span><span class="sxs-lookup"><span data-stu-id="1fc6f-168">web.config</span></span>

<span data-ttu-id="1fc6f-169">Blazor プロジェクトが発行されると、`web.config` ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="1fc6f-170">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="1fc6f-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="1fc6f-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="1fc6f-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="1fc6f-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="1fc6f-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="1fc6f-176">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="1fc6f-177">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="1fc6f-178">アプリの静的なアセットが存在するサブディレクトリ (`wwwroot/{PATH REQUESTED}`) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="1fc6f-179">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント (`wwwroot/index.html`) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="1fc6f-180">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="1fc6f-180">Use a custom web.config</span></span>

<span data-ttu-id="1fc6f-181">カスタムの `web.config` ファイルを使用するには、カスタムの `web.config` ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="1fc6f-182">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="1fc6f-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="1fc6f-183">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="1fc6f-184">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="1fc6f-185">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="1fc6f-186">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="1fc6f-187">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="1fc6f-188">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="1fc6f-189">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="1fc6f-190">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-190">Copy the installer to the server.</span></span> <span data-ttu-id="1fc6f-191">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-191">Run the installer.</span></span> <span data-ttu-id="1fc6f-192">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="1fc6f-193">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="1fc6f-194">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="1fc6f-194">Configure the website</span></span>

<span data-ttu-id="1fc6f-195">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="1fc6f-196">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-196">The folder contains:</span></span>

* <span data-ttu-id="1fc6f-197">`web.config` ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="1fc6f-198">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="1fc6f-199">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="1fc6f-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="1fc6f-200">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="1fc6f-201">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="1fc6f-202">Blazor アプリで発行された `web.config` ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="1fc6f-203">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="1fc6f-204">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:blazor/host-and-deploy/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="1fc6f-205">IIS でサブアプリを構成するときに、アプリの `index.html` ファイル内のアプリのベース パスを、使用している IIS の別名に設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="1fc6f-206">Brotli と Gzip の圧縮</span><span class="sxs-lookup"><span data-stu-id="1fc6f-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="1fc6f-207">`web.config` を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="1fc6f-208">構成例については、[`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="1fc6f-209">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="1fc6f-209">Troubleshooting</span></span>

<span data-ttu-id="1fc6f-210">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="1fc6f-211">モジュールがインストールされていない場合、IIS では `web.config` ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="1fc6f-212">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="1fc6f-213">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="1fc6f-214">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="1fc6f-214">Azure Storage</span></span>

<span data-ttu-id="1fc6f-215">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="1fc6f-216">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="1fc6f-217">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="1fc6f-218">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="1fc6f-219">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="1fc6f-220"> コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="1fc6f-221">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="1fc6f-222">`index.html` BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="1fc6f-223">ファイルの `Content-Type` ヘッダーに不適切な MIME の種類があるために、実行時にファイルが読み込まれない場合は、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="1fc6f-224">ファイルの展開時に適切な MIME の種類 (`Content-Type` ヘッダー) を設定するようにツールを構成します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="1fc6f-225">アプリの展開後に、ファイルの MIME の種類 (`Content-Type` ヘッダー) を変更します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="1fc6f-226">Storage Explorer (Azure portal) で、ファイルごとに次のようにします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="1fc6f-227">ファイルを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="1fc6f-228">**ContentType** を設定し、 **[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="1fc6f-229">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="1fc6f-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="1fc6f-230">Nginx</span></span>

<span data-ttu-id="1fc6f-231">以下に示す `nginx.conf` ファイルは、Nginx が対応するファイルをディスク上で見つけられないときに `index.html` ファイルを送信するよう Nginx を構成する方法を示すために、簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="1fc6f-232">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="1fc6f-233">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="1fc6f-233">Nginx in Docker</span></span>

<span data-ttu-id="1fc6f-234">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="1fc6f-235">`nginx.config` ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-235">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="1fc6f-236">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="1fc6f-237">Apache</span><span class="sxs-lookup"><span data-stu-id="1fc6f-237">Apache</span></span>

<span data-ttu-id="1fc6f-238">Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="1fc6f-239">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-239">Create the Apache configuration file.</span></span> <span data-ttu-id="1fc6f-240">次の例は、簡略化された構成ファイル (`blazorapp.config`) です。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-240">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="1fc6f-241">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="1fc6f-242">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="1fc6f-243">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-243">Restart the Apache service.</span></span>

<span data-ttu-id="1fc6f-244">詳細については、[`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) および [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-244">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="1fc6f-245">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="1fc6f-245">GitHub Pages</span></span>

<span data-ttu-id="1fc6f-246">URL の書き換えを処理するために、`404.html` ファイルを、要求を `index.html` ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-246">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="1fc6f-247">コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="1fc6f-248">コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="1fc6f-249">組織のサイトではなくプロジェクトのサイトを使用している場合、`index.html` 内の `<base>` タグを追加または更新します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-249">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="1fc6f-250">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1fc6f-251">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="1fc6f-251">Host configuration values</span></span>

<span data-ttu-id="1fc6f-252">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="1fc6f-253">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="1fc6f-253">Content root</span></span>

<span data-ttu-id="1fc6f-254">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="1fc6f-255">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="1fc6f-256">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1fc6f-257">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="1fc6f-258">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-258">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1fc6f-259">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="1fc6f-260">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1fc6f-261">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-261">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="1fc6f-262">パス ベース</span><span class="sxs-lookup"><span data-stu-id="1fc6f-262">Path base</span></span>

<span data-ttu-id="1fc6f-263">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="1fc6f-264">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="1fc6f-265">詳細については、「[アプリのベースパス](xref:blazor/host-and-deploy/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1fc6f-266">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="1fc6f-267">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="1fc6f-268">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1fc6f-269">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="1fc6f-270">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-270">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1fc6f-271">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="1fc6f-272">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1fc6f-273">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-273">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="1fc6f-274">URL</span><span class="sxs-lookup"><span data-stu-id="1fc6f-274">URLs</span></span>

<span data-ttu-id="1fc6f-275">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="1fc6f-276">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1fc6f-277">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="1fc6f-278">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-278">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1fc6f-279">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="1fc6f-280">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1fc6f-281">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-281">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="1fc6f-282">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="1fc6f-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="1fc6f-283"> では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="1fc6f-284">詳細については、「<xref:blazor/host-and-deploy/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="1fc6f-285">カスタム ブート リソースの読み込み</span><span class="sxs-lookup"><span data-stu-id="1fc6f-285">Custom boot resource loading</span></span>

<span data-ttu-id="1fc6f-286">Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="1fc6f-287">次のシナリオで `loadBootResource` を使用します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="1fc6f-288">ユーザーが、タイムゾーン データや `dotnet.wasm` などの静的なリソースを CDN から読み込むことができるようにする。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-288">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="1fc6f-289">HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="1fc6f-290">各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="1fc6f-291">`loadBootResource` パラメーターは次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="1fc6f-292">パラメーター</span><span class="sxs-lookup"><span data-stu-id="1fc6f-292">Parameter</span></span>    | <span data-ttu-id="1fc6f-293">説明</span><span class="sxs-lookup"><span data-stu-id="1fc6f-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="1fc6f-294">リソースの型。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-294">The type of the resource.</span></span> <span data-ttu-id="1fc6f-295">許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="1fc6f-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="1fc6f-296">リソースの名前。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="1fc6f-297">リソースの相対 URI または絶対 URI。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="1fc6f-298">応答で予想されるコンテンツを表す整合性文字列。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="1fc6f-299">`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="1fc6f-300">URI 文字列。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-300">URI string.</span></span> <span data-ttu-id="1fc6f-301">次の例 (`wwwroot/index.html`) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-301">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="1fc6f-302">タイムゾーン データ</span><span class="sxs-lookup"><span data-stu-id="1fc6f-302">Timezone data</span></span>

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

* <span data-ttu-id="1fc6f-303">`Promise<Response>`。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-303">`Promise<Response>`.</span></span> <span data-ttu-id="1fc6f-304">ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="1fc6f-305">次の例 (`wwwroot/index.html`) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-305">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="1fc6f-306">`null`/`undefined`。既定の読み込み動作になります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="1fc6f-307">外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="1fc6f-308">通常、既定では、必要なヘッダーが CDN によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="1fc6f-309">カスタム動作の型のみ指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="1fc6f-310">`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="1fc6f-311">DLL ファイルのファイル名拡張子を変更する</span><span class="sxs-lookup"><span data-stu-id="1fc6f-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="1fc6f-312">アプリで発行されている `.dll` ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-312">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="1fc6f-313">アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように `.dll` ファイルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-313">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="1fc6f-314">アプリで発行された出力の `wwwroot` ディレクトリ (たとえば、`{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 内の `.dll` ファイルをターゲットにします。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-314">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="1fc6f-315">次の例では、`.dll` ファイルの名前が `.bin` ファイル拡張子を使用するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-315">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="1fc6f-316">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="1fc6f-317">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="1fc6f-318">Linux または macOS の場合:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="1fc6f-319">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="1fc6f-320">`.bin` とは異なるファイル拡張子を使用するには、前のコマンドで `.bin` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-320">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="1fc6f-321">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルに対処するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-321">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="1fc6f-322">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-322">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="1fc6f-323">このアプローチでは、圧縮は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="1fc6f-324">更新した `blazor.boot.json` ファイルを圧縮します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-324">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="1fc6f-325">上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="1fc6f-326">`wwwroot/service-worker-assets.js.br` と `wwwroot/service-worker-assets.js.gz` を削除または再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-326">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="1fc6f-327">そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="1fc6f-328">次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="1fc6f-329">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="1fc6f-329">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="1fc6f-330">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="1fc6f-331">プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="1fc6f-332">フィードバックを提供するには、[aspnetcore/issue #5477](https://github.com/dotnet/aspnetcore/issues/5477) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1fc6f-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
