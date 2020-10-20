---
title: ASP.NET Core Blazor WebAssembly のホストと展開
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/09/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 63954bd2fbb8fdb2e347d552a10adc52263c3ad6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900714"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c8a07-103">ASP.NET Core Blazor WebAssembly のホストと展開</span><span class="sxs-lookup"><span data-stu-id="c8a07-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c8a07-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="c8a07-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="c8a07-105">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用する場合は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="c8a07-106">Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="c8a07-107">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="c8a07-108">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="c8a07-109">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c8a07-110">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="c8a07-111">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="c8a07-112">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="c8a07-113">[圧縮]</span><span class="sxs-lookup"><span data-stu-id="c8a07-113">Compression</span></span>

<span data-ttu-id="c8a07-114">Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="c8a07-115">次の圧縮アルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="c8a07-116">[Brotli](https://tools.ietf.org/html/rfc7932) (最高レベル)</span><span class="sxs-lookup"><span data-stu-id="c8a07-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="c8a07-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="c8a07-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="c8a07-118">Blazor は、適切な圧縮ファイルにサービスを提供するため、ホストに依存します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="c8a07-119">ASP.NET Core でホストするプロジェクトを使用するとき、ホスト プロジェクトでは、コンテント ネゴシエーションを実行したり、静的に圧縮されたファイルにサービスを提供したりできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="c8a07-120">Blazor WebAssembly スタンドアロン アプリをホストするとき、静的に圧縮されたファイルにサービスが提供されるよう、追加の作業が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="c8a07-121">IIS の `web.config` の圧縮構成については、[IIS の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="c8a07-122">GitHub ページなど、静的に圧縮されたファイル コンテント ネゴシエーションをサポートしない静的ホスティング ソリューションでホストするとき、Brotli 圧縮ファイルをフェッチし、デコードするようにアプリを構成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="c8a07-123">[google/brotli GitHub リポジトリ](https://github.com/google/brotli)から、JavaScript Brotli デコーダーを入手します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c8a07-124">2020 年 9 月の時点で、デコーダー ファイルは `decode.js` という名前で、リポジトリの [`js` フォルダー](https://github.com/google/brotli/tree/master/js)にあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="c8a07-125">以前のバージョンは [google/brotli GitHub リポジトリ](https://github.com/google/brotli)の `decode.js` スクリプト (`decode.min.js`) の縮小バージョンにあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="c8a07-126">[Window.BrotliDecode が decode.min.js (google/brotli #844) で設定されない](https://github.com/google/brotli/issues/844)問題が解決されるまで、自分でスクリプトを縮小するか、[npm package](https://www.npmjs.com/package/brotli) を使用してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="c8a07-127">このセクションのサンプル コードでは、スクリプトの**未縮小**バージョンを使用しています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="c8a07-128">そのデコーダーを使用するようにアプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-128">Update the app to use the decoder.</span></span> <span data-ttu-id="c8a07-129">`wwwroot/index.html` の終了タグ `<body>` に含まれるマークアップを次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="c8a07-130">圧縮を無効にするには、アプリケーションのプロジェクト ファイルに `BlazorEnableCompression` MSBuild プロパティを追加し、値を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="c8a07-131">`BlazorEnableCompression` プロパティは、コマンド シェルで次の構文を使用して [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c8a07-132">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="c8a07-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c8a07-133">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor Server (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="c8a07-134">次の 2 つのコンポーネントがある Blazor WebAssembly アプリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="c8a07-135">`Main.razor`:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="c8a07-136">`About.razor`: `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="c8a07-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="c8a07-137">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="c8a07-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c8a07-138">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-138">The browser makes a request.</span></span>
1. <span data-ttu-id="c8a07-139">既定のページ (通常は `index.html`) が返されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="c8a07-140">`index.html` によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="c8a07-141">Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="c8a07-142">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="c8a07-143">" *Blazor WebAssembly アプリ内にある*" 内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c8a07-144">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-144">The router handles the requests internally.</span></span>

<span data-ttu-id="c8a07-145">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c8a07-146">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="c8a07-147">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する `index.html` ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="c8a07-148">`index.html` が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="c8a07-149">IIS サーバーに展開する場合は、アプリで発行される `web.config` ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="c8a07-150">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c8a07-151">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="c8a07-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c8a07-152">"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="c8a07-153">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="c8a07-154">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-154">The two apps are deployed together.</span></span> <span data-ttu-id="c8a07-155">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c8a07-156">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **`Hosted`** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="c8a07-157">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c8a07-158">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="c8a07-159">複数の Blazor WebAssembly アプリによるホストされた展開</span><span class="sxs-lookup"><span data-stu-id="c8a07-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="c8a07-160">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="c8a07-160">App configuration</span></span>

<span data-ttu-id="c8a07-161">複数の Blazor WebAssembly アプリを提供するようにホストされた Blazor ソリューションを構成するには:</span><span class="sxs-lookup"><span data-stu-id="c8a07-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="c8a07-162">既存のホストされた Blazor ソリューションを使用するか、Blazor のホストされたプロジェクト テンプレートから新しいソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="c8a07-163">クライアント アプリのプロジェクト ファイルで、値が `FirstApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加して、プロジェクトの静的アセットの基本パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="c8a07-164">ソリューションに 2 つ目のクライアント アプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="c8a07-165">`SecondClient` という名前のフォルダーをソリューションのフォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="c8a07-166">Blazor WebAssembly プロジェクト テンプレートから `SecondClient` フォルダーに `SecondBlazorApp.Client` という名前の Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="c8a07-167">アプリのプロジェクト ファイル内で:</span><span class="sxs-lookup"><span data-stu-id="c8a07-167">In the app's project file:</span></span>

    * <span data-ttu-id="c8a07-168">値が `SecondApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="c8a07-169">`Shared` プロジェクトにプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="c8a07-170">プレースホルダー `{SOLUTION NAME}` は、ソリューションの名前です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="c8a07-171">サーバー アプリのプロジェクト ファイルで、追加したクライアン トアプリに対するプロジェクト参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="c8a07-172">サーバー アプリの `Properties/launchSettings.json` ファイルで、ポート 5001 と 5002 でクライアント アプリにアクセスするように、Kestrel プロファイル (`{SOLUTION NAME}.Server`) の `applicationUrl` を構成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="c8a07-173">サーバー アプリの `Startup.Configure` メソッド (`Startup.cs`) で、<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> の呼び出しの後にある次の行を削除します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="c8a07-174">要求をクライアント アプリにマップするミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="c8a07-175">次の例では、ミドルウェアが以下のときに実行されるように構成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="c8a07-176">要求のポートが、元のクライアント アプリの場合は 5001、追加されたクライアント アプリの場合は 5002 である。</span><span class="sxs-lookup"><span data-stu-id="c8a07-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="c8a07-177">要求のホストが、元のクライアント アプリの場合は `firstapp.com`、追加されたクライアント アプリの場合は `secondapp.com` である。</span><span class="sxs-lookup"><span data-stu-id="c8a07-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="c8a07-178">このセクションで示されている例では、以下に対する追加構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="c8a07-179">例のホスト ドメイン `firstapp.com` および `secondapp.com` でのアプリへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="c8a07-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="c8a07-180">クライアント アプリで TLS セキュリティ (HTTPS) を有効にするための証明書。</span><span class="sxs-lookup"><span data-stu-id="c8a07-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="c8a07-181">必要な構成はこの記事の範囲を超えており、ソリューションのホスト方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="c8a07-182">詳細については、[ホストと展開に関する記事](xref:host-and-deploy/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="c8a07-183">前に行を削除した場所に、次のコードを配置します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="c8a07-184">サーバー アプリの天気予報コントローラー (`Controllers/WeatherForecastController.cs`) で、`WeatherForecastController` に対する既存のルート (`[Route("[controller]")]`) を次のルートに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="c8a07-185">前にサーバー アプリの `Startup.Configure` メソッドに追加したミドルウェアでは、`/WeatherForecast` に対する受信要求が、ポート (5001/5002) またはドメイン (`firstapp.com`/`secondapp.com`) に応じて、`/FirstApp/WeatherForecast` または `/SecondApp/WeatherForecast` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="c8a07-186">前のコントローラー ルートは、サーバー アプリからクライアント アプリに気象データを返すために必要です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="c8a07-187">静的アセットとクラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="c8a07-187">Static assets and class libraries</span></span>

<span data-ttu-id="c8a07-188">静的アセットには、次の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="c8a07-189">アセットがクライアント アプリの `wwwroot` フォルダー内にある場合は、通常どおりにパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="c8a07-190">アセットが [Razor クラス ライブラリ (RCL)](xref:blazor/components/class-libraries) の `wwwroot` フォルダーにある場合は、[RCL の記事](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)のガイダンスに従って、クライアント アプリの静的アセットを参照します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c8a07-191">クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="c8a07-192">いずれかのコンポーネントでスタイルシートまたは JavaScript ファイルが必要な場合は、次のいずれかの方法を使用して、静的アセットを取得します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="c8a07-193">クライアント アプリの `wwwroot/index.html` ファイルでは、静的アセットに (`<link>`) リンクできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="c8a07-194">コンポーネントでは、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、静的アセットを取得できます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="c8a07-195">次の例では上記の方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c8a07-196">クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="c8a07-197">スタイルシートまたは JavaScript ファイルが必要なコンポーネントがある場合は、クライアント アプリの `wwwroot/index.html` ファイルに正しい静的アセットのリンクを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="c8a07-198">次の例ではこれらの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="c8a07-199">次の `Jeep` コンポーネントをクライアント アプリのいずれかに追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="c8a07-200">`Jeep` コンポーネントでは次のものが使用されています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="c8a07-201">クライアント アプリの `wwwroot` フォルダーにある画像 (`jeep-cj.png`)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="c8a07-202">[追加された Razor コンポーネント ライブラリ](xref:blazor/components/class-libraries) (`JeepImage`) の `wwwroot` フォルダーにある画像 (`jeep-yj.png`)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="c8a07-203">この例のコンポーネント (`Component1`) は、`JeepImage` ライブラリがソリューションに追加されると、RCL プロジェクト テンプレートによって自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="c8a07-204">画像を所有している場合を除き、車両の画像を公開**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="c8a07-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="c8a07-205">そうしないと、著作権侵害のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c8a07-206">ライブラリの `jeep-yj.png` 画像を、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="c8a07-207">`my-component` CSS クラスをクライアント アプリのページに提供するには、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、ライブラリのスタイルシートにリンクします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="c8a07-208">[`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用する代わりに、クライアント アプリの `wwwroot/index.html` ファイルからスタイルシートを読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="c8a07-209">この方法を使用すると、クライアント アプリのすべてのコンポーネントでスタイルシートを使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="c8a07-210">ライブラリの `jeep-yj.png` 画像は、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="c8a07-211">クライアント アプリの `wwwroot/index.html` ファイルでは、次の `<link>` タグを追加してライブラリのスタイルシートを要求します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="c8a07-212">クライアント アプリの `NavMenu` コンポーネント (`Shared/NavMenu.razor`) に、`Jeep` コンポーネントへのナビゲーションを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="c8a07-213">RCL の詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="c8a07-214">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="c8a07-214">Standalone deployment</span></span>

<span data-ttu-id="c8a07-215">"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c8a07-216">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="c8a07-217">スタンドアロンのデプロイ アセットは、`/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="c8a07-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="c8a07-218">Azure App Service</span></span>

<span data-ttu-id="c8a07-219">Blazor WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="c8a07-220">スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="c8a07-221">現時点では、アプリをホストする Linux サーバー イメージは使用できません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="c8a07-222">このシナリオを可能にするための取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="c8a07-223">IIS</span><span class="sxs-lookup"><span data-stu-id="c8a07-223">IIS</span></span>

<span data-ttu-id="c8a07-224">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="c8a07-225">Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c8a07-226">発行されたアセットは、`/bin/Release/{TARGET FRAMEWORK}/publish` フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="c8a07-227">`publish` フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="c8a07-228">web.config</span><span class="sxs-lookup"><span data-stu-id="c8a07-228">web.config</span></span>

<span data-ttu-id="c8a07-229">Blazor プロジェクトが発行されると、`web.config` ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c8a07-230">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c8a07-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c8a07-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="c8a07-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c8a07-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="c8a07-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c8a07-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="c8a07-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c8a07-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="c8a07-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c8a07-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="c8a07-236">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c8a07-237">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c8a07-238">アプリの静的なアセットが存在するサブディレクトリ (`wwwroot/{PATH REQUESTED}`) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="c8a07-239">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント (`wwwroot/index.html`) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="c8a07-240">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="c8a07-240">Use a custom web.config</span></span>

<span data-ttu-id="c8a07-241">カスタムの `web.config` ファイルを使用するには、カスタムの `web.config` ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="c8a07-242">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="c8a07-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="c8a07-243">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c8a07-244">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c8a07-245">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c8a07-246">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c8a07-247">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c8a07-248">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c8a07-249">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c8a07-250">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-250">Copy the installer to the server.</span></span> <span data-ttu-id="c8a07-251">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-251">Run the installer.</span></span> <span data-ttu-id="c8a07-252">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c8a07-253">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="c8a07-254">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="c8a07-254">Configure the website</span></span>

<span data-ttu-id="c8a07-255">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c8a07-256">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-256">The folder contains:</span></span>

* <span data-ttu-id="c8a07-257">`web.config` ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c8a07-258">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="c8a07-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="c8a07-259">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="c8a07-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="c8a07-260">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="c8a07-261">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="c8a07-262">Blazor アプリで発行された `web.config` ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="c8a07-263">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="c8a07-264">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:blazor/host-and-deploy/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="c8a07-265">IIS でサブアプリを構成するときに、アプリの `index.html` ファイル内のアプリのベース パスを、使用している IIS の別名に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="c8a07-266">Brotli と Gzip の圧縮</span><span class="sxs-lookup"><span data-stu-id="c8a07-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="c8a07-267">`web.config` を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="c8a07-268">構成例については、[`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="c8a07-269">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c8a07-269">Troubleshooting</span></span>

<span data-ttu-id="c8a07-270">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c8a07-271">モジュールがインストールされていない場合、IIS では `web.config` ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="c8a07-272">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="c8a07-273">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="c8a07-274">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="c8a07-274">Azure Storage</span></span>

<span data-ttu-id="c8a07-275">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="c8a07-276">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="c8a07-277">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="c8a07-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="c8a07-278">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="c8a07-279">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="c8a07-280">Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="c8a07-281">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="c8a07-282">`index.html` BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="c8a07-283">ファイルの `Content-Type` ヘッダーに不適切な MIME の種類があるために、実行時にファイルが読み込まれない場合は、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="c8a07-284">ファイルの展開時に適切な MIME の種類 (`Content-Type` ヘッダー) を設定するようにツールを構成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="c8a07-285">アプリの展開後に、ファイルの MIME の種類 (`Content-Type` ヘッダー) を変更します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="c8a07-286">Storage Explorer (Azure portal) で、ファイルごとに次のようにします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="c8a07-287">ファイルを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="c8a07-288">**ContentType** を設定し、 **[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="c8a07-289">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="c8a07-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="c8a07-290">Nginx</span></span>

<span data-ttu-id="c8a07-291">以下に示す `nginx.conf` ファイルは、Nginx が対応するファイルをディスク上で見つけられないときに `index.html` ファイルを送信するよう Nginx を構成する方法を示すために、簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="c8a07-292">[NGINX バースト レート制限](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)を [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) で設定するとき、アプリによって行われる比較的大量の要求を受け入れる目的で、場合によっては、Blazor WebAssembly アプリの `burst` パラメーター値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="c8a07-293">最初に、値を 60 以上に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-293">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="c8a07-294">"*503 - サービスを利用できません*" という状態コードを要求が受信していることがブラウザー開発者ツールまたはネットワーク トラフィック ツールで示されている場合、この値を増やします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="c8a07-295">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="c8a07-296">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="c8a07-296">Nginx in Docker</span></span>

<span data-ttu-id="c8a07-297">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c8a07-298">`nginx.config` ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="c8a07-299">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="c8a07-300">Apache</span><span class="sxs-lookup"><span data-stu-id="c8a07-300">Apache</span></span>

<span data-ttu-id="c8a07-301">Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:</span><span class="sxs-lookup"><span data-stu-id="c8a07-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="c8a07-302">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-302">Create the Apache configuration file.</span></span> <span data-ttu-id="c8a07-303">次の例は、簡略化された構成ファイル (`blazorapp.config`) です。</span><span class="sxs-lookup"><span data-stu-id="c8a07-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="c8a07-304">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="c8a07-305">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="c8a07-306">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-306">Restart the Apache service.</span></span>

<span data-ttu-id="c8a07-307">詳細については、[`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) および [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="c8a07-308">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="c8a07-308">GitHub Pages</span></span>

<span data-ttu-id="c8a07-309">URL の書き換えを処理するために、`wwwroot/404.html` ファイルを、要求を `index.html` ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="c8a07-310">例については、[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="c8a07-311">[ライブ サイト](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="c8a07-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="c8a07-312">組織のサイトではなくプロジェクトのサイトを使用しているときは、`wwwroot/index.html` 内の `<base>` タグを更新します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="c8a07-313">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `/my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="c8a07-314">[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)では、[`.github/workflows/main.yml` 構成ファイル](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)による発行時に、基本 `href` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="c8a07-315">[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)は、.NET Foundation または Microsoft では、所有、管理、またはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c8a07-316">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="c8a07-316">Host configuration values</span></span>

<span data-ttu-id="c8a07-317">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c8a07-318">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="c8a07-318">Content root</span></span>

<span data-ttu-id="c8a07-319">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="c8a07-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="c8a07-320">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="c8a07-321">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c8a07-322">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="c8a07-323">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c8a07-324">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="c8a07-325">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c8a07-326">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="c8a07-327">パス ベース</span><span class="sxs-lookup"><span data-stu-id="c8a07-327">Path base</span></span>

<span data-ttu-id="c8a07-328">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c8a07-329">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="c8a07-330">詳細については、「[アプリのベースパス](xref:blazor/host-and-deploy/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c8a07-331">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c8a07-332">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c8a07-333">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c8a07-334">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="c8a07-335">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c8a07-336">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="c8a07-337">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c8a07-338">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="c8a07-339">URL</span><span class="sxs-lookup"><span data-stu-id="c8a07-339">URLs</span></span>

<span data-ttu-id="c8a07-340">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c8a07-341">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c8a07-342">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c8a07-343">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="c8a07-344">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c8a07-345">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c8a07-346">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="c8a07-347">トリマーを構成する</span><span class="sxs-lookup"><span data-stu-id="c8a07-347">Configure the Trimmer</span></span>

<span data-ttu-id="c8a07-348">Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL トリミング設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c8a07-349">詳細については、「<xref:blazor/host-and-deploy/configure-trimmer>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="c8a07-350">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="c8a07-350">Configure the Linker</span></span>

<span data-ttu-id="c8a07-351">Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c8a07-352">詳細については、「<xref:blazor/host-and-deploy/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="c8a07-353">カスタム ブート リソースの読み込み</span><span class="sxs-lookup"><span data-stu-id="c8a07-353">Custom boot resource loading</span></span>

<span data-ttu-id="c8a07-354">Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="c8a07-355">次のシナリオで `loadBootResource` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="c8a07-356">ユーザーが、タイムゾーン データや `dotnet.wasm` などの静的なリソースを CDN から読み込むことができるようにする。</span><span class="sxs-lookup"><span data-stu-id="c8a07-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="c8a07-357">HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。</span><span class="sxs-lookup"><span data-stu-id="c8a07-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="c8a07-358">各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="c8a07-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="c8a07-359">`loadBootResource` パラメーターは次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="c8a07-360">パラメーター</span><span class="sxs-lookup"><span data-stu-id="c8a07-360">Parameter</span></span>    | <span data-ttu-id="c8a07-361">説明</span><span class="sxs-lookup"><span data-stu-id="c8a07-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="c8a07-362">リソースの型。</span><span class="sxs-lookup"><span data-stu-id="c8a07-362">The type of the resource.</span></span> <span data-ttu-id="c8a07-363">許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="c8a07-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="c8a07-364">リソースの名前。</span><span class="sxs-lookup"><span data-stu-id="c8a07-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="c8a07-365">リソースの相対 URI または絶対 URI。</span><span class="sxs-lookup"><span data-stu-id="c8a07-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="c8a07-366">応答で予想されるコンテンツを表す整合性文字列。</span><span class="sxs-lookup"><span data-stu-id="c8a07-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="c8a07-367">`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="c8a07-368">URI 文字列。</span><span class="sxs-lookup"><span data-stu-id="c8a07-368">URI string.</span></span> <span data-ttu-id="c8a07-369">次の例 (`wwwroot/index.html`) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="c8a07-370">タイムゾーン データ</span><span class="sxs-lookup"><span data-stu-id="c8a07-370">Timezone data</span></span>

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

* <span data-ttu-id="c8a07-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="c8a07-371">`Promise<Response>`.</span></span> <span data-ttu-id="c8a07-372">ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="c8a07-373">次の例 (`wwwroot/index.html`) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="c8a07-374">`null`/`undefined`。既定の読み込み動作になります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="c8a07-375">外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="c8a07-376">通常、既定では、必要なヘッダーが CDN によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="c8a07-377">カスタム動作の型のみ指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="c8a07-378">`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="c8a07-379">DLL ファイルのファイル名拡張子を変更する</span><span class="sxs-lookup"><span data-stu-id="c8a07-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="c8a07-380">アプリで発行されている `.dll` ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="c8a07-381">アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように `.dll` ファイルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="c8a07-382">アプリで発行された出力の `wwwroot` ディレクトリ (たとえば、`{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 内の `.dll` ファイルをターゲットにします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="c8a07-383">次の例では、`.dll` ファイルの名前が `.bin` ファイル拡張子を使用するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="c8a07-384">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="c8a07-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="c8a07-385">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="c8a07-386">Linux または macOS の場合:</span><span class="sxs-lookup"><span data-stu-id="c8a07-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="c8a07-387">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="c8a07-388">`.bin` とは異なるファイル拡張子を使用するには、前のコマンドで `.bin` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="c8a07-389">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルに対処するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="c8a07-390">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="c8a07-391">このアプローチでは、圧縮は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="c8a07-392">更新した `blazor.boot.json` ファイルを圧縮します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="c8a07-393">上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="c8a07-394">`wwwroot/service-worker-assets.js.br` と `wwwroot/service-worker-assets.js.gz` を削除または再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="c8a07-395">そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="c8a07-396">次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="c8a07-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="c8a07-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="c8a07-398">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="c8a07-399">プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="c8a07-400">同じアセンブリの名前変更と遅延読み込みについては、<xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> のガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="c8a07-401">整合性チェックの失敗を解決する</span><span class="sxs-lookup"><span data-stu-id="c8a07-401">Resolve integrity check failures</span></span>

<span data-ttu-id="c8a07-402">Blazor WebAssembly によってアプリのスタートアップ ファイルがダウンロードされると、応答に対して整合性チェックを実行するようにブラウザーに指示が出されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-402">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="c8a07-403">`blazor.boot.json` ファイルの情報を使用して、`.dll`、`.wasm`、およびその他のファイルに対して想定される SHA-256 ハッシュ値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-403">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="c8a07-404">これには次のような理由で利点があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-404">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="c8a07-405">たとえばユーザーがアプリケーション ファイルをダウンロードしているときに Web サーバーに新しい展開が適用された場合など、一貫性のないファイルのセットを読み込むリスクがなくなります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-405">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="c8a07-406">不整合なファイルは未定義の動作につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-406">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="c8a07-407">ユーザーのブラウザーが不整合または無効な応答をキャッシュしないようになります (その場合、ページを手動で更新してもアプリを起動できなくなる可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="c8a07-407">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="c8a07-408">応答を安全にキャッシュできるようになり、想定される SHA-256 ハッシュ自体が変更されるまでサーバー側の変更がチェックされません。そのため、後続のページ読み込みに伴う要求の数が減り、完了までにかかる時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-408">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="c8a07-409">想定される SHA-256 ハッシュに一致しない応答が Web サーバーから返された場合、ブラウザーの開発者コンソールに次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-409">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

```
Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. The resource has been blocked.
```

<span data-ttu-id="c8a07-410">ほとんどの場合、これは整合性チェック自体に関する問題では "*ありません*"。</span><span class="sxs-lookup"><span data-stu-id="c8a07-410">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="c8a07-411">代わりにこれは他の問題があることを意味し、整合性チェックによってその他の問題に関する警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-411">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="c8a07-412">整合性に関する問題の診断</span><span class="sxs-lookup"><span data-stu-id="c8a07-412">Diagnosing integrity problems</span></span>

<span data-ttu-id="c8a07-413">アプリがビルドされると、生成された `blazor.boot.json` マニフェストによって、ビルド出力が生成された時点でのブート リソース (たとえば `.dll`、`.wasm`、およびその他のファイル) の SHA-256 ハッシュが記述されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-413">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="c8a07-414">整合性チェックは、`blazor.boot.json` の SHA-256 ハッシュがブラウザーに配信されるファイルと一致している限り成功します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-414">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="c8a07-415">これに失敗する一般的な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-415">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="c8a07-416">Web サーバーからの応答が、ブラウザーが要求したファイルではなく、エラー (たとえば *404 - Not Found* や *500 - Internal Server Error*) である場合。</span><span class="sxs-lookup"><span data-stu-id="c8a07-416">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="c8a07-417">これはブラウザーによって、応答エラーとしてではなく整合性チェックの失敗として報告されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-417">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="c8a07-418">ファイルのビルドからブラウザーへの配信の間に、何らかによってファイルの内容が変更された場合。</span><span class="sxs-lookup"><span data-stu-id="c8a07-418">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="c8a07-419">次のような場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-419">This might happen:</span></span>
   * <span data-ttu-id="c8a07-420">ユーザーまたはビルド ツールによって、ビルド出力が手動で変更された場合。</span><span class="sxs-lookup"><span data-stu-id="c8a07-420">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="c8a07-421">展開プロセスの何らかの側面によってファイルが変更された場合。</span><span class="sxs-lookup"><span data-stu-id="c8a07-421">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="c8a07-422">たとえば、Git ベースの展開メカニズムを使用する場合は、Windows でファイルをコミットして Linux でチェックアウトすると、Git によって Windows スタイルの改行コードが Unix スタイルの改行コードに透過的に変換されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-422">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="c8a07-423">ファイルの改行コードを変更すると、SHA-256 ハッシュが変更されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-423">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="c8a07-424">この問題を回避するには、[`.gitattributes` を使用してビルド成果物を `binary` ファイルとして扱う](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)ことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-424">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="c8a07-425">Web サーバーによるファイル提供の一環として、その内容が変更された場合。</span><span class="sxs-lookup"><span data-stu-id="c8a07-425">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="c8a07-426">たとえば、一部のコンテンツ配信ネットワーク (CDN) では、HTML の[縮小](xref:client-side/bundling-and-minification#minification)が自動的に試行され、それによって変更されます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-426">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="c8a07-427">場合によっては、このような機能を無効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-427">You may need to disable such features.</span></span>

<span data-ttu-id="c8a07-428">これらのうちどれが自分のケースに当てはまるか診断するには:</span><span class="sxs-lookup"><span data-stu-id="c8a07-428">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="c8a07-429">エラー メッセージを確認し、エラーをトリガーしているファイルをメモします。</span><span class="sxs-lookup"><span data-stu-id="c8a07-429">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="c8a07-430">ブラウザーの開発者ツールを開き、 *[ネットワーク]* タブを確認します。必要に応じて、ページを再度読み込み、要求と応答の一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-430">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="c8a07-431">その一覧でエラーをトリガーしているファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-431">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="c8a07-432">応答に含まれる HTTP 状態コードを確認します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-432">Check the HTTP status code in the response.</span></span> <span data-ttu-id="c8a07-433">サーバーから *200 - OK* (または別の 2xx 状態コード) 以外のものが返された場合は、サーバー側に診断すべき問題があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-433">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="c8a07-434">たとえば、状態コード 403 は承認に関する問題があることを意味しますが、状態コード 500 は、サーバーが未指定のエラーで失敗していることを意味します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-434">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="c8a07-435">サーバー側のログを参照してアプリを診断し、修正してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-435">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="c8a07-436">リソースに対する状態コードが *200 - OK* の場合は、ブラウザーの開発者ツールで応答の内容を確認し、その内容が予想されるデータと一致していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-436">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matchs up with the data expected.</span></span> <span data-ttu-id="c8a07-437">たとえば、よくある問題は、他のファイルに対しても要求から `index.html` データが返されるように、ルーティングを誤って構成してしまうことです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-437">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="c8a07-438">`.wasm` 要求への応答が WebAssembly であり、`.dll` 要求への応答が .NET アセンブリ バイナリであることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-438">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="c8a07-439">そうでない場合、サーバー側のルーティングに関する問題を診断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-439">If not, you have a server-side routing problem to diagnose.</span></span>

<span data-ttu-id="c8a07-440">サーバーから正しいと思われるデータが返されていることを確認した場合は、ファイルのビルドと配布の間で何か別の原因によって内容が変更されています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-440">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="c8a07-441">これを調査するには:</span><span class="sxs-lookup"><span data-stu-id="c8a07-441">To investigate this:</span></span>

 * <span data-ttu-id="c8a07-442">ファイルがビルドされた後にファイルが変更される場合に備えて、ビルド ツールチェーンと展開メカニズムを調べます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-442">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="c8a07-443">この例としては、前に説明したように、Git によってファイルの改行コードが変換される場合が挙げられます。</span><span class="sxs-lookup"><span data-stu-id="c8a07-443">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="c8a07-444">応答を動的に変更する (たとえば、HTML を縮小しようとする) ように設定されている場合があるため、Web サーバーまたは CDN の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-444">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="c8a07-445">Web サーバーに HTTP 圧縮が実装されていても問題ありません (たとえば `content-encoding: br` や `content-encoding: gzip` が返される場合)。これは展開後の結果には影響しないためです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-445">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="c8a07-446">ただし、Web サーバーによって圧縮されていないデータが変更される場合は "*問題があります*"。</span><span class="sxs-lookup"><span data-stu-id="c8a07-446">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="c8a07-447">非 PWA アプリの整合性チェックを無効にする</span><span class="sxs-lookup"><span data-stu-id="c8a07-447">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="c8a07-448">ほとんどの場合、整合性チェックを無効にしないでください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-448">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="c8a07-449">整合性チェックを無効にしても、予期しない応答の原因となった根本的な問題は解決されず、前述の利点が失われる結果になります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-449">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="c8a07-450">Web サーバーから一貫した応答が返されるとは限らないため、整合性チェックを無効にするしかないケースがあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-450">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="c8a07-451">整合性チェックを無効にするには、Blazor WebAssembly プロジェクトの `.csproj` ファイル内のプロパティ グループに次を追加します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-451">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="c8a07-452">`BlazorCacheBootResources` により、SHA-256 ハッシュに基づいて `.dll`、`.wasm`、およびその他のファイルをキャッシュする Blazor の既定の動作も無効になります。このプロパティによって、SHA-256 ハッシュの正確性を信頼できないことが指定されるためです。</span><span class="sxs-lookup"><span data-stu-id="c8a07-452">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="c8a07-453">この設定を使用しても、ブラウザーの通常の HTTP キャッシュによってこれらのファイルがキャッシュされる可能性がありますが、このような状況が発生するかどうかは、Web サーバーの構成と、それによって提供される `cache-control` ヘッダーによって異なります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-453">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="c8a07-454">`BlazorCacheBootResources` プロパティによって[プログレッシブ Web アプリケーション (PWA)](xref:blazor/progressive-web-app) の整合性チェックが無効になることはありません。</span><span class="sxs-lookup"><span data-stu-id="c8a07-454">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="c8a07-455">PWA に関連するガイダンスについては、「[PWA の整合性チェックを無効にする](#disable-integrity-checking-for-pwas)」セクションをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c8a07-455">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="c8a07-456">PWA の整合性チェックを無効にする</span><span class="sxs-lookup"><span data-stu-id="c8a07-456">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="c8a07-457">Blazor のプログレッシブ Web アプリケーション (PWA) テンプレートには、オフライン使用のためにアプリケーション ファイルをフェッチおよび格納するための推奨される `service-worker.published.js` ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-457">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="c8a07-458">これは通常のアプリの起動メカニズムとは別のプロセスであり、独自の整合性チェック ロジックを備えています。</span><span class="sxs-lookup"><span data-stu-id="c8a07-458">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="c8a07-459">`service-worker.published.js` ファイル内に、次の行があります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-459">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="c8a07-460">整合性チェックを無効にするには、行を次のように変更して `integrity` パラメーターを削除します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-460">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="c8a07-461">ここでも、整合性チェックを無効にすることは、整合性チェックによって提供される安全性の保証が失われることを意味します。</span><span class="sxs-lookup"><span data-stu-id="c8a07-461">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="c8a07-462">たとえば、ユーザーのブラウザーでアプリをキャッシュしている瞬間に、新しいバージョンを展開した場合、古い展開から一部のファイルがキャッシュされ、新しい展開から別のファイルがキャッシュされるリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-462">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="c8a07-463">そのような場合、さらなる更新プログラムを展開するまで、アプリは破損した状態のままになります。</span><span class="sxs-lookup"><span data-stu-id="c8a07-463">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
