---
<span data-ttu-id="8a363-101">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-103">'Blazor'</span></span>
- <span data-ttu-id="8a363-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-104">'Identity'</span></span>
- <span data-ttu-id="8a363-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-106">'Razor'</span></span>
- <span data-ttu-id="8a363-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-107">'SignalR' uid:</span></span> 

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="8a363-108">ASP.NET Core Blazor WebAssembly をホストしてデプロイする</span><span class="sxs-lookup"><span data-stu-id="8a363-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="8a363-109">作成者: [Luke Latham](https://github.com/guardrex)、[Rainer Stropek](https://www.timecockpit.com)、[Daniel Roth](https://github.com/danroth27)、[Ben Adams](https://twitter.com/ben_a_adams)、[Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="8a363-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="8a363-110">[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)を使用すると、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="8a363-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="8a363-111">Blazor アプリ、その依存関係、.NET ランタイムが並行してブラウザーにダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="8a363-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="8a363-112">アプリがブラウザー UI スレッド上で直接実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="8a363-113">次の展開戦略がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8a363-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="8a363-114">Blazor アプリは、ASP.NET Core アプリによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="8a363-115">この戦略については、「[ASP.NET Core でのホストされた展開](#hosted-deployment-with-aspnet-core)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="8a363-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="8a363-116">Blazor アプリは、Blazor アプリの提供に .NET が使用されていない静的ホスティング Web サーバーまたはサービス上に配置されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="8a363-117">この戦略については、「[スタンドアロン展開](#standalone-deployment)」セクションで示されます。これには、Blazor WebAssembly アプリを IIS サブアプリとしてホストする方法についての情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a363-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="8a363-118">Brotli 事前圧縮</span><span class="sxs-lookup"><span data-stu-id="8a363-118">Brotli precompression</span></span>

<span data-ttu-id="8a363-119">Blazor WebAssembly アプリが公開されると、最上位レベルの [Brotli 圧縮アルゴリズム](https://tools.ietf.org/html/rfc7932)を使用して、出力が事前圧縮されます。このことで、アプリのサイズが縮小され、実行時の圧縮が不要になります。</span><span class="sxs-lookup"><span data-stu-id="8a363-119">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="8a363-120">IIS の *web.config* の圧縮構成については、[「IIS」の「Brotli と Gzip の圧縮」](#brotli-and-gzip-compression)セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-120">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="8a363-121">正しいルーティングのために URL を書き換える</span><span class="sxs-lookup"><span data-stu-id="8a363-121">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="8a363-122">Blazor WebAssembly アプリ内のページ コンポーネントに対するルーティング要求は、Blazor サーバー (ホストされているアプリ) でのルーティング要求のように単純なものではありません。</span><span class="sxs-lookup"><span data-stu-id="8a363-122">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="8a363-123">2 つのコンポーネントを含む Blazor WebAssembly を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="8a363-123">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="8a363-124">*Main.razor*:アプリのルートで読み込まれ、`About` コンポーネントへのリンク (`href="About"`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8a363-124">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="8a363-125">*About.razor*: `About` コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="8a363-125">*About.razor*: `About` component.</span></span>

<span data-ttu-id="8a363-126">アプリの既定のドキュメントがブラウザーのアドレス バー (例: `https://www.contoso.com/`) を使用して要求された場合:</span><span class="sxs-lookup"><span data-stu-id="8a363-126">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="8a363-127">ブラウザーにより要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-127">The browser makes a request.</span></span>
1. <span data-ttu-id="8a363-128">既定のページ (通常は *index.html*) が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-128">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="8a363-129">*index.html* によりアプリがブートストラップされます。</span><span class="sxs-lookup"><span data-stu-id="8a363-129">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="8a363-130"> のルーターが読み込まれて、Razor `Main` コンポーネントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-130">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="8a363-131">Main ページでは、`About` コンポーネントへのリンクの選択がクライアント上で動作します。Blazor のルーターにより、インターネット上で `www.contoso.com` に `About` を求めるブラウザーの要求が停止され、レンダリングされた `About` コンポーネント自体が提供されるためです。</span><span class="sxs-lookup"><span data-stu-id="8a363-131">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="8a363-132">" *Blazor WebAssembly アプリ*" 内にある内部エンドポイントへの要求は、すべて同じように動作します。要求によって、サーバーにホストされているインターネット上のリソースに対するブラウザーベースの要求がトリガーされることはありません。</span><span class="sxs-lookup"><span data-stu-id="8a363-132">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="8a363-133">要求は、ルーターによって内部的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-133">The router handles the requests internally.</span></span>

<span data-ttu-id="8a363-134">ブラウザーのアドレス バーを使用して `www.contoso.com/About` の要求が行われた場合、その要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8a363-134">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="8a363-135">アプリのインターネット ホスト上にそのようなリソースは存在しないため、"*404 見つかりません*" という応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-135">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="8a363-136">ブラウザーではクライアント側ページの要求がインターネットベースのホストに対して行われるため、Web サーバーとホスティング サービスでは、サーバー上に物理的に存在しないリソースに対する *index.html* ページへのすべての要求を、書き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a363-136">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="8a363-137">*index.html* が返されると、アプリの Blazor ルーターがそれを受け取り、正しいリソースで応答します。</span><span class="sxs-lookup"><span data-stu-id="8a363-137">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="8a363-138">IIS サーバーに展開する場合は、アプリの発行される *web.config* ファイルで URL Rewrite Module を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8a363-138">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="8a363-139">詳細については、「[IIS](#iis)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-139">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="8a363-140">ASP.NET Core でのホストされた展開</span><span class="sxs-lookup"><span data-stu-id="8a363-140">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="8a363-141">"*ホストされたデプロイ*" により、Blazor WebAssembly アプリが、Web サーバー上で実行されている [ASP.NET Core アプリ](xref:index)からブラウザーに提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-141">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="8a363-142">クライアント Blazor WebAssembly アプリは、サーバー アプリの他の静的な Web アセットと共に、サーバー アプリの " */bin/Release/{ターゲット フレームワーク}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-142">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="8a363-143">2 つのアプリが一緒に展開されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-143">The two apps are deployed together.</span></span> <span data-ttu-id="8a363-144">ASP.NET Core アプリをホストできる Web サーバーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8a363-144">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8a363-145">ホストされている展開の場合、Visual Studio には **Blazor WebAssembly アプリ** プロジェクト テンプレートが含まれており ([dotnet new](/dotnet/core/tools/dotnet-new) コマンドを使用する場合は `blazorwasm` テンプレート)、 **[ホスト]** オプションが選択されています (`dotnet new` コマンドを使用する場合は `-ho|--hosted`)。</span><span class="sxs-lookup"><span data-stu-id="8a363-145">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="8a363-146">ASP.NET Core アプリでのホストと展開の詳細については、「<xref:host-and-deploy/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-146">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="8a363-147">Azure App Service の展開については、「<xref:tutorials/publish-to-azure-webapp-using-vs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-147">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="8a363-148">スタンドアロン展開</span><span class="sxs-lookup"><span data-stu-id="8a363-148">Standalone deployment</span></span>

<span data-ttu-id="8a363-149">"*スタンドアロン デプロイ*" により、Blazor WebAssembly アプリが、クライアントによって直接要求される静的ファイルのセットとして提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-149">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="8a363-150">任意の静的ファイル サーバーで Blazor アプリを提供できます。</span><span class="sxs-lookup"><span data-stu-id="8a363-150">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="8a363-151">スタンドアロン展開のアセットは " */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot*" フォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-151">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="8a363-152">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8a363-152">Azure App Service</span></span>

Blazor<span data-ttu-id="8a363-153"> WebAssembly アプリは、[IIS](#iis) 上でアプリをホストするために使用される Windows 上の Azure App Service にデプロイできます。</span><span class="sxs-lookup"><span data-stu-id="8a363-153"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="8a363-154">スタンドアロンの Blazor WebAssembly アプリを Azure App Service for Linux にデプロイすることは、現在サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8a363-154">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="8a363-155">現時点では、アプリをホストする Linux サーバー イメージは使用できません。</span><span class="sxs-lookup"><span data-stu-id="8a363-155">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="8a363-156">このシナリオを可能にするための取り組みが進行中です。</span><span class="sxs-lookup"><span data-stu-id="8a363-156">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="8a363-157">IIS</span><span class="sxs-lookup"><span data-stu-id="8a363-157">IIS</span></span>

<span data-ttu-id="8a363-158">IIS は、Blazor アプリ対応の静的ファイル サーバーです。</span><span class="sxs-lookup"><span data-stu-id="8a363-158">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="8a363-159">Blazor をホストするよう IIS を構成する方法については、「[IIS で静的 Web サイトを構築する](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-159">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="8a363-160">発行された資産は、 */bin/Release/<ターゲット フレームワーク>/publish* フォルダーに作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-160">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="8a363-161">*publish*フォルダーのコンテンツを、Web サーバーまたはホスティング サービス上でホストします。</span><span class="sxs-lookup"><span data-stu-id="8a363-161">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="8a363-162">web.config</span><span class="sxs-lookup"><span data-stu-id="8a363-162">web.config</span></span>

<span data-ttu-id="8a363-163">Blazor プロジェクトが発行されると、*web.config* ファイルが以下の IIS 構成で作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-163">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="8a363-164">各ファイル拡張子に対して設定される MIME の種類は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8a363-164">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="8a363-165">*.dll*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="8a363-165">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="8a363-166">*.json*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="8a363-166">*.json*: `application/json`</span></span>
  * <span data-ttu-id="8a363-167">*.wasm*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="8a363-167">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="8a363-168">*.woff*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8a363-168">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="8a363-169">*.woff2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="8a363-169">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="8a363-170">次の MIME の種類に対しては、HTTP 圧縮が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="8a363-170">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="8a363-171">URL Rewrite Module のルールが確立されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-171">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="8a363-172">アプリの静的なアセットが存在するサブディレクトリ ("*wwwroot/{要求されたパス}* ") が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-172">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="8a363-173">ファイル以外のアセットの要求が、アプリの静的アセット フォルダー内の既定のドキュメント ("*wwwroot/index.html*") にリダイレクトされるように、SPA フォールバック ルーティングが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-173">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="8a363-174">カスタム web.config を使用する</span><span class="sxs-lookup"><span data-stu-id="8a363-174">Use a custom web.config</span></span>

<span data-ttu-id="8a363-175">カスタムの *web.config* ファイルを使用するには、カスタムの *web.config* ファイルをプロジェクト フォルダーのルートに配置し、プロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-175">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="8a363-176">URL リライト モジュールをインストールする</span><span class="sxs-lookup"><span data-stu-id="8a363-176">Install the URL Rewrite Module</span></span>

<span data-ttu-id="8a363-177">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) は、URL の書き換えに必要となります。</span><span class="sxs-lookup"><span data-stu-id="8a363-177">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="8a363-178">このモジュールは既定ではインストールされていません。また、Web サーバー (IIS) の役割サービス機能としてインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8a363-178">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="8a363-179">モジュールは、IIS Web サイトからダウンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a363-179">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="8a363-180">Web Platform Installer を使用してモジュールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="8a363-180">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="8a363-181">ローカルで、[URL Rewrite Module のダウンロード ページ](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)に移動します。</span><span class="sxs-lookup"><span data-stu-id="8a363-181">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="8a363-182">英語版については、**WebPI** を選択して WebPI インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="8a363-182">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="8a363-183">その他の言語版については、サーバーの適切なアーキテクチャ (x86/x64) を選択して、インストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="8a363-183">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="8a363-184">インストーラーをサーバーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="8a363-184">Copy the installer to the server.</span></span> <span data-ttu-id="8a363-185">インストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-185">Run the installer.</span></span> <span data-ttu-id="8a363-186">**[インストール]** ボタンを選択して、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="8a363-186">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="8a363-187">インストールが完了した後、サーバーの再起動は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8a363-187">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="8a363-188">Web サイトを構成する</span><span class="sxs-lookup"><span data-stu-id="8a363-188">Configure the website</span></span>

<span data-ttu-id="8a363-189">Web サイトの**物理パス**をアプリのフォルダーに設定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-189">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="8a363-190">フォルダーには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8a363-190">The folder contains:</span></span>

* <span data-ttu-id="8a363-191">*web.config* ファイル。IIS ではこのファイルを使用して、必要なリダイレクト ルールやファイルのコンテンツの種類など、Web サイトの構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="8a363-191">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="8a363-192">アプリの静的なアセット フォルダー。</span><span class="sxs-lookup"><span data-stu-id="8a363-192">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="8a363-193">IIS サブアプリとしてホストする</span><span class="sxs-lookup"><span data-stu-id="8a363-193">Host as an IIS sub-app</span></span>

<span data-ttu-id="8a363-194">スタンドアロン アプリが IIS サブアプリとしてホストされている場合は、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-194">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="8a363-195">継承された ASP.NET Core モジュール ハンドラーを無効にします。</span><span class="sxs-lookup"><span data-stu-id="8a363-195">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="8a363-196">Blazor アプリの発行された *web.config* ファイル内のハンドラーを、`<handlers>` セクションをファイルに追加することで削除します。</span><span class="sxs-lookup"><span data-stu-id="8a363-196">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="8a363-197">`inheritInChildApplications` が `false` に設定された `<location>` 要素を使用して、ルート (親) アプリの `<system.webServer>` セクションの継承を無効にします。</span><span class="sxs-lookup"><span data-stu-id="8a363-197">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="8a363-198">ハンドラーの削除または継承の無効化は、[アプリの基本パスの構成](xref:host-and-deploy/blazor/index#app-base-path)に加えて行われます。</span><span class="sxs-lookup"><span data-stu-id="8a363-198">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="8a363-199">IIS でサブアプリを構成するときに、アプリの *index.html* ファイル内のアプリのベース パスを、使用している IIS エイリアスに設定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-199">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="8a363-200">Brotli と Gzip の圧縮</span><span class="sxs-lookup"><span data-stu-id="8a363-200">Brotli and Gzip compression</span></span>

<span data-ttu-id="8a363-201">*web.config* を使用して、Brotli または Gzip で圧縮された Blazor アセットを提供するように IIS を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="8a363-201">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="8a363-202">構成の例については、[web.config](webassembly/_samples/web.config?raw=true) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-202">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="8a363-203">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="8a363-203">Troubleshooting</span></span>

<span data-ttu-id="8a363-204">Web サイトの構成にアクセスしようとしたときに、"*500 - 内部サーバー エラー*" という応答が返され、IIS マネージャーによりエラーがスローされた場合は、URL リライト モジュールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8a363-204">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="8a363-205">モジュールがインストールされていない場合、IIS では *web.config* ファイルを解析できません。</span><span class="sxs-lookup"><span data-stu-id="8a363-205">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="8a363-206">これは、IIS マネージャーによる Web サイトの構成の読み込み、そして Web サイトによる Blazor の静的ファイルの提供を阻止するためのものです。</span><span class="sxs-lookup"><span data-stu-id="8a363-206">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="8a363-207">IIS への展開に関するトラブルシューティングの詳細については、「<xref:test/troubleshoot-azure-iis>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-207">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="8a363-208">Azure ストレージ</span><span class="sxs-lookup"><span data-stu-id="8a363-208">Azure Storage</span></span>

<span data-ttu-id="8a363-209">[Azure Storage](/azure/storage/) の静的ファイル ホスティングにより、サーバーレス Blazor アプリ ホスティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="8a363-209">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="8a363-210">カスタム ドメイン名の Azure Content Delivery Network (CDN) と HTTPS がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8a363-210">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="8a363-211">ストレージ アカウントでホスティングされている静的 Web サイトに Blob service サービスが有効になっているとき:</span><span class="sxs-lookup"><span data-stu-id="8a363-211">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="8a363-212">**インデックス ドキュメント名**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-212">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="8a363-213">**エラー ドキュメント パス**を `index.html` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-213">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="8a363-214"> コンポーネントとその他の非ファイル エンドポイントは、Blob service で保管される静的コンテンツの物理パスに置かれません。</span><span class="sxs-lookup"><span data-stu-id="8a363-214"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="8a363-215">このようなリソースの 1 つに対して受け取った要求を Blazor ルーターで処理しなければならないとき、Blob service によって生成された *404 - Not Found* エラーにより、要求が**エラー ドキュメント パス**に転送されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-215">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="8a363-216">*index.html* BLOB が返され、Blazor ルーターでパスが読み込まれ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-216">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="8a363-217">詳細については、「[Azure Storage での静的 Web サイト ホスティング](/azure/storage/blobs/storage-blob-static-website)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-217">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="8a363-218">Nginx</span><span class="sxs-lookup"><span data-stu-id="8a363-218">Nginx</span></span>

<span data-ttu-id="8a363-219">以下に示す *nginx.conf* ファイルは、Nginx が対応するファイルをディスク上で見つけられないときは *index.html* ファイルを送信するよう Nginx を構成する方法を示すために、簡素化したものです。</span><span class="sxs-lookup"><span data-stu-id="8a363-219">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="8a363-220">運用環境での Nginx Web サーバーの構成に関する詳細については、「[Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)」 (NGINX Plus と NGINX 構成ファイルの作成) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-220">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="8a363-221">Docker での Nginx</span><span class="sxs-lookup"><span data-stu-id="8a363-221">Nginx in Docker</span></span>

<span data-ttu-id="8a363-222">Nginx を使用して Docker で Blazor をホストするには、Alpine ベースの Nginx イメージを使用するように Dockerfile をセットアップします。</span><span class="sxs-lookup"><span data-stu-id="8a363-222">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="8a363-223">*nginx.config* ファイルをコンテナーにコピーするように、Dockerfile を更新します。</span><span class="sxs-lookup"><span data-stu-id="8a363-223">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="8a363-224">次の例に示すように、1 つの行を Dockerfile に追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-224">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="8a363-225">Apache</span><span class="sxs-lookup"><span data-stu-id="8a363-225">Apache</span></span>

<span data-ttu-id="8a363-226">Blazor WebAssembly アプリを CentOS 7 以降にデプロイするには:</span><span class="sxs-lookup"><span data-stu-id="8a363-226">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="8a363-227">Apache 構成ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="8a363-227">Create the Apache configuration file.</span></span> <span data-ttu-id="8a363-228">次の例は、簡略化された構成ファイル (*blazorapp.config*) です。</span><span class="sxs-lookup"><span data-stu-id="8a363-228">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="8a363-229">Apache 構成ファイルを `/etc/httpd/conf.d/` ディレクトリ (CentOS 7 の既定の Apache 構成ディレクトリ) に配置します。</span><span class="sxs-lookup"><span data-stu-id="8a363-229">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="8a363-230">アプリのファイルを `/var/www/blazorapp` ディレクトリ (構成ファイルで `DocumentRoot` に指定された場所) に配置します。</span><span class="sxs-lookup"><span data-stu-id="8a363-230">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="8a363-231">Apache サービスを再起動します。</span><span class="sxs-lookup"><span data-stu-id="8a363-231">Restart the Apache service.</span></span>

<span data-ttu-id="8a363-232">詳細については、「[mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html)」と「[mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-232">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="8a363-233">GitHub ページ</span><span class="sxs-lookup"><span data-stu-id="8a363-233">GitHub Pages</span></span>

<span data-ttu-id="8a363-234">URL の書き換えを処理するために、*404.html* ファイルを、要求を *index.html* ページにリダイレクトするスクリプトと共に追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-234">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="8a363-235">コミュニティが提供する実装例については、「[Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/)」(GitHub ページ用の単一ページ アプリ) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-235">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="8a363-236">コミュニティのアプローチの使用例については、[GitHub 上の blazor-demo/blazor-demo.github.io に関するページ](https://github.com/blazor-demo/blazor-demo.github.io) ([ライブ サイト](https://blazor-demo.github.io/)) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-236">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="8a363-237">組織のサイトではなくプロジェクトのサイトを使用している場合、*index.html*内の `<base>` タグを追加または更新します。</span><span class="sxs-lookup"><span data-stu-id="8a363-237">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="8a363-238">`href` 属性の値を、GitHub リポジトリの名前の末尾にスラッシュを付けたもの (例: `my-repository/`) に設定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-238">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8a363-239">ホストの構成値</span><span class="sxs-lookup"><span data-stu-id="8a363-239">Host configuration values</span></span>

<span data-ttu-id="8a363-240">[Blazor WebAssembly アプリ](xref:blazor/hosting-models#blazor-webassembly)では、開発環境での実行時に以下のホスト構成値をコマンドライン引数として受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="8a363-240">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="8a363-241">コンテンツ ルート</span><span class="sxs-lookup"><span data-stu-id="8a363-241">Content root</span></span>

<span data-ttu-id="8a363-242">`--contentroot` 引数では、アプリのコンテンツ ファイルを含むディレクトリへの絶対パスが設定されます ([コンテンツ ルート](xref:fundamentals/index#content-root))。</span><span class="sxs-lookup"><span data-stu-id="8a363-242">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="8a363-243">次の例では、`/content-root-path` はアプリのコンテンツ ルート パスです。</span><span class="sxs-lookup"><span data-stu-id="8a363-243">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="8a363-244">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="8a363-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8a363-245">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="8a363-246">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8a363-247">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリが実行されるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-247">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="8a363-248">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8a363-249">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="8a363-250">パス ベース</span><span class="sxs-lookup"><span data-stu-id="8a363-250">Path base</span></span>

<span data-ttu-id="8a363-251">`--pathbase` 引数により、ルート以外の相対 URL パスでローカルで実行されているアプリに対して、アプリのベース パスを設定することができます (ステージングと運用環境の場合、`<base>` タグ `href` は `/` 以外のパスに設定されます)。</span><span class="sxs-lookup"><span data-stu-id="8a363-251">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="8a363-252">次の例では、`/relative-URL-path` はアプリのパス ベースです。</span><span class="sxs-lookup"><span data-stu-id="8a363-252">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="8a363-253">詳細については、「[アプリのベースパス](xref:host-and-deploy/blazor/index#app-base-path)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-253">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a363-254">`<base>` タグの `href` に指定するパスとは異なり、`--pathbase` 引数値を渡すときはスラッシュ (`/`) を末尾に含めないでください。</span><span class="sxs-lookup"><span data-stu-id="8a363-254">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="8a363-255">`<base>` タグでアプリのベース パスが `<base href="/CoolApp/">` と指定されている場合 (末尾にスラッシュあり)、コマンドライン引数値としては `--pathbase=/CoolApp` を渡してください (末尾にスラッシュなし)。</span><span class="sxs-lookup"><span data-stu-id="8a363-255">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="8a363-256">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="8a363-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8a363-257">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="8a363-258">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-258">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8a363-259">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-259">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="8a363-260">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8a363-261">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-261">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="8a363-262">URL</span><span class="sxs-lookup"><span data-stu-id="8a363-262">URLs</span></span>

<span data-ttu-id="8a363-263">`--urls` 引数では、要求をリッスンするポートとプロトコルを使用して、IP アドレスまたはホスト アドレスが設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-263">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="8a363-264">コマンド プロンプトでアプリをローカルに実行するときに、引数を渡します。</span><span class="sxs-lookup"><span data-stu-id="8a363-264">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="8a363-265">アプリのディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8a363-265">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="8a363-266">**IIS Express** プロファイル内のアプリの *launchSettings.json* ファイルに、エントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-266">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="8a363-267">この設定は、Visual Studio デバッガーを使用し、コマンド プロンプトから `dotnet run` でアプリを実行するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-267">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="8a363-268">Visual Studio の **[プロパティ]**  >  **[デバッグ]**  >  **[アプリケーションの引数]** で、引数を指定します。</span><span class="sxs-lookup"><span data-stu-id="8a363-268">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="8a363-269">Visual Studio のプロパティ ページで引数を設定すると、その引数が *launchSettings.json* ファイルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-269">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="8a363-270">リンカーを構成する</span><span class="sxs-lookup"><span data-stu-id="8a363-270">Configure the Linker</span></span>

Blazor<span data-ttu-id="8a363-271"> では、出力アセンブリから不要な中間言語 (IL) を削除するために、IL リンク設定が各リリース ビルド上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-271"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="8a363-272">詳細については、「<xref:host-and-deploy/blazor/configure-linker>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-272">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="8a363-273">カスタム ブート リソースの読み込み</span><span class="sxs-lookup"><span data-stu-id="8a363-273">Custom boot resource loading</span></span>

<span data-ttu-id="8a363-274">Blazor WebAssembly アプリを `loadBootResource` 関数で初期化して、組み込みのブート リソース読み込みメカニズムをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8a363-274">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="8a363-275">次のシナリオで `loadBootResource` を使用します。</span><span class="sxs-lookup"><span data-stu-id="8a363-275">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="8a363-276">ユーザーが、タイムゾーン データや *dotnet.wasm* などの静的なリソースを CDN から読み込むことができるようにする。</span><span class="sxs-lookup"><span data-stu-id="8a363-276">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="8a363-277">HTTP 要求を使用して圧縮されたアセンブリを読み込み、サーバーからの圧縮コンテンツのフェッチをサポートしていないホストのクライアントに展開する。</span><span class="sxs-lookup"><span data-stu-id="8a363-277">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="8a363-278">各 `fetch` 要求を新しい名前にリダイレクトして、リソースを別の名前に設定する。</span><span class="sxs-lookup"><span data-stu-id="8a363-278">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="8a363-279">`loadBootResource` パラメーターは次の表に表示されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-279">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="8a363-280">パラメーター</span><span class="sxs-lookup"><span data-stu-id="8a363-280">Parameter</span></span>    | <span data-ttu-id="8a363-281">説明</span><span class="sxs-lookup"><span data-stu-id="8a363-281">Description</span></span> |
| ---
<span data-ttu-id="8a363-282">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-282">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-283">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-284">'Blazor'</span></span>
- <span data-ttu-id="8a363-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-285">'Identity'</span></span>
- <span data-ttu-id="8a363-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-287">'Razor'</span></span>
- <span data-ttu-id="8a363-288">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a363-289">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-289">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-290">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-291">'Blazor'</span></span>
- <span data-ttu-id="8a363-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-292">'Identity'</span></span>
- <span data-ttu-id="8a363-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-294">'Razor'</span></span>
- <span data-ttu-id="8a363-295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a363-296">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-296">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-297">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-298">'Blazor'</span></span>
- <span data-ttu-id="8a363-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-299">'Identity'</span></span>
- <span data-ttu-id="8a363-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-301">'Razor'</span></span>
- <span data-ttu-id="8a363-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a363-303">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-303">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-304">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-305">'Blazor'</span></span>
- <span data-ttu-id="8a363-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-306">'Identity'</span></span>
- <span data-ttu-id="8a363-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-308">'Razor'</span></span>
- <span data-ttu-id="8a363-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-309">'SignalR' uid:</span></span> 

<span data-ttu-id="8a363-310">------ | --- title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-310">------ | --- title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-311">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-312">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-312">'Blazor'</span></span>
- <span data-ttu-id="8a363-313">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-313">'Identity'</span></span>
- <span data-ttu-id="8a363-314">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-314">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-315">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-315">'Razor'</span></span>
- <span data-ttu-id="8a363-316">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-316">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a363-317">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-317">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-318">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-319">'Blazor'</span></span>
- <span data-ttu-id="8a363-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-320">'Identity'</span></span>
- <span data-ttu-id="8a363-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-322">'Razor'</span></span>
- <span data-ttu-id="8a363-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8a363-324">title:'ASP.NET Core Blazor WebAssembly をホストしてデプロイする' author: description:'ASP.NET Core、Content Delivery Network (CDN)、ファイル サーバー、GitHub ページを使用して、Blazor アプリをホストしデプロイする方法について説明します。'</span><span class="sxs-lookup"><span data-stu-id="8a363-324">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="8a363-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="8a363-325">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8a363-326">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8a363-326">'Blazor'</span></span>
- <span data-ttu-id="8a363-327">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8a363-327">'Identity'</span></span>
- <span data-ttu-id="8a363-328">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8a363-328">'Let's Encrypt'</span></span>
- <span data-ttu-id="8a363-329">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8a363-329">'Razor'</span></span>
- <span data-ttu-id="8a363-330">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="8a363-330">'SignalR' uid:</span></span> 

<span data-ttu-id="8a363-331">------ | | `type`       | リソースの種類。</span><span class="sxs-lookup"><span data-stu-id="8a363-331">------ | | `type`       | The type of the resource.</span></span> <span data-ttu-id="8a363-332">許容される種類: `assembly`、`pdb`、`dotnetjs`、`dotnetwasm`、`timezonedata` | | `name`       | リソースの名前。</span><span class="sxs-lookup"><span data-stu-id="8a363-332">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` | | `name`       | The name of the resource.</span></span> <span data-ttu-id="8a363-333">| | `defaultUri` | リソースの相対 URI または絶対 URI。</span><span class="sxs-lookup"><span data-stu-id="8a363-333">| | `defaultUri` | The relative or absolute URI of the resource.</span></span> <span data-ttu-id="8a363-334">| | `integrity`  | 応答で予想されるコンテンツを表す整合性文字列。</span><span class="sxs-lookup"><span data-stu-id="8a363-334">| | `integrity`  | The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="8a363-335">`loadBootResource` は読み込みプロセスをオーバーライドするために、次のいずれかを返します。</span><span class="sxs-lookup"><span data-stu-id="8a363-335">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="8a363-336">URI 文字列。</span><span class="sxs-lookup"><span data-stu-id="8a363-336">URI string.</span></span> <span data-ttu-id="8a363-337">次の例 (*wwwroot/index.html*) では、`https://my-awesome-cdn.com/` の CDN から次のファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-337">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="8a363-338">*dotnet.\*.js*</span><span class="sxs-lookup"><span data-stu-id="8a363-338">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="8a363-339">*dotnet.wasm*</span><span class="sxs-lookup"><span data-stu-id="8a363-339">*dotnet.wasm*</span></span>
  * <span data-ttu-id="8a363-340">タイムゾーン データ</span><span class="sxs-lookup"><span data-stu-id="8a363-340">Timezone data</span></span>

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

* <span data-ttu-id="8a363-341">`Promise<Response>`。</span><span class="sxs-lookup"><span data-stu-id="8a363-341">`Promise<Response>`.</span></span> <span data-ttu-id="8a363-342">ヘッダーに `integrity` パラメーターを渡して、既定の整合性チェックの動作を保持します。</span><span class="sxs-lookup"><span data-stu-id="8a363-342">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="8a363-343">次の例 (*wwwroot/index.html*) は、カスタム HTTP ヘッダーを送信要求に追加し、`integrity` パラメーターを経由して `fetch` 呼び出しに渡します。</span><span class="sxs-lookup"><span data-stu-id="8a363-343">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="8a363-344">`null`/`undefined`。既定の読み込み動作になります。</span><span class="sxs-lookup"><span data-stu-id="8a363-344">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="8a363-345">外部ソースは、ブラウザーがクロスオリジンのリソースの読み込みを許可するために必要な CORS ヘッダーを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a363-345">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="8a363-346">通常、既定では、必要なヘッダーが CDN によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-346">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="8a363-347">カスタム動作の型のみ指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a363-347">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="8a363-348">`loadBootResource` に指定されていない型は、既定の読み込み動作に従ってフレームワークによって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8a363-348">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="8a363-349">DLL ファイルのファイル名拡張子を変更する</span><span class="sxs-lookup"><span data-stu-id="8a363-349">Change the filename extension of DLL files</span></span>

<span data-ttu-id="8a363-350">アプリで発行されている " *.dll*" ファイルのファイル名拡張子を変更する必要がある場合は、このセクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="8a363-350">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="8a363-351">アプリを発行した後、シェル スクリプトまたは DevOps ビルド パイプラインを使用して、別のファイル拡張子を使用するように " *.dll*" ファイルの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="8a363-351">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="8a363-352">アプリの発行された出力の "*wwwroot*" ディレクトリ内の " *.dll*" ファイルをターゲットにします (たとえば、" *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*")。</span><span class="sxs-lookup"><span data-stu-id="8a363-352">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="8a363-353">次の例では、" *.dll*" ファイルの名前が " *.bin*" ファイル拡張子を使用するように変更されています。</span><span class="sxs-lookup"><span data-stu-id="8a363-353">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="8a363-354">Windows の場合:</span><span class="sxs-lookup"><span data-stu-id="8a363-354">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="8a363-355">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-355">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="8a363-356">Linux または macOS の場合:</span><span class="sxs-lookup"><span data-stu-id="8a363-356">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="8a363-357">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-357">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="8a363-358">" *.bin*" とは異なるファイル拡張子を使用するには、前のコマンドで " *.bin*" を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8a363-358">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="8a363-359">圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルに対処するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="8a363-359">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="8a363-360">圧縮された "*blazor.boot.json.gz*" と "*blazor.boot.json.br*" のファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="8a363-360">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="8a363-361">このアプローチでは、圧縮は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="8a363-361">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="8a363-362">更新された "*blazor.boot.json*" ファイルを再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8a363-362">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="8a363-363">上記のガイダンスは、サービス ワーカー アセットが使用されている場合にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-363">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="8a363-364">*wwwroot/service-worker-assets.js.br* と *wwwroot/service-worker-assets.js.gz* を削除または再圧縮します。</span><span class="sxs-lookup"><span data-stu-id="8a363-364">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="8a363-365">そうしないと、ブラウザーでのファイルの整合性チェックが失敗します。</span><span class="sxs-lookup"><span data-stu-id="8a363-365">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="8a363-366">次の Windows の例では、プロジェクトのルートに配置された PowerShell スクリプトを使用しています。</span><span class="sxs-lookup"><span data-stu-id="8a363-366">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="8a363-367">"*ChangeDLLExtensions.ps1:* :"</span><span class="sxs-lookup"><span data-stu-id="8a363-367">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="8a363-368">サービス ワーカー アセットも使用されている場合は、次のコマンドを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a363-368">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="8a363-369">プロジェクト ファイルでは、アプリの発行後にスクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a363-369">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="8a363-370">フィードバックを提供するには、[aspnetcore/issue #5477](https://github.com/dotnet/aspnetcore/issues/5477) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8a363-370">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 