---
title: ASP.NET Core での静的資産のバンドルと縮小
author: scottaddie
description: バンドルと縮小の手法を適用して、ASP.NET Core Web アプリケーションの静的リソースを最適化する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488690"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="9223c-103">ASP.NET Core での静的資産のバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="9223c-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="9223c-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)、[David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="9223c-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="9223c-105">この記事では、ASP.NET Core Web アプリでこれらの機能を使用する方法など、バンドルと縮小を適用する利点について説明します。</span><span class="sxs-lookup"><span data-stu-id="9223c-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="9223c-106">バンドルと縮小とは</span><span class="sxs-lookup"><span data-stu-id="9223c-106">What is bundling and minification</span></span>

<span data-ttu-id="9223c-107">バンドルと縮小は、Web アプリに適用できる 2 種類のパフォーマンス最適化です。</span><span class="sxs-lookup"><span data-stu-id="9223c-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="9223c-108">バンドルと縮小を併用すると、サーバー要求の数が減り、要求される静的資産のサイズが小さくなるため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="9223c-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="9223c-109">バンドルと縮小を使用すると、主に最初のページ要求の読み込み時間が短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="9223c-110">Web ページが要求されると、ブラウザーによって静的資産 (JavaScript、CSS、および画像) がキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="9223c-111">そのため、同じ資産を要求する同じサイト上で、同じページまたは複数のページを要求する場合、バンドルと縮小を使用してもパフォーマンスは改善されません。</span><span class="sxs-lookup"><span data-stu-id="9223c-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="9223c-112">expires ヘッダーが資産に正しく設定されておらず、バンドルと縮小が使用されていない場合、ブラウザーの更新の間隔ヒューリスティックにより、数日後には資産が古くなっているとマークされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="9223c-113">さらに、ブラウザーでは、各資産に対する検証要求が必要です。</span><span class="sxs-lookup"><span data-stu-id="9223c-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="9223c-114">この場合、最初のページ要求の後でも、バンドルと縮小によりパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="9223c-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="9223c-115">バンドル</span><span class="sxs-lookup"><span data-stu-id="9223c-115">Bundling</span></span>

<span data-ttu-id="9223c-116">バンドルでは、複数のファイルを単一のファイルに連結します。</span><span class="sxs-lookup"><span data-stu-id="9223c-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="9223c-117">バンドルを使用すると、Web ページなどの Web 資産のレンダリングに必要なサーバー要求の数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="9223c-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="9223c-118">CSS、JavaScript など専用の個別のバンドルをいくつでも作成できます。ファイルが少なくなると、ブラウザーからサーバーへ、またはアプリケーションを提供するサービスからの HTTP 要求が少なくなることを意味します。</span><span class="sxs-lookup"><span data-stu-id="9223c-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="9223c-119">その結果、最初のページ読み込みのパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="9223c-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="9223c-120">縮小</span><span class="sxs-lookup"><span data-stu-id="9223c-120">Minification</span></span>

<span data-ttu-id="9223c-121">縮小を使用すると、機能を変更せずにコードから不要な文字を削除できます。</span><span class="sxs-lookup"><span data-stu-id="9223c-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="9223c-122">その結果、要求される資産 (CSS、画像、JavaScript ファイルなど) のサイズが大幅に減ります。</span><span class="sxs-lookup"><span data-stu-id="9223c-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="9223c-123">縮小の一般的な副作用として、変数名を 1 文字に短縮する機能や、コメントと不要な空白を削除する機能があります。</span><span class="sxs-lookup"><span data-stu-id="9223c-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="9223c-124">次の JavaScript 関数を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9223c-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="9223c-125">縮小により、関数が次のように短縮されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="9223c-126">コメントと不要な空白の削除に加えて、次のパラメーターと変数名は次のように名前が変更されました。</span><span class="sxs-lookup"><span data-stu-id="9223c-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="9223c-127">元</span><span class="sxs-lookup"><span data-stu-id="9223c-127">Original</span></span> | <span data-ttu-id="9223c-128">名前の変更</span><span class="sxs-lookup"><span data-stu-id="9223c-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="9223c-129">バンドルと縮小の影響</span><span class="sxs-lookup"><span data-stu-id="9223c-129">Impact of bundling and minification</span></span>

<span data-ttu-id="9223c-130">次の表は、個別に資産を読み込む場合とバンドルと縮小を使用する場合の違いを示しています。</span><span class="sxs-lookup"><span data-stu-id="9223c-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="9223c-131">アクション</span><span class="sxs-lookup"><span data-stu-id="9223c-131">Action</span></span> | <span data-ttu-id="9223c-132">バンドルと縮小あり</span><span class="sxs-lookup"><span data-stu-id="9223c-132">With B/M</span></span> | <span data-ttu-id="9223c-133">バンドルと縮小なし</span><span class="sxs-lookup"><span data-stu-id="9223c-133">Without B/M</span></span> | <span data-ttu-id="9223c-134">変更</span><span class="sxs-lookup"><span data-stu-id="9223c-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="9223c-135">ファイル要求</span><span class="sxs-lookup"><span data-stu-id="9223c-135">File Requests</span></span>  | <span data-ttu-id="9223c-136">7</span><span class="sxs-lookup"><span data-stu-id="9223c-136">7</span></span>   | <span data-ttu-id="9223c-137">18</span><span class="sxs-lookup"><span data-stu-id="9223c-137">18</span></span>     | <span data-ttu-id="9223c-138">157%</span><span class="sxs-lookup"><span data-stu-id="9223c-138">157%</span></span>
<span data-ttu-id="9223c-139">転送済み (KB)</span><span class="sxs-lookup"><span data-stu-id="9223c-139">KB Transferred</span></span> | <span data-ttu-id="9223c-140">156</span><span class="sxs-lookup"><span data-stu-id="9223c-140">156</span></span> | <span data-ttu-id="9223c-141">264.68</span><span class="sxs-lookup"><span data-stu-id="9223c-141">264.68</span></span> | <span data-ttu-id="9223c-142">70%</span><span class="sxs-lookup"><span data-stu-id="9223c-142">70%</span></span>
<span data-ttu-id="9223c-143">読み込み時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="9223c-143">Load Time (ms)</span></span> | <span data-ttu-id="9223c-144">885</span><span class="sxs-lookup"><span data-stu-id="9223c-144">885</span></span> | <span data-ttu-id="9223c-145">2360</span><span class="sxs-lookup"><span data-stu-id="9223c-145">2360</span></span>   | <span data-ttu-id="9223c-146">167%</span><span class="sxs-lookup"><span data-stu-id="9223c-146">167%</span></span>

<span data-ttu-id="9223c-147">ブラウザーは、HTTP 要求ヘッダーに関してかなり冗長です。</span><span class="sxs-lookup"><span data-stu-id="9223c-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="9223c-148">送信された合計バイト数メトリックは、バンドル時に大幅に減りました。</span><span class="sxs-lookup"><span data-stu-id="9223c-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="9223c-149">読み込み時間は大幅に改善されていますが、この例はローカルで実行されています。</span><span class="sxs-lookup"><span data-stu-id="9223c-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="9223c-150">ネットワークを経由で転送される資産にバンドルと縮小を使用すると、パフォーマンスが大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="9223c-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="9223c-151">バンドルと縮小の戦略を選択する</span><span class="sxs-lookup"><span data-stu-id="9223c-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="9223c-152">MVC および Razor Pages プロジェクト テンプレートには、JSON 構成ファイルで構成されるバンドルおよび縮小のためのソリューションが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9223c-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="9223c-153">[Grunt](xref:client-side/using-grunt) タスク ランナーなどのサードパーティ ツールの場合、同じタスクを実行するにはもう少し複雑です。</span><span class="sxs-lookup"><span data-stu-id="9223c-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="9223c-154">サードパーティ製のツールは、リンティングや画像の最適化など、バンドルと縮小を超える処理が開発ワークフローに必要な場合に最適です。</span><span class="sxs-lookup"><span data-stu-id="9223c-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="9223c-155">設計時にバンドルと縮小を使用することで、アプリのデプロイ前に縮小されたファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="9223c-156">デプロイ前のバンドルと縮小によって、サーバーの負荷が軽減されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="9223c-157">ただし、設計時にバンドルと縮小を使用するとビルドの複雑さが増すので、静的ファイルでのみ機能することを認識することが重要です。</span><span class="sxs-lookup"><span data-stu-id="9223c-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="9223c-158">バンドルと縮小を構成する</span><span class="sxs-lookup"><span data-stu-id="9223c-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9223c-159">ASP.NET Core 2.0 以前では、MVC および Razor Pages プロジェクト テンプレートには、各バンドルのオプションが定義された *bundleconfig.json* 構成ファイルが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9223c-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9223c-160">ASP.NET Core 2.1 以降では、*bundleconfig.json* という名前の新しい JSON ファイルを MVC または Razor Pages プロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="9223c-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="9223c-161">開始点としてそのファイルに次の JSON を含めます。</span><span class="sxs-lookup"><span data-stu-id="9223c-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="9223c-162">*bundleconfig.json* ファイルには、各バンドルのオプションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="9223c-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="9223c-163">上記の例では、カスタムの JavaScript (*wwwroot/js/site.js*) とスタイルシート (*wwwroot/css/site.css*) ファイルに対して 1 つのバンドル構成が定義されています。</span><span class="sxs-lookup"><span data-stu-id="9223c-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="9223c-164">構成のオプションには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="9223c-164">Configuration options include:</span></span>

* <span data-ttu-id="9223c-165">`outputFileName`:出力するバンドル ファイルの名前。</span><span class="sxs-lookup"><span data-stu-id="9223c-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="9223c-166">*bundleconfig.json* ファイルからの相対パスを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="9223c-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="9223c-167">**必須**</span><span class="sxs-lookup"><span data-stu-id="9223c-167">**required**</span></span>
* <span data-ttu-id="9223c-168">`inputFiles`:まとめてバンドルするファイルの配列。</span><span class="sxs-lookup"><span data-stu-id="9223c-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="9223c-169">これらは、構成ファイルへの相対パスです。</span><span class="sxs-lookup"><span data-stu-id="9223c-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="9223c-170">**省略可能**、\* 値が空の場合、空の出力ファイルになります。</span><span class="sxs-lookup"><span data-stu-id="9223c-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="9223c-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) パラメーターがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="9223c-172">`minify`:出力の種類の縮小オプション。</span><span class="sxs-lookup"><span data-stu-id="9223c-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="9223c-173">**省略可能**、*既定値 - `minify: { enabled: true }`*</span><span class="sxs-lookup"><span data-stu-id="9223c-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="9223c-174">構成オプションは、出力ファイルの種類ごとに使用できます。</span><span class="sxs-lookup"><span data-stu-id="9223c-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="9223c-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="9223c-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="9223c-176">JavaScript Minifier</span><span class="sxs-lookup"><span data-stu-id="9223c-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="9223c-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="9223c-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="9223c-178">`includeInProject`:生成されたファイルをプロジェクト ファイルに追加するかどうかを示すフラグ。</span><span class="sxs-lookup"><span data-stu-id="9223c-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="9223c-179">**省略可能**、*既定値 - false*</span><span class="sxs-lookup"><span data-stu-id="9223c-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="9223c-180">`sourceMap`:バンドルされたファイルのソース マップを生成するかどうかを示すフラグ。</span><span class="sxs-lookup"><span data-stu-id="9223c-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="9223c-181">**省略可能**、*既定値 - false*</span><span class="sxs-lookup"><span data-stu-id="9223c-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="9223c-182">`sourceMapRootPath`:生成されたソース マップ ファイルを格納するためのルート パス。</span><span class="sxs-lookup"><span data-stu-id="9223c-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="9223c-183">ワークフローにファイルを追加する</span><span class="sxs-lookup"><span data-stu-id="9223c-183">Add files to workflow</span></span>

<span data-ttu-id="9223c-184">次のような新しい *custom.css* ファイルが追加される例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="9223c-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="9223c-185">*custom.css* を縮小し、それと *site.css* を *site.min.css* ファイルにバンドルするには、相対パスを *bundleconfig.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="9223c-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="9223c-186">または、次の glob パターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9223c-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="9223c-187">この glob パターンはすべての CSS ファイルを照合し、縮小されたファイル パターンは除外されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="9223c-188">アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9223c-188">Build the application.</span></span> <span data-ttu-id="9223c-189">*site.min.css* を開き、ファイルの最後に *custom.css* のコンテンツが追加されていることに注意します。</span><span class="sxs-lookup"><span data-stu-id="9223c-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="9223c-190">環境ベースのバンドルと縮小</span><span class="sxs-lookup"><span data-stu-id="9223c-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="9223c-191">ベスト プラクティスとして、アプリのバンドルおよび縮小されたファイルを運用環境で使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9223c-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="9223c-192">開発中は、元のファイルがあるので、アプリのデバッグが容易になります。</span><span class="sxs-lookup"><span data-stu-id="9223c-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="9223c-193">ビューで [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) を使用して、ページに含めるファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="9223c-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="9223c-194">Environment Tag Helper を使用すると、特定の[環境](xref:fundamentals/environments)で実行されている場合にのみコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="9223c-195">`Development` 環境で実行されている場合、次の `environment` タグを使用すると、未処理の CSS ファイルがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="9223c-196">`Development` 以外の環境で実行されている場合に、次の `environment` タグを使用すると、バンドルおよび縮小された CSS ファイルがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="9223c-197">たとえば、`Production` または `Staging` で実行すると、これらのスタイルシートのレンダリングがトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="9223c-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="9223c-198">Gulp から bundleconfig.json を使用する</span><span class="sxs-lookup"><span data-stu-id="9223c-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="9223c-199">アプリのバンドルおよび縮小ワークフローで追加の処理が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="9223c-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="9223c-200">たとえば、画像の最適化、キャッシュ バスティング、CDN 資産の処理などです。</span><span class="sxs-lookup"><span data-stu-id="9223c-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="9223c-201">これらの要件を満たすために、Gulp を使用するようにバンドルおよび縮小ワークフローを変換できます。</span><span class="sxs-lookup"><span data-stu-id="9223c-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="9223c-202">Gulp を使用するようにバンドルおよび縮小ワークフローを手動で変換できます。</span><span class="sxs-lookup"><span data-stu-id="9223c-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="9223c-203">次の `devDependencies` を使用して *package.json* ファイルをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="9223c-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="9223c-204">`gulp-uglify` モジュールは、ECMAScript (ES) 2015 / ES6 以降をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9223c-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="9223c-205">ES2015 / ES6 以降を使用するには、`gulp-uglify` ではなく [gulp-terser](https://www.npmjs.com/package/gulp-terser) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9223c-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="9223c-206">*package.json* と同じレベルで次のコマンドを実行して、依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9223c-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="9223c-207">グローバルな依存関係として Gulp CLI をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9223c-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="9223c-208">次の *gulpfile.js* ファイルをプロジェクトのルートにコピーします。</span><span class="sxs-lookup"><span data-stu-id="9223c-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="9223c-209">Gulp タスクを実行する</span><span class="sxs-lookup"><span data-stu-id="9223c-209">Run Gulp tasks</span></span>

<span data-ttu-id="9223c-210">Visual Studio でプロジェクトをビルドする前に Gulp 縮小タスクをトリガーするには、次の [MSBuild ターゲット](/visualstudio/msbuild/msbuild-targets)を \*.csproj ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="9223c-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="9223c-211">この例では、`MyPreCompileTarget` ターゲット内で定義されたタスクは、事前に定義された `Build` ターゲットの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="9223c-212">次のような出力が Visual Studio の出力ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9223c-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="9223c-213">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="9223c-213">Additional resources</span></span>

* [<span data-ttu-id="9223c-214">Grunt の使用</span><span class="sxs-lookup"><span data-stu-id="9223c-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="9223c-215">複数の環境の使用</span><span class="sxs-lookup"><span data-stu-id="9223c-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="9223c-216">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="9223c-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
