---
title: ASP.NET Core Blazor WebAssembly のホストと展開
author: guardrex
description: ASP.NET Core、Content Delivery Networks (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2020
no-loc:
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
ms.openlocfilehash: 06059e0f9ff6a3f4073d8d01d1ac541c30ad1ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014192"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="6a35f-103">ASP.NET Core Blazor WebAssembly のホストと展開</span><span class="sxs-lookup"><span data-stu-id="6a35f-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6a35f-104">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="6a35f-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="6a35f-105">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用する場合は以下のようになります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="6a35f-106">Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="6a35f-107">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="6a35f-108">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="6a35f-109">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="6a35f-110">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="6a35f-111">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="6a35f-112">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="6a35f-113">[圧縮]</span><span class="sxs-lookup"><span data-stu-id="6a35f-113">Compression</span></span>

<span data-ttu-id="6a35f-114">Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="6a35f-115">次の圧縮アルゴリズムが使用されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="6a35f-116">[Brotli](https://tools.ietf.org/html/rfc7932) (最高レベル)</span><span class="sxs-lookup"><span data-stu-id="6a35f-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="6a35f-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="6a35f-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="6a35f-118">Blazor は、適切な圧縮ファイルにサービスを提供するため、ホストに依存します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="6a35f-119">ASP.NET Core でホストするプロジェクトを使用するとき、ホスト プロジェクトでは、コンテント ネゴシエーションを実行したり、静的に圧縮されたファイルにサービスを提供したりできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="6a35f-120">Blazor WebAssembly スタンドアロン アプリをホストするとき、静的に圧縮されたファイルにサービスが提供されるよう、追加の作業が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="6a35f-121">IIS の `web.config` の圧縮構成については、[IIS の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="6a35f-122">GitHub ページなど、静的に圧縮されたファイル コンテント ネゴシエーションをサポートしない静的ホスティング ソリューションでホストするとき、Brotli 圧縮ファイルをフェッチし、デコードするようにアプリを構成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="6a35f-123">[google/brotli GitHub リポジトリ](https://github.com/google/brotli)から、JavaScript Brotli デコーダーを入手します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="6a35f-124">2020 年 7 月の時点で、デコーダー ファイルは `decode.min.js` という名前で、リポジトリの [`js` フォルダー](https://github.com/google/brotli/tree/master/js)にあります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="6a35f-125">そのデコーダーを使用するようにアプリを更新します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-125">Update the app to use the decoder.</span></span> <span data-ttu-id="6a35f-126">`wwwroot/index.html` の終了 `<body>` タグの中にあるマークアップを次に変更します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-126">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="6a35f-127">圧縮を無効にするには、アプリケーションのプロジェクト ファイルに `BlazorEnableCompression` MSBuild プロパティを追加し、値を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="6a35f-128">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="6a35f-128">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="6a35f-129">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor Server (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-129">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="6a35f-130">次の 2 つのコンポーネントがある Blazor WebAssembly アプリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-130">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="6a35f-131">`Main.razor`:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-131">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="6a35f-132">`About.razor`: `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="6a35f-132">`About.razor`: `About` component.</span></span>

<span data-ttu-id="6a35f-133">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="6a35f-133">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="6a35f-134">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-134">The browser makes a request.</span></span>
1. <span data-ttu-id="6a35f-135">既定のページ (通常は `index.html`) が返されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-135">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="6a35f-136">`index.html` によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-136">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="6a35f-137">Blazor のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-137">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="6a35f-138">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-138">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="6a35f-139">" *Blazor WebAssembly アプリ内にある*" 内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-139">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="6a35f-140">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-140">The router handles the requests internally.</span></span>

<span data-ttu-id="6a35f-141">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-141">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="6a35f-142">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-142">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="6a35f-143">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する `index.html` ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-143">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="6a35f-144">`index.html` が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-144">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="6a35f-145">IIS サーバーに展開する場合は、アプリで発行される `web.config` ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-145">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="6a35f-146">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-146">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="6a35f-147">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="6a35f-147">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="6a35f-148">"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-148">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="6a35f-149">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-149">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="6a35f-150">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-150">The two apps are deployed together.</span></span> <span data-ttu-id="6a35f-151">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-151">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="6a35f-152">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([`dotnet new`](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **`Hosted`** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="6a35f-152">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="6a35f-153">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-153">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="6a35f-154">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-154">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="6a35f-155">複数の Blazor WebAssembly アプリによるホストされた展開</span><span class="sxs-lookup"><span data-stu-id="6a35f-155">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="6a35f-156">アプリの構成</span><span class="sxs-lookup"><span data-stu-id="6a35f-156">App configuration</span></span>

<span data-ttu-id="6a35f-157">複数の Blazor WebAssembly アプリを提供するようにホストされた Blazor ソリューションを構成するには:</span><span class="sxs-lookup"><span data-stu-id="6a35f-157">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="6a35f-158">既存のホストされた Blazor ソリューションを使用するか、Blazor のホストされたプロジェクト テンプレートから新しいソリューションを作成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-158">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="6a35f-159">クライアント アプリのプロジェクト ファイルで、値が `FirstApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加して、プロジェクトの静的アセットの基本パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-159">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="6a35f-160">ソリューションに 2 つ目のクライアント アプリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-160">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="6a35f-161">`SecondClient` という名前のフォルダーをソリューションのフォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-161">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="6a35f-162">Blazor WebAssembly プロジェクト テンプレートから `SecondClient` フォルダーに `SecondBlazorApp.Client` という名前の Blazor WebAssembly アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-162">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="6a35f-163">アプリのプロジェクト ファイル内で:</span><span class="sxs-lookup"><span data-stu-id="6a35f-163">In the app's project file:</span></span>

    * <span data-ttu-id="6a35f-164">値が `SecondApp` の `<PropertyGroup>` に `<StaticWebAssetBasePath>` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-164">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="6a35f-165">`Shared` プロジェクトにプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-165">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="6a35f-166">プレースホルダー `{SOLUTION NAME}` は、ソリューションの名前です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-166">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="6a35f-167">サーバー アプリのプロジェクト ファイルで、追加したクライアン トアプリに対するプロジェクト参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-167">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="6a35f-168">サーバー アプリの `Properties/launchSettings.json` ファイルで、ポート 5001 と 5002 でクライアント アプリにアクセスするように、Kestrel プロファイル (`{SOLUTION NAME}.Server`) の `applicationUrl` を構成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-168">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="6a35f-169">サーバー アプリの `Startup.Configure` メソッド (`Startup.cs`) で、<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> の呼び出しの後にある次の行を削除します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-169">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="6a35f-170">要求をクライアント アプリにマップするミドルウェアを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-170">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="6a35f-171">次の例では、ミドルウェアが以下のときに実行されるように構成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-171">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="6a35f-172">要求のポートが、元のクライアント アプリの場合は 5001、追加されたクライアント アプリの場合は 5002 である。</span><span class="sxs-lookup"><span data-stu-id="6a35f-172">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="6a35f-173">要求のホストが、元のクライアント アプリの場合は `firstapp.com`、追加されたクライアント アプリの場合は `secondapp.com` である。</span><span class="sxs-lookup"><span data-stu-id="6a35f-173">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6a35f-174">このセクションで示されている例では、以下に対する追加構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-174">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="6a35f-175">例のホスト ドメイン `firstapp.com` および `secondapp.com` でのアプリへのアクセス。</span><span class="sxs-lookup"><span data-stu-id="6a35f-175">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="6a35f-176">クライアント アプリで TLS セキュリティ (HTTPS) を有効にするための証明書。</span><span class="sxs-lookup"><span data-stu-id="6a35f-176">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="6a35f-177">必要な構成はこの記事の範囲を超えており、ソリューションのホスト方法によって異なります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-177">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="6a35f-178">詳細については、[ホストと展開に関する記事](xref:host-and-deploy/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-178">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="6a35f-179">前に行を削除した場所に、次のコードを配置します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-179">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="6a35f-180">サーバー アプリの天気予報コントローラー (`Controllers/WeatherForecastController.cs`) で、`WeatherForecastController` に対する既存のルート (`[Route("[controller]")]`) を次のルートに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-180">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="6a35f-181">前にサーバー アプリの `Startup.Configure` メソッドに追加したミドルウェアでは、`/WeatherForecast` に対する受信要求が、ポート (5001/5002) またはドメイン (`firstapp.com`/`secondapp.com`) に応じて、`/FirstApp/WeatherForecast` または `/SecondApp/WeatherForecast` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-181">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="6a35f-182">前のコントローラー ルートは、サーバー アプリからクライアント アプリに気象データを返すために必要です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-182">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="6a35f-183">静的アセットとクラス ライブラリ</span><span class="sxs-lookup"><span data-stu-id="6a35f-183">Static assets and class libraries</span></span>

<span data-ttu-id="6a35f-184">静的アセットには、次の方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-184">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="6a35f-185">アセットがクライアント アプリの `wwwroot` フォルダー内にある場合は、通常どおりにパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-185">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="6a35f-186">アセットが [Razor クラス ライブラリ (RCL)](xref:blazor/components/class-libraries) の `wwwroot` フォルダーにある場合は、[RCL の記事](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)のガイダンスに従って、クライアント アプリの静的アセットを参照します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-186">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6a35f-187">クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-187">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="6a35f-188">いずれかのコンポーネントでスタイルシートまたは JavaScript ファイルが必要な場合は、次のいずれかの方法を使用して、静的アセットを取得します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-188">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="6a35f-189">クライアント アプリの `wwwroot/index.html` ファイルでは、静的アセットに (`<link>`) リンクできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-189">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="6a35f-190">コンポーネントでは、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、静的アセットを取得できます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-190">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="6a35f-191">次の例では上記の方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-191">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6a35f-192">クラス ライブラリによってクライアント アプリに提供されるコンポーネントは、通常どおりに参照されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-192">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="6a35f-193">スタイルシートまたは JavaScript ファイルが必要なコンポーネントがある場合は、クライアント アプリの `wwwroot/index.html` ファイルに正しい静的アセットのリンクを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-193">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="6a35f-194">次の例ではこれらの方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-194">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="6a35f-195">次の `Jeep` コンポーネントをクライアント アプリのいずれかに追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-195">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="6a35f-196">`Jeep` コンポーネントでは次のものが使用されています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-196">The `Jeep` component uses:</span></span>

* <span data-ttu-id="6a35f-197">クライアント アプリの `wwwroot` フォルダーにある画像 (`jeep-cj.png`)。</span><span class="sxs-lookup"><span data-stu-id="6a35f-197">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="6a35f-198">[追加された Razor コンポーネント ライブラリ](xref:blazor/components/class-libraries) (`JeepImage`) の `wwwroot` フォルダーにある画像 (`jeep-yj.png`)。</span><span class="sxs-lookup"><span data-stu-id="6a35f-198">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="6a35f-199">この例のコンポーネント (`Component1`) は、`JeepImage` ライブラリがソリューションに追加されると、RCL プロジェクト テンプレートによって自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-199">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="6a35f-200">画像を所有している場合を除き、車両の画像を公開**しないでください**。</span><span class="sxs-lookup"><span data-stu-id="6a35f-200">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="6a35f-201">そうしないと、著作権侵害のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-201">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6a35f-202">ライブラリの `jeep-yj.png` 画像を、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-202">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="6a35f-203">`my-component` CSS クラスをクライアント アプリのページに提供するには、フレームワークの [`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用して、ライブラリのスタイルシートにリンクします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-203">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="6a35f-204">[`Link` コンポーネント](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)を使用する代わりに、クライアント アプリの `wwwroot/index.html` ファイルからスタイルシートを読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-204">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="6a35f-205">この方法を使用すると、クライアント アプリのすべてのコンポーネントでスタイルシートを使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-205">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="6a35f-206">ライブラリの `jeep-yj.png` 画像は、ライブラリの `Component1` コンポーネント (`Component1.razor`) に追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="6a35f-207">クライアント アプリの `wwwroot/index.html` ファイルでは、次の `<link>` タグを追加してライブラリのスタイルシートを要求します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-207">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="6a35f-208">クライアント アプリの `NavMenu` コンポーネント (`Shared/NavMenu.razor`) に、`Jeep` コンポーネントへのナビゲーションを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-208">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="6a35f-209">RCL の詳細については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-209">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="6a35f-210">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="6a35f-210">Standalone deployment</span></span>

<span data-ttu-id="6a35f-211">"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-211">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="6a35f-212">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-212">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="6a35f-213">スタンドアロンのデプロイ アセットは、`/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-213">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="6a35f-214">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6a35f-214">Azure App Service</span></span>

<span data-ttu-id="6a35f-215">Blazor WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-215">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="6a35f-216">スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-216">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="6a35f-217">現時点では、アプリをホストする Linux サーバー イメージは使用できません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-217">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="6a35f-218">このシナリオを可能にするための取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-218">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="6a35f-219">IIS</span><span class="sxs-lookup"><span data-stu-id="6a35f-219">IIS</span></span>

<span data-ttu-id="6a35f-220">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-220">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="6a35f-221">Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-221">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="6a35f-222">発行されたアセットは、`/bin/Release/{TARGET FRAMEWORK}/publish` フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-222">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="6a35f-223">`publish` フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-223">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="6a35f-224">web.config</span><span class="sxs-lookup"><span data-stu-id="6a35f-224">web.config</span></span>

<span data-ttu-id="6a35f-225">Blazor プロジェクトが発行されると、`web.config` ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-225">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="6a35f-226">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-226">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="6a35f-227">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="6a35f-227">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="6a35f-228">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="6a35f-228">`.json`: `application/json`</span></span>
  * <span data-ttu-id="6a35f-229">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="6a35f-229">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="6a35f-230">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6a35f-230">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="6a35f-231">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="6a35f-231">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="6a35f-232">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-232">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="6a35f-233">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-233">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="6a35f-234">アプリの静的なアセットが存在するサブディレクトリ (`wwwroot/{PATH REQUESTED}`) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-234">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="6a35f-235">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント (`wwwroot/index.html`) にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-235">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="6a35f-236">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="6a35f-236">Use a custom web.config</span></span>

<span data-ttu-id="6a35f-237">カスタムの `web.config` ファイルを使用するには、カスタムの `web.config` ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-237">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="6a35f-238">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="6a35f-238">Install the URL Rewrite Module</span></span>

<span data-ttu-id="6a35f-239">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-239">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="6a35f-240">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-240">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="6a35f-241">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-241">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="6a35f-242">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-242">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="6a35f-243">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-243">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="6a35f-244">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-244">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="6a35f-245">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-245">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="6a35f-246">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-246">Copy the installer to the server.</span></span> <span data-ttu-id="6a35f-247">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-247">Run the installer.</span></span> <span data-ttu-id="6a35f-248">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-248">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="6a35f-249">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-249">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="6a35f-250">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="6a35f-250">Configure the website</span></span>

<span data-ttu-id="6a35f-251">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-251">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="6a35f-252">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-252">The folder contains:</span></span>

* <span data-ttu-id="6a35f-253">`web.config` ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-253">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="6a35f-254">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="6a35f-254">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="6a35f-255">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="6a35f-255">Host as an IIS sub-app</span></span>

<span data-ttu-id="6a35f-256">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-256">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="6a35f-257">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-257">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="6a35f-258">Blazor アプリで発行された `web.config` ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-258">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="6a35f-259">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-259">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="6a35f-260">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:blazor/host-and-deploy/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-260">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="6a35f-261">IIS でサブアプリを構成するときに、アプリの `index.html` ファイル内のアプリのベース パスを、使用している IIS の別名に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-261">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="6a35f-262">Brotli と Gzip の圧縮</span><span class="sxs-lookup"><span data-stu-id="6a35f-262">Brotli and Gzip compression</span></span>

<span data-ttu-id="6a35f-263">`web.config` を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-263">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="6a35f-264">構成例については、[`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-264">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="6a35f-265">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6a35f-265">Troubleshooting</span></span>

<span data-ttu-id="6a35f-266">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-266">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="6a35f-267">モジュールがインストールされていない場合、IIS では `web.config` ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-267">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="6a35f-268">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-268">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="6a35f-269">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-269">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="6a35f-270">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="6a35f-270">Azure Storage</span></span>

<span data-ttu-id="6a35f-271">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-271">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="6a35f-272">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-272">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="6a35f-273">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="6a35f-273">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="6a35f-274">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-274">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="6a35f-275">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-275">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="6a35f-276">Razor コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-276">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="6a35f-277">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-277">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="6a35f-278">`index.html` BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-278">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="6a35f-279">ファイルの `Content-Type` ヘッダーに不適切な MIME の種類があるために、実行時にファイルが読み込まれない場合は、次のいずれかの操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-279">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="6a35f-280">ファイルの展開時に適切な MIME の種類 (`Content-Type` ヘッダー) を設定するようにツールを構成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-280">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="6a35f-281">アプリの展開後に、ファイルの MIME の種類 (`Content-Type` ヘッダー) を変更します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-281">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="6a35f-282">Storage Explorer (Azure portal) で、ファイルごとに次のようにします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-282">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="6a35f-283">ファイルを右クリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-283">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="6a35f-284">**ContentType** を設定し、 **[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-284">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="6a35f-285">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-285">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="6a35f-286">Nginx</span><span class="sxs-lookup"><span data-stu-id="6a35f-286">Nginx</span></span>

<span data-ttu-id="6a35f-287">以下に示す `nginx.conf` ファイルは、Nginx が対応するファイルをディスク上で見つけられないときに `index.html` ファイルを送信するよう Nginx を構成する方法を示すために、簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-287">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="6a35f-288">[NGINX バースト レート制限](https://www.nginx.com/blog/rate-limiting-nginx/#bursts)を [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) で設定するとき、アプリによって行われる比較的大量の要求を受け入れる目的で、場合によっては、Blazor WebAssembly アプリの `burst` パラメーター値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-288">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="6a35f-289">最初に、値を 60 以上に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-289">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="6a35f-290">"*503 - サービスを利用できません*" という状態コードを要求が受信していることがブラウザー開発者ツールまたはネットワーク トラフィック ツールで示されている場合、この値を増やします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-290">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="6a35f-291">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-291">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="6a35f-292">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="6a35f-292">Nginx in Docker</span></span>

<span data-ttu-id="6a35f-293">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-293">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="6a35f-294">`nginx.config` ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-294">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="6a35f-295">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-295">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="6a35f-296">Apache</span><span class="sxs-lookup"><span data-stu-id="6a35f-296">Apache</span></span>

<span data-ttu-id="6a35f-297">Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:</span><span class="sxs-lookup"><span data-stu-id="6a35f-297">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="6a35f-298">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-298">Create the Apache configuration file.</span></span> <span data-ttu-id="6a35f-299">次の例は、簡略化された構成ファイル (`blazorapp.config`) です。</span><span class="sxs-lookup"><span data-stu-id="6a35f-299">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="6a35f-300">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-300">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="6a35f-301">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-301">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="6a35f-302">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-302">Restart the Apache service.</span></span>

<span data-ttu-id="6a35f-303">詳細については、[`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) および [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-303">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="6a35f-304">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="6a35f-304">GitHub Pages</span></span>

<span data-ttu-id="6a35f-305">URL の書き換えを処理するために、`wwwroot/404.html` ファイルを、要求を `index.html` ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-305">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="6a35f-306">例については、[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-306">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="6a35f-307">[ライブ サイト](https://stevesandersonms.github.io/BlazorOnGitHubPages/)</span><span class="sxs-lookup"><span data-stu-id="6a35f-307">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="6a35f-308">組織のサイトではなくプロジェクトのサイトを使用しているときは、`wwwroot/index.html` 内の `<base>` タグを更新します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-308">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="6a35f-309">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `/my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-309">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="6a35f-310">[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)では、[`.github/workflows/main.yml` 構成ファイル](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)による発行時に、基本 `href` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-310">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="6a35f-311">[SteveSandersonMS/BlazorOnGitHubPages GitHub リポジトリ](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)は、.NET Foundation または Microsoft では、所有、管理、またはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6a35f-311">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="6a35f-312">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="6a35f-312">Host configuration values</span></span>

<span data-ttu-id="6a35f-313">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-313">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="6a35f-314">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="6a35f-314">Content root</span></span>

<span data-ttu-id="6a35f-315">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="6a35f-315">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="6a35f-316">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-316">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="6a35f-317">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-317">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6a35f-318">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-318">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="6a35f-319">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-319">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6a35f-320">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-320">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="6a35f-321">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-321">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6a35f-322">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-322">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="6a35f-323">パス ベース</span><span class="sxs-lookup"><span data-stu-id="6a35f-323">Path base</span></span>

<span data-ttu-id="6a35f-324">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="6a35f-324">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="6a35f-325">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="6a35f-325">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="6a35f-326">詳細については、「[アプリのベースパス](xref:blazor/host-and-deploy/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-326">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6a35f-327">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-327">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="6a35f-328">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="6a35f-328">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="6a35f-329">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-329">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6a35f-330">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-330">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="6a35f-331">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-331">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6a35f-332">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-332">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="6a35f-333">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-333">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6a35f-334">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-334">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="6a35f-335">URL</span><span class="sxs-lookup"><span data-stu-id="6a35f-335">URLs</span></span>

<span data-ttu-id="6a35f-336">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-336">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="6a35f-337">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-337">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="6a35f-338">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-338">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="6a35f-339">**IIS Express** プロファイル内のアプリの `launchSettings.json` ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-339">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="6a35f-340">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-340">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="6a35f-341">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-341">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="6a35f-342">Visual Studio のプロパティ ページで引数を設定すると、その引数が `launchSettings.json` ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-342">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="6a35f-343">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="6a35f-343">Configure the Linker</span></span>

<span data-ttu-id="6a35f-344">Blazor では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-344">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="6a35f-345">詳細については、「<xref:blazor/host-and-deploy/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-345">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="6a35f-346">カスタム ブート リソースの読み込み</span><span class="sxs-lookup"><span data-stu-id="6a35f-346">Custom boot resource loading</span></span>

<span data-ttu-id="6a35f-347">Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-347">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="6a35f-348">次のシナリオで `loadBootResource` を使用します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-348">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="6a35f-349">ユーザーが、タイムゾーン データや `dotnet.wasm` などの静的なリソースを CDN から読み込むことができるようにする。</span><span class="sxs-lookup"><span data-stu-id="6a35f-349">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="6a35f-350">HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。</span><span class="sxs-lookup"><span data-stu-id="6a35f-350">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="6a35f-351">各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="6a35f-351">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="6a35f-352">`loadBootResource` パラメーターは次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-352">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="6a35f-353">パラメーター</span><span class="sxs-lookup"><span data-stu-id="6a35f-353">Parameter</span></span>    | <span data-ttu-id="6a35f-354">説明</span><span class="sxs-lookup"><span data-stu-id="6a35f-354">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="6a35f-355">リソースの型。</span><span class="sxs-lookup"><span data-stu-id="6a35f-355">The type of the resource.</span></span> <span data-ttu-id="6a35f-356">許容される型: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="6a35f-356">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="6a35f-357">リソースの名前。</span><span class="sxs-lookup"><span data-stu-id="6a35f-357">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="6a35f-358">リソースの相対 URI または絶対 URI。</span><span class="sxs-lookup"><span data-stu-id="6a35f-358">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="6a35f-359">応答で予想されるコンテンツを表す整合性文字列。</span><span class="sxs-lookup"><span data-stu-id="6a35f-359">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="6a35f-360">`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-360">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="6a35f-361">URI 文字列。</span><span class="sxs-lookup"><span data-stu-id="6a35f-361">URI string.</span></span> <span data-ttu-id="6a35f-362">次の例 (`wwwroot/index.html`) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-362">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="6a35f-363">タイムゾーン データ</span><span class="sxs-lookup"><span data-stu-id="6a35f-363">Timezone data</span></span>

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

* <span data-ttu-id="6a35f-364">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="6a35f-364">`Promise<Response>`.</span></span> <span data-ttu-id="6a35f-365">ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-365">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="6a35f-366">次の例 (`wwwroot/index.html`) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-366">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="6a35f-367">`null`/`undefined`。既定の読み込み動作になります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-367">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="6a35f-368">外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-368">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="6a35f-369">通常、既定では、必要なヘッダーが CDN によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-369">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="6a35f-370">カスタム動作の型のみ指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6a35f-370">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="6a35f-371">`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-371">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="6a35f-372">DLL ファイルのファイル名拡張子を変更する</span><span class="sxs-lookup"><span data-stu-id="6a35f-372">Change the filename extension of DLL files</span></span>

<span data-ttu-id="6a35f-373">アプリで発行されている `.dll` ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-373">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="6a35f-374">アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように `.dll` ファイルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-374">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="6a35f-375">アプリで発行された出力の `wwwroot` ディレクトリ (たとえば、`{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`) 内の `.dll` ファイルをターゲットにします。</span><span class="sxs-lookup"><span data-stu-id="6a35f-375">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="6a35f-376">次の例では、`.dll` ファイルの名前が `.bin` ファイル拡張子を使用するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-376">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="6a35f-377">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="6a35f-377">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="6a35f-378">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-378">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="6a35f-379">Linux または macOS の場合:</span><span class="sxs-lookup"><span data-stu-id="6a35f-379">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="6a35f-380">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-380">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="6a35f-381">`.bin` とは異なるファイル拡張子を使用するには、前のコマンドで `.bin` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-381">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="6a35f-382">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルに対処するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-382">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="6a35f-383">圧縮された `blazor.boot.json.gz` と `blazor.boot.json.br` のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-383">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="6a35f-384">このアプローチでは、圧縮は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-384">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="6a35f-385">更新した `blazor.boot.json` ファイルを圧縮します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-385">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="6a35f-386">上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-386">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="6a35f-387">`wwwroot/service-worker-assets.js.br` と `wwwroot/service-worker-assets.js.gz` を削除または再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-387">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="6a35f-388">そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-388">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="6a35f-389">次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="6a35f-389">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="6a35f-390">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="6a35f-390">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="6a35f-391">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6a35f-391">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="6a35f-392">プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="6a35f-392">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="6a35f-393">フィードバックを提供するには、[aspnetcore/issue #5477](https://github.com/dotnet/aspnetcore/issues/5477) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6a35f-393">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
