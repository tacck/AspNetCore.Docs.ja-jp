---
<span data-ttu-id="734dc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-102">'Blazor'</span></span>
- <span data-ttu-id="734dc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-103">'Identity'</span></span>
- <span data-ttu-id="734dc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-105">'Razor'</span></span>
- <span data-ttu-id="734dc-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="734dc-107">ASP.NET Core の URL リライト ミドルウェア</span><span class="sxs-lookup"><span data-stu-id="734dc-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="734dc-108">作成者: [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="734dc-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="734dc-109">このドキュメントでは、ASP.NET Core アプリの URL リライト ミドルウェアを使用して URL を書き換える手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="734dc-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="734dc-110">URL の書き換えは、1 つまたは複数の事前定義された規則に基づいて URL 要求を変更する操作です。</span><span class="sxs-lookup"><span data-stu-id="734dc-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="734dc-111">URL 書き換えは、リソースの場所とそれらのアドレスの間の抽象化を作成し、場所とアドレスが緊密にリンクされていないようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="734dc-112">URL 書き換えは複数のシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="734dc-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="734dc-113">サーバー リソースを一時的または永続的に移動するか置き換えて、リソースに対する安定したロケーターを維持します。</span><span class="sxs-lookup"><span data-stu-id="734dc-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="734dc-114">要求処理を異なる複数のアプリまたは 1 つのアプリの複数の区分に分割します。</span><span class="sxs-lookup"><span data-stu-id="734dc-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="734dc-115">受信した要求の URL セグメントを削除、追加、または再編成します。</span><span class="sxs-lookup"><span data-stu-id="734dc-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="734dc-116">検索エンジン最適化 (SEO) のためにパブリック URL を最適化します。</span><span class="sxs-lookup"><span data-stu-id="734dc-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="734dc-117">ビジターがリソースの要求から返される内容を予測しやすいように、フレンドリなパブリック URL の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="734dc-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="734dc-118">セキュリティで保護されていない要求をセキュリティで保護されたエンドポイントにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="734dc-119">外部サイトが別のサイトでホストされている静的資産を独自のコンテンツにリンクすることによってその資産を使用するホットリンクを防止します。</span><span class="sxs-lookup"><span data-stu-id="734dc-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-120">URL の書き換えによってアプリのパフォーマンスが低下することがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="734dc-121">可能であれば、ルールの複雑さと数を制限します。</span><span class="sxs-lookup"><span data-stu-id="734dc-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="734dc-122">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="734dc-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="734dc-123">URL リダイレクトと URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="734dc-124">"*URL リダイレクト*" と "*URL 書き換え*" では表現上はあまり違いがないように見えますが、クライアントへのリソースの提供に関しては重要な意味を持っています。</span><span class="sxs-lookup"><span data-stu-id="734dc-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="734dc-125">ASP.NET Core の URL リライト ミドルウェアは、両方のニーズを満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="734dc-126">"*URL リダイレクト*" にはクライアント側の操作が関係しており、クライアントはもともと要求したものとは異なるアドレスにあるリソースにアクセスするよう指示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="734dc-127">これによりサーバーへのラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="734dc-127">This requires a round trip to the server.</span></span> <span data-ttu-id="734dc-128">クライアントが、リソースの新しい要求を実行するときに、クライアントに返されたリダイレクト URL がブラウザーのアドレス バーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="734dc-129">`/resource` が `/different-resource` に "*リダイレクトされる*" 場合、サーバーからの応答では、クライアントが `/different-resource` にあるリソースを取得する必要があることと、リダイレクトが一時的または永続的のどちらかを示す状態コードが示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に一時的に変更されました。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="734dc-135">要求を別の URL にリダイレクトする場合は、応答で状態コードを指定することにより、リダイレクトが永続的か一時的かのどちらかを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="734dc-136">"*301 - 完全な移動*" 状態コードは、リソースに新しい永続的な URL があり、リソースに対する将来のすべての要求で新しい URL を使用する必要があることをクライアントに指示したい場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="734dc-137">"*301 状態コードを受信すると、クライアントは応答をキャッシュに入れて再利用することができます。* "</span><span class="sxs-lookup"><span data-stu-id="734dc-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="734dc-138">"*302 - 検出*" 状態コードは、リダイレクトが一時的である場合、または一般に変更される可能性がある場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="734dc-139">302 状態コードは、URL を保存して後で再利用しないようクライアントに指示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="734dc-140">状態コードの詳細については、[RFC 2616: 状態コードの定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="734dc-141">"*URL 書き換え*" はサーバー側の操作であり、クライアントが要求したものとは異なるリソース アドレスからリソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="734dc-142">URL 書き換えでは、サーバーへのラウンドトリップは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="734dc-143">書き換えられた URL は、クライアントに返されず、ブラウザーのアドレス バーに表示されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="734dc-144">`/resource` が `/different-resource` に "*書き換えられた*" 場合、サーバーは `/different-resource` にあるリソースを "*内部的に*" 取得して返します。</span><span class="sxs-lookup"><span data-stu-id="734dc-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="734dc-145">クライアントは、書き換えられた URL にあるリソースを取得できる場合がありますが、クライアントは、その要求を実行して応答を受信したときに、書き換えられた URL にリソースが存在することは通知されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に変更されました。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="734dc-150">URL リライト サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="734dc-150">URL rewriting sample app</span></span>

<span data-ttu-id="734dc-151">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)を使用して、URL リライト ミドルウェアの機能を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="734dc-152">そのアプリは、リダイレクトと書き換えのルールを適用し、複数のシナリオについて、リダイレクトされた URL または書き換えられた URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="734dc-153">URL リライト ミドルウェアを使用する状況</span><span class="sxs-lookup"><span data-stu-id="734dc-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="734dc-154">次の方法を使用できない場合は、URL リライト ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="734dc-155">Windows Server 上の IIS での URL リライト モジュール</span><span class="sxs-lookup"><span data-stu-id="734dc-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="734dc-156">Apache サーバー上の Apache mod_rewrite モジュール</span><span class="sxs-lookup"><span data-stu-id="734dc-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="734dc-157">Nginx での URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="734dc-158">また、アプリが [HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) でホストされているときも、ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="734dc-159">IIS、Apache、Nginx でサーバー ベースの URL 書き換えテクノロジが使用される主な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="734dc-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="734dc-160">ミドルウェアでは、これらのモジュールの完全な機能がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="734dc-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="734dc-161">IIS 書き換えモジュールの `IsFile` 制約や `IsDirectory` 制約など、サーバー モジュールの一部の機能は、ASP.NET Core プロジェクトでは動作しません。</span><span class="sxs-lookup"><span data-stu-id="734dc-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="734dc-162">これらのシナリオでは、代わりにミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="734dc-163">通常、ミドルウェアのパフォーマンスはモジュールより劣ります。</span><span class="sxs-lookup"><span data-stu-id="734dc-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="734dc-164">どちらのアプローチの方がパフォーマンスの低下が大きいか、またはパフォーマンスが低下した場合でもごくわずかかどうかを確実に知るには、ベンチマークが唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="734dc-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="734dc-165">Package</span><span class="sxs-lookup"><span data-stu-id="734dc-165">Package</span></span>

<span data-ttu-id="734dc-166">URL リライト ミドルウェアは、[Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) パッケージによって提供されます。このパッケージは ASP.NET Core アプリに暗黙的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="734dc-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="734dc-167">拡張機能とオプション</span><span class="sxs-lookup"><span data-stu-id="734dc-167">Extension and options</span></span>

<span data-ttu-id="734dc-168">各書き換えルールに対する拡張メソッドを含む [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) クラスのインスタンスを作成することにより、URL 書き換えとリダイレクトのルールを設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="734dc-169">処理したい順序で複数のルールを連結します。</span><span class="sxs-lookup"><span data-stu-id="734dc-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="734dc-170"><xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> によって URL リライト ミドルウェアが要求パイプラインに追加されると、`RewriteOptions` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="734dc-171">非 WWW を WWW にリダイレクトする</span><span class="sxs-lookup"><span data-stu-id="734dc-171">Redirect non-www to www</span></span>

<span data-ttu-id="734dc-172">アプリで非 `www` の要求を `www` にリダイレクトできる 3 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="734dc-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>:要求が非 `www` の場合、要求を `www` サブドメインに永続的にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="734dc-174">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="734dc-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>:受信した要求が非 `www` の場合、要求を `www` サブドメインにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="734dc-176">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="734dc-177">オーバーロードにより、応答に対する状態コードを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="734dc-178">状態コードの割り当てには、<xref:Microsoft.AspNetCore.Http.StatusCodes> クラスのフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="734dc-179">URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="734dc-179">URL redirect</span></span>

<span data-ttu-id="734dc-180">要求をリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="734dc-181">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="734dc-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="734dc-182">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="734dc-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="734dc-183">3 番目のパラメーター (存在する場合) は、状態コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="734dc-184">状態コードを指定しない場合、状態コードは既定で "*302 - 検出*" に設定されます。これは、リソースが一時的に移動されるか置き換えられることを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="734dc-185">開発者ツールが有効になっているブラウザーで、パス `/redirect-rule/1234/5678` を指定してサンプル アプリに対する要求を実行します。</span><span class="sxs-lookup"><span data-stu-id="734dc-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="734dc-186">正規表現が `redirect-rule/(.*)` の要求のパスに一致し、パスが `/redirected/1234/5678` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="734dc-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="734dc-187">リダイレクト URL が、"*302 - 検出*" 状態コードでクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="734dc-188">ブラウザーは、ブラウザーのアドレス バーに表示されるリダイレクト URL に新しい要求を実行します</span><span class="sxs-lookup"><span data-stu-id="734dc-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="734dc-189">リダイレクト URL にはサンプル アプリに一致するルールがないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="734dc-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="734dc-190">2 番目の要求は、アプリから *200 - OK* 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="734dc-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="734dc-191">応答の本文では、リダイレクト URL が示されています。</span><span class="sxs-lookup"><span data-stu-id="734dc-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="734dc-192">URL が "*リダイレクト*" されるときに、サーバーへのラウンドトリップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="734dc-193">リダイレクト ルールを設定するときは注意してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="734dc-194">リダイレクト ルールは、リダイレクト後を含めて、アプリへのすべての要求で評価されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="734dc-195">"*無限リダイレクトのループ*" が誤って作成されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="734dc-196">元の要求: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="734dc-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="734dc-198">かっこ内に含まれる式の一部は、*キャプチャ グループ*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="734dc-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="734dc-199">式のドット (`.`) は、*どの文字とも一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="734dc-200">アスタリスク (`*`) は、*直前の文字に 0 回以上一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="734dc-201">そのため、URL の最後の 2 つのパス セグメント `1234/5678` は、キャプチャ グループ `(.*)` によってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="734dc-202">要求 URL で `redirect-rule/` の後に指定した任意の値が、この単一のキャプチャ グループによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="734dc-203">置換文字列では、キャプチャされたグループがドル記号 (`$`) を使用して文字列に挿入され、その後にキャプチャのシーケンス番号が続きます。</span><span class="sxs-lookup"><span data-stu-id="734dc-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="734dc-204">最初のキャプチャ グループ値は、`$1` で取得され、2 番目は `$2` で取得され、これらは正規表現内のキャプチャ グループの順序で続行されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="734dc-205">サンプル アプリでは、リダイレクト ルールの正規表現内に 1 つのキャプチャ グループだけがあるので、置換文字列に 1 つの挿入されたグループ (`$1`) だけがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="734dc-206">ルールが適用されるときに、URL `/redirected/1234/5678` になります。</span><span class="sxs-lookup"><span data-stu-id="734dc-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="734dc-207">セキュリティで保護されたエンドポイントへの URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="734dc-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="734dc-208">HTTP 要求を、同じホストとパスに HTTPS プロトコルを使用してリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="734dc-209">状態コードが指定されていない場合、ミドルウェアは既定で "*302 - 検出*" に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="734dc-210">ポートが指定されていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="734dc-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="734dc-211">ミドルウェアの既定値 `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="734dc-212">スキームは `https` (HTTPS プロトコル) に変更されて、クライアントはポート 443 でリソースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="734dc-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="734dc-213">次の例では、状態コードを "*301 - 完全な移動*" に設定し、ポートを 5001 に変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="734dc-214">セキュリティで保護されていない要求を、セキュリティで保護された HTTPS プロトコル (ポート 443) を使用して同じホストとパスにリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="734dc-215">ミドルウェアは状態コードを "*301 -完全な移動"* に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="734dc-216">リダイレクト規則を追加せずにセキュリティで保護されたエンドポイントにリダイレクトするときは、HTTPS リダイレクト ミドルウェアを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="734dc-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="734dc-217">詳細については、「[HTTPS の適用](xref:security/enforcing-ssl#require-https)」のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="734dc-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="734dc-218">サンプル アプリは、`AddRedirectToHttps` または `AddRedirectToHttpsPermanent` の使用方法の例を示すことができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="734dc-219">拡張メソッドを `RewriteOptions` に追加します。</span><span class="sxs-lookup"><span data-stu-id="734dc-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="734dc-220">任意の URL でセキュリティ保護されていない要求をアプリに対して行います。</span><span class="sxs-lookup"><span data-stu-id="734dc-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="734dc-221">自己署名証明書が信頼できないことを示すブラウザーのセキュリティ警告を消去するか、証明書を信頼する例外を作成します。</span><span class="sxs-lookup"><span data-stu-id="734dc-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="734dc-222">`AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="734dc-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="734dc-224">`AddRedirectToHttpsPermanent`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="734dc-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="734dc-226">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-226">URL rewrite</span></span>

<span data-ttu-id="734dc-227">URL 書き換えのルールを作成するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="734dc-228">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="734dc-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="734dc-229">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="734dc-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="734dc-230">3 番目のパラメーター `skipRemainingRules: {true|false}` は、現在のルールが適用される場合に追加の書き換えルールをスキップするかどうかをミドルウェアに示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="734dc-231">元の要求: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="734dc-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="734dc-233">式の先頭にあるキャレット (`^`) は、URL パスの先頭からマッチングが開始されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="734dc-234">前のリダイレクト ルール `redirect-rule/(.*)` の例では、正規表現の先頭にキャレット (`^`) がありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="734dc-235">したがって、パスの `redirect-rule/` の前にどのような文字があっても一致します。</span><span class="sxs-lookup"><span data-stu-id="734dc-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="734dc-236">パス</span><span class="sxs-lookup"><span data-stu-id="734dc-236">Path</span></span>                               | <span data-ttu-id="734dc-237">一致したもの</span><span class="sxs-lookup"><span data-stu-id="734dc-237">Match</span></span> |
| ---
<span data-ttu-id="734dc-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-239">'Blazor'</span></span>
- <span data-ttu-id="734dc-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-240">'Identity'</span></span>
- <span data-ttu-id="734dc-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-242">'Razor'</span></span>
- <span data-ttu-id="734dc-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-245">'Blazor'</span></span>
- <span data-ttu-id="734dc-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-246">'Identity'</span></span>
- <span data-ttu-id="734dc-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-248">'Razor'</span></span>
- <span data-ttu-id="734dc-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-251">'Blazor'</span></span>
- <span data-ttu-id="734dc-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-252">'Identity'</span></span>
- <span data-ttu-id="734dc-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-254">'Razor'</span></span>
- <span data-ttu-id="734dc-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-257">'Blazor'</span></span>
- <span data-ttu-id="734dc-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-258">'Identity'</span></span>
- <span data-ttu-id="734dc-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-260">'Razor'</span></span>
- <span data-ttu-id="734dc-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-263">'Blazor'</span></span>
- <span data-ttu-id="734dc-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-264">'Identity'</span></span>
- <span data-ttu-id="734dc-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-266">'Razor'</span></span>
- <span data-ttu-id="734dc-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-269">'Blazor'</span></span>
- <span data-ttu-id="734dc-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-270">'Identity'</span></span>
- <span data-ttu-id="734dc-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-272">'Razor'</span></span>
- <span data-ttu-id="734dc-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-275">'Blazor'</span></span>
- <span data-ttu-id="734dc-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-276">'Identity'</span></span>
- <span data-ttu-id="734dc-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-278">'Razor'</span></span>
- <span data-ttu-id="734dc-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-281">'Blazor'</span></span>
- <span data-ttu-id="734dc-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-282">'Identity'</span></span>
- <span data-ttu-id="734dc-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-284">'Razor'</span></span>
- <span data-ttu-id="734dc-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-287">'Blazor'</span></span>
- <span data-ttu-id="734dc-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-288">'Identity'</span></span>
- <span data-ttu-id="734dc-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-290">'Razor'</span></span>
- <span data-ttu-id="734dc-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-293">'Blazor'</span></span>
- <span data-ttu-id="734dc-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-294">'Identity'</span></span>
- <span data-ttu-id="734dc-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-296">'Razor'</span></span>
- <span data-ttu-id="734dc-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-299">'Blazor'</span></span>
- <span data-ttu-id="734dc-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-300">'Identity'</span></span>
- <span data-ttu-id="734dc-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-302">'Razor'</span></span>
- <span data-ttu-id="734dc-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-305">'Blazor'</span></span>
- <span data-ttu-id="734dc-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-306">'Identity'</span></span>
- <span data-ttu-id="734dc-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-308">'Razor'</span></span>
- <span data-ttu-id="734dc-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-311">'Blazor'</span></span>
- <span data-ttu-id="734dc-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-312">'Identity'</span></span>
- <span data-ttu-id="734dc-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-314">'Razor'</span></span>
- <span data-ttu-id="734dc-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-317">'Blazor'</span></span>
- <span data-ttu-id="734dc-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-318">'Identity'</span></span>
- <span data-ttu-id="734dc-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-320">'Razor'</span></span>
- <span data-ttu-id="734dc-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-323">'Blazor'</span></span>
- <span data-ttu-id="734dc-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-324">'Identity'</span></span>
- <span data-ttu-id="734dc-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-326">'Razor'</span></span>
- <span data-ttu-id="734dc-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-327">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | はい   | | `/my-cool-redirect-rule/1234/5678` | はい   | | `/anotherredirect-rule/1234/5678`  | はい   |</span><span class="sxs-lookup"><span data-stu-id="734dc-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="734dc-329">書き換えルール `^rewrite-rule/(\d+)/(\d+)` は、`rewrite-rule/` で始まる場合のみパスと一致します。</span><span class="sxs-lookup"><span data-stu-id="734dc-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="734dc-330">次の表では、一致の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="734dc-331">パス</span><span class="sxs-lookup"><span data-stu-id="734dc-331">Path</span></span>                              | <span data-ttu-id="734dc-332">一致したもの</span><span class="sxs-lookup"><span data-stu-id="734dc-332">Match</span></span> |
| ---
<span data-ttu-id="734dc-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-334">'Blazor'</span></span>
- <span data-ttu-id="734dc-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-335">'Identity'</span></span>
- <span data-ttu-id="734dc-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-337">'Razor'</span></span>
- <span data-ttu-id="734dc-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-340">'Blazor'</span></span>
- <span data-ttu-id="734dc-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-341">'Identity'</span></span>
- <span data-ttu-id="734dc-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-343">'Razor'</span></span>
- <span data-ttu-id="734dc-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-346">'Blazor'</span></span>
- <span data-ttu-id="734dc-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-347">'Identity'</span></span>
- <span data-ttu-id="734dc-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-349">'Razor'</span></span>
- <span data-ttu-id="734dc-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-352">'Blazor'</span></span>
- <span data-ttu-id="734dc-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-353">'Identity'</span></span>
- <span data-ttu-id="734dc-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-355">'Razor'</span></span>
- <span data-ttu-id="734dc-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-358">'Blazor'</span></span>
- <span data-ttu-id="734dc-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-359">'Identity'</span></span>
- <span data-ttu-id="734dc-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-361">'Razor'</span></span>
- <span data-ttu-id="734dc-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-364">'Blazor'</span></span>
- <span data-ttu-id="734dc-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-365">'Identity'</span></span>
- <span data-ttu-id="734dc-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-367">'Razor'</span></span>
- <span data-ttu-id="734dc-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-370">'Blazor'</span></span>
- <span data-ttu-id="734dc-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-371">'Identity'</span></span>
- <span data-ttu-id="734dc-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-373">'Razor'</span></span>
- <span data-ttu-id="734dc-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-376">'Blazor'</span></span>
- <span data-ttu-id="734dc-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-377">'Identity'</span></span>
- <span data-ttu-id="734dc-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-379">'Razor'</span></span>
- <span data-ttu-id="734dc-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-382">'Blazor'</span></span>
- <span data-ttu-id="734dc-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-383">'Identity'</span></span>
- <span data-ttu-id="734dc-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-385">'Razor'</span></span>
- <span data-ttu-id="734dc-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-388">'Blazor'</span></span>
- <span data-ttu-id="734dc-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-389">'Identity'</span></span>
- <span data-ttu-id="734dc-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-391">'Razor'</span></span>
- <span data-ttu-id="734dc-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-394">'Blazor'</span></span>
- <span data-ttu-id="734dc-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-395">'Identity'</span></span>
- <span data-ttu-id="734dc-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-397">'Razor'</span></span>
- <span data-ttu-id="734dc-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-400">'Blazor'</span></span>
- <span data-ttu-id="734dc-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-401">'Identity'</span></span>
- <span data-ttu-id="734dc-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-403">'Razor'</span></span>
- <span data-ttu-id="734dc-404">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-406">'Blazor'</span></span>
- <span data-ttu-id="734dc-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-407">'Identity'</span></span>
- <span data-ttu-id="734dc-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-409">'Razor'</span></span>
- <span data-ttu-id="734dc-410">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-412">'Blazor'</span></span>
- <span data-ttu-id="734dc-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-413">'Identity'</span></span>
- <span data-ttu-id="734dc-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-415">'Razor'</span></span>
- <span data-ttu-id="734dc-416">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-416">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | はい   | | `/my-cool-rewrite-rule/1234/5678` | いいえ    | | `/anotherrewrite-rule/1234/5678`  | いいえ    |</span><span class="sxs-lookup"><span data-stu-id="734dc-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="734dc-418">式の `^rewrite-rule/` に続く部分に、2 つのキャプチャ グループ `(\d+)/(\d+)` があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="734dc-419">`\d` は、*1 桁 (数字) に一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="734dc-420">プラス記号 (`+`) は、*直前の 1 つ以上の文字に一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="734dc-421">そのため、URL は、数字とその後に続くスラッシュ、さらにその後に続く別の数字を含んでいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="734dc-422">これらのキャプチャ グループが `$1` および `$2` として書き換えられた URL に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="734dc-423">書き換えルールの置換文字列は、クエリ文字列にキャプチャされたグループを配置します。</span><span class="sxs-lookup"><span data-stu-id="734dc-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="734dc-424">`/rewrite-rule/1234/5678` の要求されたパスは、`/rewritten?var1=1234&var2=5678` でリソースを取得するように書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="734dc-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="734dc-425">クエリ文字列が元の要求に存在する場合、URL が書き換えられるときに保持されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="734dc-426">リソースを取得するためのサーバーへのラウンドトリップはありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="734dc-427">リソースが存在する場合は、取得され、*200 - OK* 状態コードと共にクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="734dc-428">クライアントはリダイレクトされていないため、ブラウザーのアドレス バーの URL は変更されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="734dc-429">クライアントは、URL の書き換え操作がサーバーで発生したことを検出できません。</span><span class="sxs-lookup"><span data-stu-id="734dc-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-430">式内の照合ルールは計算量が多く、アプリの応答時間が長くなるので、可能な限り `skipRemainingRules: true` を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="734dc-431">最も高速なアプリの応答を実現するには以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="734dc-432">一致する頻度が最も多いルールから一致頻度が最も少ないルールへの順番で書き換えルールを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="734dc-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="734dc-433">一致が発生し、追加の処理が必要ないときに残りのルールの処理をスキップします。</span><span class="sxs-lookup"><span data-stu-id="734dc-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="734dc-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="734dc-434">Apache mod_rewrite</span></span>

<span data-ttu-id="734dc-435"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> を使用して Apache mod_rewrite ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="734dc-436">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="734dc-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="734dc-437">mod_rewrite ルールの情報と例については、「[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="734dc-438">*ApacheModRewrite.txt* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="734dc-439">サンプル アプリは、要求を `/apache-mod-rules-redirect/(.\*)` から `/redirected?id=$1` にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="734dc-440">応答の状態コードは "*302 -検出*" です。</span><span class="sxs-lookup"><span data-stu-id="734dc-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="734dc-441">元の要求: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="734dc-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="734dc-443">ミドルウェアは、次の Apache mod_rewrite サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="734dc-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="734dc-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="734dc-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="734dc-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="734dc-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="734dc-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="734dc-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="734dc-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="734dc-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="734dc-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="734dc-449">HTTP_HOST</span></span>
* <span data-ttu-id="734dc-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="734dc-450">HTTP_REFERER</span></span>
* <span data-ttu-id="734dc-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="734dc-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="734dc-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="734dc-452">HTTPS</span></span>
* <span data-ttu-id="734dc-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="734dc-453">IPV6</span></span>
* <span data-ttu-id="734dc-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="734dc-454">QUERY_STRING</span></span>
* <span data-ttu-id="734dc-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-456">REMOTE_PORT</span></span>
* <span data-ttu-id="734dc-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="734dc-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="734dc-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="734dc-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="734dc-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="734dc-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="734dc-460">REQUEST_URI</span></span>
* <span data-ttu-id="734dc-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="734dc-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-462">SERVER_ADDR</span></span>
* <span data-ttu-id="734dc-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-463">SERVER_PORT</span></span>
* <span data-ttu-id="734dc-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="734dc-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="734dc-465">TIME</span><span class="sxs-lookup"><span data-stu-id="734dc-465">TIME</span></span>
* <span data-ttu-id="734dc-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="734dc-466">TIME_DAY</span></span>
* <span data-ttu-id="734dc-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="734dc-467">TIME_HOUR</span></span>
* <span data-ttu-id="734dc-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="734dc-468">TIME_MIN</span></span>
* <span data-ttu-id="734dc-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="734dc-469">TIME_MON</span></span>
* <span data-ttu-id="734dc-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="734dc-470">TIME_SEC</span></span>
* <span data-ttu-id="734dc-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="734dc-471">TIME_WDAY</span></span>
* <span data-ttu-id="734dc-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="734dc-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="734dc-473">IIS URL リライト モジュールのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="734dc-474">IIS URL リライト モジュールに適用される同じルール セットを使用するには、<xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="734dc-475">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="734dc-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="734dc-476">Windows Server IIS で実行されているときにミドルウェアでアプリの *web.config* ファイルを使用するように指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="734dc-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="734dc-477">IIS を使用する場合、IIS リライト モジュールとの競合を避けるため、これらのルールをアプリの *web.config* の外部に保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="734dc-478">IIS URL リライト モジュールのルールの詳細と例については、「[Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)」(URL リライト モジュール 2.0 の使用 ) と「[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)」(URL リライト モジュール構成リファレンス) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="734dc-479">*IISUrlRewrite.xml* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="734dc-480">サンプル アプリは、要求を `/iis-rules-rewrite/(.*)` から `/rewritten?id=$1` に書き換えます。</span><span class="sxs-lookup"><span data-stu-id="734dc-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="734dc-481">応答は、*200 - OK* 状態コードでクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="734dc-482">元の要求: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="734dc-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="734dc-484">望ましくない方法でアプリに影響するように構成されたサーバー レベル ルールを使用するアクティブな IIS リライト モジュールがある場合は、アプリに対して IIS リライト モジュールを無効にできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="734dc-485">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="734dc-486">サポートされていない機能</span><span class="sxs-lookup"><span data-stu-id="734dc-486">Unsupported features</span></span>

<span data-ttu-id="734dc-487">ASP.NET Core 2.x でリリースされたミドルウェアは、次の IIS URL リライト モジュールの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="734dc-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="734dc-488">送信ルール</span><span class="sxs-lookup"><span data-stu-id="734dc-488">Outbound Rules</span></span>
* <span data-ttu-id="734dc-489">カスタム サーバー変数</span><span class="sxs-lookup"><span data-stu-id="734dc-489">Custom Server Variables</span></span>
* <span data-ttu-id="734dc-490">ワイルドカード</span><span class="sxs-lookup"><span data-stu-id="734dc-490">Wildcards</span></span>
* <span data-ttu-id="734dc-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="734dc-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="734dc-492">サポートされるサーバー変数</span><span class="sxs-lookup"><span data-stu-id="734dc-492">Supported server variables</span></span>

<span data-ttu-id="734dc-493">ミドルウェアは、次の IIS URL リライト モジュール サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="734dc-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="734dc-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="734dc-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="734dc-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="734dc-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="734dc-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="734dc-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="734dc-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="734dc-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="734dc-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="734dc-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="734dc-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="734dc-499">HTTP_HOST</span></span>
* <span data-ttu-id="734dc-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="734dc-500">HTTP_REFERER</span></span>
* <span data-ttu-id="734dc-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="734dc-501">HTTP_URL</span></span>
* <span data-ttu-id="734dc-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="734dc-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="734dc-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="734dc-503">HTTPS</span></span>
* <span data-ttu-id="734dc-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="734dc-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="734dc-505">QUERY_STRING</span></span>
* <span data-ttu-id="734dc-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-507">REMOTE_PORT</span></span>
* <span data-ttu-id="734dc-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="734dc-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="734dc-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-510"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> を介して <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="734dc-511">このアプローチは、書き換えルール ファイルの場所に関する大きな柔軟性を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="734dc-512">書き換えルール ファイルを指定したパスでサーバーに導入されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="734dc-513">メソッド ベースのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-513">Method-based rule</span></span>

<span data-ttu-id="734dc-514">メソッド内で独自のルール ロジックを実装するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="734dc-515">`Add` は <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> を公開し、メソッドで <xref:Microsoft.AspNetCore.Http.HttpContext> を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="734dc-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) は、追加のパイプライン処理の実行方法を決定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="734dc-517">次の表で説明する <xref:Microsoft.AspNetCore.Rewrite.RuleResult> フィールドのいずれかに値を設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="734dc-518">アクション</span><span class="sxs-lookup"><span data-stu-id="734dc-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="734dc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-520">'Blazor'</span></span>
- <span data-ttu-id="734dc-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-521">'Identity'</span></span>
- <span data-ttu-id="734dc-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-523">'Razor'</span></span>
- <span data-ttu-id="734dc-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-526">'Blazor'</span></span>
- <span data-ttu-id="734dc-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-527">'Identity'</span></span>
- <span data-ttu-id="734dc-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-529">'Razor'</span></span>
- <span data-ttu-id="734dc-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-532">'Blazor'</span></span>
- <span data-ttu-id="734dc-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-533">'Identity'</span></span>
- <span data-ttu-id="734dc-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-535">'Razor'</span></span>
- <span data-ttu-id="734dc-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-538">'Blazor'</span></span>
- <span data-ttu-id="734dc-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-539">'Identity'</span></span>
- <span data-ttu-id="734dc-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-541">'Razor'</span></span>
- <span data-ttu-id="734dc-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-544">'Blazor'</span></span>
- <span data-ttu-id="734dc-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-545">'Identity'</span></span>
- <span data-ttu-id="734dc-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-547">'Razor'</span></span>
- <span data-ttu-id="734dc-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-550">'Blazor'</span></span>
- <span data-ttu-id="734dc-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-551">'Identity'</span></span>
- <span data-ttu-id="734dc-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-553">'Razor'</span></span>
- <span data-ttu-id="734dc-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-556">'Blazor'</span></span>
- <span data-ttu-id="734dc-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-557">'Identity'</span></span>
- <span data-ttu-id="734dc-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-559">'Razor'</span></span>
- <span data-ttu-id="734dc-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-562">'Blazor'</span></span>
- <span data-ttu-id="734dc-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-563">'Identity'</span></span>
- <span data-ttu-id="734dc-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-565">'Razor'</span></span>
- <span data-ttu-id="734dc-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-568">'Blazor'</span></span>
- <span data-ttu-id="734dc-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-569">'Identity'</span></span>
- <span data-ttu-id="734dc-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-571">'Razor'</span></span>
- <span data-ttu-id="734dc-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-574">'Blazor'</span></span>
- <span data-ttu-id="734dc-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-575">'Identity'</span></span>
- <span data-ttu-id="734dc-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-577">'Razor'</span></span>
- <span data-ttu-id="734dc-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-580">'Blazor'</span></span>
- <span data-ttu-id="734dc-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-581">'Identity'</span></span>
- <span data-ttu-id="734dc-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-583">'Razor'</span></span>
- <span data-ttu-id="734dc-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-586">'Blazor'</span></span>
- <span data-ttu-id="734dc-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-587">'Identity'</span></span>
- <span data-ttu-id="734dc-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-589">'Razor'</span></span>
- <span data-ttu-id="734dc-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-592">'Blazor'</span></span>
- <span data-ttu-id="734dc-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-593">'Identity'</span></span>
- <span data-ttu-id="734dc-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-595">'Razor'</span></span>
- <span data-ttu-id="734dc-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-598">'Blazor'</span></span>
- <span data-ttu-id="734dc-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-599">'Identity'</span></span>
- <span data-ttu-id="734dc-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-601">'Razor'</span></span>
- <span data-ttu-id="734dc-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-604">'Blazor'</span></span>
- <span data-ttu-id="734dc-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-605">'Identity'</span></span>
- <span data-ttu-id="734dc-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-607">'Razor'</span></span>
- <span data-ttu-id="734dc-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-610">'Blazor'</span></span>
- <span data-ttu-id="734dc-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-611">'Identity'</span></span>
- <span data-ttu-id="734dc-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-613">'Razor'</span></span>
- <span data-ttu-id="734dc-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-614">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-616">'Blazor'</span></span>
- <span data-ttu-id="734dc-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-617">'Identity'</span></span>
- <span data-ttu-id="734dc-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-619">'Razor'</span></span>
- <span data-ttu-id="734dc-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-622">'Blazor'</span></span>
- <span data-ttu-id="734dc-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-623">'Identity'</span></span>
- <span data-ttu-id="734dc-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-625">'Razor'</span></span>
- <span data-ttu-id="734dc-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-628">'Blazor'</span></span>
- <span data-ttu-id="734dc-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-629">'Identity'</span></span>
- <span data-ttu-id="734dc-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-631">'Razor'</span></span>
- <span data-ttu-id="734dc-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-634">'Blazor'</span></span>
- <span data-ttu-id="734dc-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-635">'Identity'</span></span>
- <span data-ttu-id="734dc-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-637">'Razor'</span></span>
- <span data-ttu-id="734dc-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-640">'Blazor'</span></span>
- <span data-ttu-id="734dc-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-641">'Identity'</span></span>
- <span data-ttu-id="734dc-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-643">'Razor'</span></span>
- <span data-ttu-id="734dc-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-646">'Blazor'</span></span>
- <span data-ttu-id="734dc-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-647">'Identity'</span></span>
- <span data-ttu-id="734dc-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-649">'Razor'</span></span>
- <span data-ttu-id="734dc-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-652">'Blazor'</span></span>
- <span data-ttu-id="734dc-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-653">'Identity'</span></span>
- <span data-ttu-id="734dc-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-655">'Razor'</span></span>
- <span data-ttu-id="734dc-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-658">'Blazor'</span></span>
- <span data-ttu-id="734dc-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-659">'Identity'</span></span>
- <span data-ttu-id="734dc-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-661">'Razor'</span></span>
- <span data-ttu-id="734dc-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-664">'Blazor'</span></span>
- <span data-ttu-id="734dc-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-665">'Identity'</span></span>
- <span data-ttu-id="734dc-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-667">'Razor'</span></span>
- <span data-ttu-id="734dc-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-670">'Blazor'</span></span>
- <span data-ttu-id="734dc-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-671">'Identity'</span></span>
- <span data-ttu-id="734dc-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-673">'Razor'</span></span>
- <span data-ttu-id="734dc-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-676">'Blazor'</span></span>
- <span data-ttu-id="734dc-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-677">'Identity'</span></span>
- <span data-ttu-id="734dc-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-679">'Razor'</span></span>
- <span data-ttu-id="734dc-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-682">'Blazor'</span></span>
- <span data-ttu-id="734dc-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-683">'Identity'</span></span>
- <span data-ttu-id="734dc-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-685">'Razor'</span></span>
- <span data-ttu-id="734dc-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-688">'Blazor'</span></span>
- <span data-ttu-id="734dc-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-689">'Identity'</span></span>
- <span data-ttu-id="734dc-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-691">'Razor'</span></span>
- <span data-ttu-id="734dc-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-694">'Blazor'</span></span>
- <span data-ttu-id="734dc-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-695">'Identity'</span></span>
- <span data-ttu-id="734dc-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-697">'Razor'</span></span>
- <span data-ttu-id="734dc-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-700">'Blazor'</span></span>
- <span data-ttu-id="734dc-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-701">'Identity'</span></span>
- <span data-ttu-id="734dc-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-703">'Razor'</span></span>
- <span data-ttu-id="734dc-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-706">'Blazor'</span></span>
- <span data-ttu-id="734dc-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-707">'Identity'</span></span>
- <span data-ttu-id="734dc-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-709">'Razor'</span></span>
- <span data-ttu-id="734dc-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-712">'Blazor'</span></span>
- <span data-ttu-id="734dc-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-713">'Identity'</span></span>
- <span data-ttu-id="734dc-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-715">'Razor'</span></span>
- <span data-ttu-id="734dc-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-718">'Blazor'</span></span>
- <span data-ttu-id="734dc-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-719">'Identity'</span></span>
- <span data-ttu-id="734dc-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-721">'Razor'</span></span>
- <span data-ttu-id="734dc-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-724">'Blazor'</span></span>
- <span data-ttu-id="734dc-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-725">'Identity'</span></span>
- <span data-ttu-id="734dc-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-727">'Razor'</span></span>
- <span data-ttu-id="734dc-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-730">'Blazor'</span></span>
- <span data-ttu-id="734dc-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-731">'Identity'</span></span>
- <span data-ttu-id="734dc-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-733">'Razor'</span></span>
- <span data-ttu-id="734dc-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-736">'Blazor'</span></span>
- <span data-ttu-id="734dc-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-737">'Identity'</span></span>
- <span data-ttu-id="734dc-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-739">'Razor'</span></span>
- <span data-ttu-id="734dc-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-742">'Blazor'</span></span>
- <span data-ttu-id="734dc-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-743">'Identity'</span></span>
- <span data-ttu-id="734dc-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-745">'Razor'</span></span>
- <span data-ttu-id="734dc-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-748">'Blazor'</span></span>
- <span data-ttu-id="734dc-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-749">'Identity'</span></span>
- <span data-ttu-id="734dc-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-751">'Razor'</span></span>
- <span data-ttu-id="734dc-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-754">'Blazor'</span></span>
- <span data-ttu-id="734dc-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-755">'Identity'</span></span>
- <span data-ttu-id="734dc-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-757">'Razor'</span></span>
- <span data-ttu-id="734dc-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-760">'Blazor'</span></span>
- <span data-ttu-id="734dc-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-761">'Identity'</span></span>
- <span data-ttu-id="734dc-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-763">'Razor'</span></span>
- <span data-ttu-id="734dc-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-766">'Blazor'</span></span>
- <span data-ttu-id="734dc-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-767">'Identity'</span></span>
- <span data-ttu-id="734dc-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-769">'Razor'</span></span>
- <span data-ttu-id="734dc-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-772">'Blazor'</span></span>
- <span data-ttu-id="734dc-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-773">'Identity'</span></span>
- <span data-ttu-id="734dc-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-775">'Razor'</span></span>
- <span data-ttu-id="734dc-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-778">'Blazor'</span></span>
- <span data-ttu-id="734dc-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-779">'Identity'</span></span>
- <span data-ttu-id="734dc-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-781">'Razor'</span></span>
- <span data-ttu-id="734dc-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-784">'Blazor'</span></span>
- <span data-ttu-id="734dc-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-785">'Identity'</span></span>
- <span data-ttu-id="734dc-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-787">'Razor'</span></span>
- <span data-ttu-id="734dc-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-790">'Blazor'</span></span>
- <span data-ttu-id="734dc-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-791">'Identity'</span></span>
- <span data-ttu-id="734dc-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-793">'Razor'</span></span>
- <span data-ttu-id="734dc-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-794">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-795">-------------------------------- | | `RuleResult.ContinueRules` (既定) | ルールの適用を続けます。</span><span class="sxs-lookup"><span data-stu-id="734dc-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="734dc-796">| | `RuleResult.EndResponse`             | ルールの適用を停止し、応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="734dc-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="734dc-797">| | `RuleResult.SkipRemainingRules`      | ルールの適用を停止し、次のミドルウェアにコンテキストを送信します。</span><span class="sxs-lookup"><span data-stu-id="734dc-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="734dc-798">サンプル アプリは、 *.xml* で終了するパスの要求をリダイレクトするメソッドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="734dc-799">`/file.xml` に対して要求が行われた場合、要求は `/xmlfiles/file.xml` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="734dc-800">状態コードは "*301 - 完全な移動*" に設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="734dc-801">ブラウザーが */xmlfiles/file.xml* に対する新しい要求を行うと、静的ファイル ミドルウェアはクライアントに *wwwroot/xmlfiles* フォルダーからファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="734dc-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="734dc-802">リダイレクトの場合は、応答の状態コードを明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="734dc-803">それ以外の場合は、*200 - OK* 状態コードが返され、クライアントでのリダイレクトは発生しません。</span><span class="sxs-lookup"><span data-stu-id="734dc-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="734dc-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="734dc-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="734dc-805">このアプローチでは、要求を書き換えることもできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="734dc-806">サンプル アプリでは、*wwwroot* フォルダーから *file.txt* テキスト ファイルを提供するためにテキスト ファイル要求のパスを書き換える処理が示されています。</span><span class="sxs-lookup"><span data-stu-id="734dc-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="734dc-807">静的ファイル ミドルウェアでは、更新された要求パスに基づいてファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="734dc-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="734dc-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="734dc-809">IRule ベースのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-809">IRule-based rule</span></span>

<span data-ttu-id="734dc-810"><xref:Microsoft.AspNetCore.Rewrite.IRule> インターフェイスを実装するクラス内のルール ロジックを使用するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="734dc-811">`IRule` では、メソッド ベースのルール アプローチを使用する場合より高い柔軟性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="734dc-812">実装クラスには、<xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> メソッドに対してパラメーターを渡すことができるコンストラクターを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="734dc-813">サンプル アプリの `extension` と `newPath` のパラメーターの値は、いくつかの条件を満たすかチェックされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="734dc-814">`extension` は、値を含んでいる必要があり、値は *.png*、 *.jpg*、または *.gif* でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="734dc-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="734dc-815">`newPath` が有効ではない場合、<xref:System.ArgumentException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="734dc-816">*image.png* に対して行われた要求は、`/png-images/image.png` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="734dc-817">*image.jpg* に対して行われた要求は、`/jpg-images/image.jpg` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="734dc-818">状態コードは "*301 - 完全な移動*" に設定され、`context.Result` はルールの処理を停止して応答を送信するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="734dc-819">元の要求: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="734dc-819">Original Request: `/image.png`</span></span>

![image.png の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="734dc-821">元の要求: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="734dc-821">Original Request: `/image.jpg`</span></span>

![image.jpg の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="734dc-823">正規表現の例</span><span class="sxs-lookup"><span data-stu-id="734dc-823">Regex examples</span></span>

| <span data-ttu-id="734dc-824">Goal</span><span class="sxs-lookup"><span data-stu-id="734dc-824">Goal</span></span> | <span data-ttu-id="734dc-825">正規表現文字列と</span><span class="sxs-lookup"><span data-stu-id="734dc-825">Regex String &</span></span><br><span data-ttu-id="734dc-826">一致の例</span><span class="sxs-lookup"><span data-stu-id="734dc-826">Match Example</span></span> | <span data-ttu-id="734dc-827">置換文字列と</span><span class="sxs-lookup"><span data-stu-id="734dc-827">Replacement String &</span></span><br><span data-ttu-id="734dc-828">出力の例</span><span class="sxs-lookup"><span data-stu-id="734dc-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="734dc-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-830">'Blazor'</span></span>
- <span data-ttu-id="734dc-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-831">'Identity'</span></span>
- <span data-ttu-id="734dc-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-833">'Razor'</span></span>
- <span data-ttu-id="734dc-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-836">'Blazor'</span></span>
- <span data-ttu-id="734dc-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-837">'Identity'</span></span>
- <span data-ttu-id="734dc-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-839">'Razor'</span></span>
- <span data-ttu-id="734dc-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-842">'Blazor'</span></span>
- <span data-ttu-id="734dc-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-843">'Identity'</span></span>
- <span data-ttu-id="734dc-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-845">'Razor'</span></span>
- <span data-ttu-id="734dc-846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-848">'Blazor'</span></span>
- <span data-ttu-id="734dc-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-849">'Identity'</span></span>
- <span data-ttu-id="734dc-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-851">'Razor'</span></span>
- <span data-ttu-id="734dc-852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-854">'Blazor'</span></span>
- <span data-ttu-id="734dc-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-855">'Identity'</span></span>
- <span data-ttu-id="734dc-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-857">'Razor'</span></span>
- <span data-ttu-id="734dc-858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-860">'Blazor'</span></span>
- <span data-ttu-id="734dc-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-861">'Identity'</span></span>
- <span data-ttu-id="734dc-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-863">'Razor'</span></span>
- <span data-ttu-id="734dc-864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-866">'Blazor'</span></span>
- <span data-ttu-id="734dc-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-867">'Identity'</span></span>
- <span data-ttu-id="734dc-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-869">'Razor'</span></span>
- <span data-ttu-id="734dc-870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-872">'Blazor'</span></span>
- <span data-ttu-id="734dc-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-873">'Identity'</span></span>
- <span data-ttu-id="734dc-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-875">'Razor'</span></span>
- <span data-ttu-id="734dc-876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-878">'Blazor'</span></span>
- <span data-ttu-id="734dc-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-879">'Identity'</span></span>
- <span data-ttu-id="734dc-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-881">'Razor'</span></span>
- <span data-ttu-id="734dc-882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-884">'Blazor'</span></span>
- <span data-ttu-id="734dc-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-885">'Identity'</span></span>
- <span data-ttu-id="734dc-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-887">'Razor'</span></span>
- <span data-ttu-id="734dc-888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-890">'Blazor'</span></span>
- <span data-ttu-id="734dc-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-891">'Identity'</span></span>
- <span data-ttu-id="734dc-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-893">'Razor'</span></span>
- <span data-ttu-id="734dc-894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-896">'Blazor'</span></span>
- <span data-ttu-id="734dc-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-897">'Identity'</span></span>
- <span data-ttu-id="734dc-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-899">'Razor'</span></span>
- <span data-ttu-id="734dc-900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-902">'Blazor'</span></span>
- <span data-ttu-id="734dc-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-903">'Identity'</span></span>
- <span data-ttu-id="734dc-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-905">'Razor'</span></span>
- <span data-ttu-id="734dc-906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-906">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-908">'Blazor'</span></span>
- <span data-ttu-id="734dc-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-909">'Identity'</span></span>
- <span data-ttu-id="734dc-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-911">'Razor'</span></span>
- <span data-ttu-id="734dc-912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-914">'Blazor'</span></span>
- <span data-ttu-id="734dc-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-915">'Identity'</span></span>
- <span data-ttu-id="734dc-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-917">'Razor'</span></span>
- <span data-ttu-id="734dc-918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-920">'Blazor'</span></span>
- <span data-ttu-id="734dc-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-921">'Identity'</span></span>
- <span data-ttu-id="734dc-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-923">'Razor'</span></span>
- <span data-ttu-id="734dc-924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-926">'Blazor'</span></span>
- <span data-ttu-id="734dc-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-927">'Identity'</span></span>
- <span data-ttu-id="734dc-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-929">'Razor'</span></span>
- <span data-ttu-id="734dc-930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-932">'Blazor'</span></span>
- <span data-ttu-id="734dc-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-933">'Identity'</span></span>
- <span data-ttu-id="734dc-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-935">'Razor'</span></span>
- <span data-ttu-id="734dc-936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-938">'Blazor'</span></span>
- <span data-ttu-id="734dc-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-939">'Identity'</span></span>
- <span data-ttu-id="734dc-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-941">'Razor'</span></span>
- <span data-ttu-id="734dc-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-944">'Blazor'</span></span>
- <span data-ttu-id="734dc-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-945">'Identity'</span></span>
- <span data-ttu-id="734dc-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-947">'Razor'</span></span>
- <span data-ttu-id="734dc-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-950">'Blazor'</span></span>
- <span data-ttu-id="734dc-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-951">'Identity'</span></span>
- <span data-ttu-id="734dc-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-953">'Razor'</span></span>
- <span data-ttu-id="734dc-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-956">'Blazor'</span></span>
- <span data-ttu-id="734dc-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-957">'Identity'</span></span>
- <span data-ttu-id="734dc-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-959">'Razor'</span></span>
- <span data-ttu-id="734dc-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-962">'Blazor'</span></span>
- <span data-ttu-id="734dc-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-963">'Identity'</span></span>
- <span data-ttu-id="734dc-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-965">'Razor'</span></span>
- <span data-ttu-id="734dc-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-968">'Blazor'</span></span>
- <span data-ttu-id="734dc-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-969">'Identity'</span></span>
- <span data-ttu-id="734dc-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-971">'Razor'</span></span>
- <span data-ttu-id="734dc-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-974">'Blazor'</span></span>
- <span data-ttu-id="734dc-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-975">'Identity'</span></span>
- <span data-ttu-id="734dc-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-977">'Razor'</span></span>
- <span data-ttu-id="734dc-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-980">'Blazor'</span></span>
- <span data-ttu-id="734dc-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-981">'Identity'</span></span>
- <span data-ttu-id="734dc-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-983">'Razor'</span></span>
- <span data-ttu-id="734dc-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-986">'Blazor'</span></span>
- <span data-ttu-id="734dc-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-987">'Identity'</span></span>
- <span data-ttu-id="734dc-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-989">'Razor'</span></span>
- <span data-ttu-id="734dc-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-992">'Blazor'</span></span>
- <span data-ttu-id="734dc-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-993">'Identity'</span></span>
- <span data-ttu-id="734dc-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-995">'Razor'</span></span>
- <span data-ttu-id="734dc-996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-998">'Blazor'</span></span>
- <span data-ttu-id="734dc-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-999">'Identity'</span></span>
- <span data-ttu-id="734dc-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1001">'Razor'</span></span>
- <span data-ttu-id="734dc-1002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1004">'Blazor'</span></span>
- <span data-ttu-id="734dc-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1005">'Identity'</span></span>
- <span data-ttu-id="734dc-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1007">'Razor'</span></span>
- <span data-ttu-id="734dc-1008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1009">------------------- | | パスをクエリ文字列に書き換える | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="734dc-1010">`/path?var1=abc&var2=123` | | 末尾のスラッシュを除去する | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="734dc-1011">`/path` | | 末尾のスラッシュを強制する | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="734dc-1012">`/path/` | | 特定の要求を書き換えを回避する | `^(.*)(?<!\.axd)$` または `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="734dc-1013">はい: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="734dc-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="734dc-1014">いいえ: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="734dc-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="734dc-1015">`/resource.axd` | | URL セグメントを再配置する | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="734dc-1016">`path/3/2/1` | | URL セグメントを置き換える | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="734dc-1017">このドキュメントでは、ASP.NET Core アプリの URL リライト ミドルウェアを使用して URL を書き換える手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="734dc-1018">URL の書き換えは、1 つまたは複数の事前定義された規則に基づいて URL 要求を変更する操作です。</span><span class="sxs-lookup"><span data-stu-id="734dc-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="734dc-1019">URL 書き換えは、リソースの場所とそれらのアドレスの間の抽象化を作成し、場所とアドレスが緊密にリンクされていないようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="734dc-1020">URL 書き換えは複数のシナリオで使用できます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="734dc-1021">サーバー リソースを一時的または永続的に移動するか置き換えて、リソースに対する安定したロケーターを維持します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="734dc-1022">要求処理を異なる複数のアプリまたは 1 つのアプリの複数の区分に分割します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="734dc-1023">受信した要求の URL セグメントを削除、追加、または再編成します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="734dc-1024">検索エンジン最適化 (SEO) のためにパブリック URL を最適化します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="734dc-1025">ビジターがリソースの要求から返される内容を予測しやすいように、フレンドリなパブリック URL の使用を許可します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="734dc-1026">セキュリティで保護されていない要求をセキュリティで保護されたエンドポイントにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="734dc-1027">外部サイトが別のサイトでホストされている静的資産を独自のコンテンツにリンクすることによってその資産を使用するホットリンクを防止します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-1028">URL の書き換えによってアプリのパフォーマンスが低下することがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="734dc-1029">可能であれば、ルールの複雑さと数を制限します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="734dc-1030">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="734dc-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="734dc-1031">URL リダイレクトと URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="734dc-1032">"*URL リダイレクト*" と "*URL 書き換え*" では表現上はあまり違いがないように見えますが、クライアントへのリソースの提供に関しては重要な意味を持っています。</span><span class="sxs-lookup"><span data-stu-id="734dc-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="734dc-1033">ASP.NET Core の URL リライト ミドルウェアは、両方のニーズを満たすことができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="734dc-1034">"*URL リダイレクト*" にはクライアント側の操作が関係しており、クライアントはもともと要求したものとは異なるアドレスにあるリソースにアクセスするよう指示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="734dc-1035">これによりサーバーへのラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="734dc-1036">クライアントが、リソースの新しい要求を実行するときに、クライアントに返されたリダイレクト URL がブラウザーのアドレス バーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="734dc-1037">`/resource` が `/different-resource` に "*リダイレクトされる*" 場合、サーバーからの応答では、クライアントが `/different-resource` にあるリソースを取得する必要があることと、リダイレクトが一時的または永続的のどちらかを示す状態コードが示されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に一時的に変更されました。](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="734dc-1043">要求を別の URL にリダイレクトする場合は、応答で状態コードを指定することにより、リダイレクトが永続的か一時的かのどちらかを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="734dc-1044">"*301 - 完全な移動*" 状態コードは、リソースに新しい永続的な URL があり、リソースに対する将来のすべての要求で新しい URL を使用する必要があることをクライアントに指示したい場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="734dc-1045">"*301 状態コードを受信すると、クライアントは応答をキャッシュに入れて再利用することができます。* "</span><span class="sxs-lookup"><span data-stu-id="734dc-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="734dc-1046">"*302 - 検出*" 状態コードは、リダイレクトが一時的である場合、または一般に変更される可能性がある場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="734dc-1047">302 状態コードは、URL を保存して後で再利用しないようクライアントに指示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="734dc-1048">状態コードの詳細については、[RFC 2616: 状態コードの定義](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="734dc-1049">"*URL 書き換え*" はサーバー側の操作であり、クライアントが要求したものとは異なるリソース アドレスからリソースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="734dc-1050">URL 書き換えでは、サーバーへのラウンドトリップは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="734dc-1051">書き換えられた URL は、クライアントに返されず、ブラウザーのアドレス バーに表示されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="734dc-1052">`/resource` が `/different-resource` に "*書き換えられた*" 場合、サーバーは `/different-resource` にあるリソースを "*内部的に*" 取得して返します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="734dc-1053">クライアントは、書き換えられた URL にあるリソースを取得できる場合がありますが、クライアントは、その要求を実行して応答を受信したときに、書き換えられた URL にリソースが存在することは通知されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI サービス エンドポイントは、サーバー上でバージョン 1 (v1) からバージョン 2 (v2) に変更されました。](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="734dc-1058">URL リライト サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="734dc-1058">URL rewriting sample app</span></span>

<span data-ttu-id="734dc-1059">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)を使用して、URL リライト ミドルウェアの機能を調べることができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="734dc-1060">そのアプリは、リダイレクトと書き換えのルールを適用し、複数のシナリオについて、リダイレクトされた URL または書き換えられた URL を表示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="734dc-1061">URL リライト ミドルウェアを使用する状況</span><span class="sxs-lookup"><span data-stu-id="734dc-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="734dc-1062">次の方法を使用できない場合は、URL リライト ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="734dc-1063">Windows Server 上の IIS での URL リライト モジュール</span><span class="sxs-lookup"><span data-stu-id="734dc-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="734dc-1064">Apache サーバー上の Apache mod_rewrite モジュール</span><span class="sxs-lookup"><span data-stu-id="734dc-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="734dc-1065">Nginx での URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="734dc-1066">また、アプリが [HTTP.sys サーバー](xref:fundamentals/servers/httpsys) (旧称 WebListener) でホストされているときも、ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="734dc-1067">IIS、Apache、Nginx でサーバー ベースの URL 書き換えテクノロジが使用される主な理由は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="734dc-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="734dc-1068">ミドルウェアでは、これらのモジュールの完全な機能がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="734dc-1069">IIS 書き換えモジュールの `IsFile` 制約や `IsDirectory` 制約など、サーバー モジュールの一部の機能は、ASP.NET Core プロジェクトでは動作しません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="734dc-1070">これらのシナリオでは、代わりにミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="734dc-1071">通常、ミドルウェアのパフォーマンスはモジュールより劣ります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="734dc-1072">どちらのアプローチの方がパフォーマンスの低下が大きいか、またはパフォーマンスが低下した場合でもごくわずかかどうかを確実に知るには、ベンチマークが唯一の方法です。</span><span class="sxs-lookup"><span data-stu-id="734dc-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="734dc-1073">Package</span><span class="sxs-lookup"><span data-stu-id="734dc-1073">Package</span></span>

<span data-ttu-id="734dc-1074">ミドルウェアをプロジェクトに組み込むには、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)へのパッケージ参照をプロジェクト ファイルに追加します。これには、[Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) パッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="734dc-1075">`Microsoft.AspNetCore.App` メタパッケージを使用しない場合は、`Microsoft.AspNetCore.Rewrite` パッケージへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="734dc-1076">拡張機能とオプション</span><span class="sxs-lookup"><span data-stu-id="734dc-1076">Extension and options</span></span>

<span data-ttu-id="734dc-1077">各書き換えルールに対する拡張メソッドを含む [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) クラスのインスタンスを作成することにより、URL 書き換えとリダイレクトのルールを設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="734dc-1078">処理したい順序で複数のルールを連結します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="734dc-1079"><xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> によって URL リライト ミドルウェアが要求パイプラインに追加されると、`RewriteOptions` が渡されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="734dc-1080">非 WWW を WWW にリダイレクトする</span><span class="sxs-lookup"><span data-stu-id="734dc-1080">Redirect non-www to www</span></span>

<span data-ttu-id="734dc-1081">アプリで非 `www` の要求を `www` にリダイレクトできる 3 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="734dc-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>:要求が非 `www` の場合、要求を `www` サブドメインに永続的にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="734dc-1083">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="734dc-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>:受信した要求が非 `www` の場合、要求を `www` サブドメインにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="734dc-1085">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) 状態コードでリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="734dc-1086">オーバーロードにより、応答に対する状態コードを提供することができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="734dc-1087">状態コードの割り当てには、<xref:Microsoft.AspNetCore.Http.StatusCodes> クラスのフィールドを使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="734dc-1088">URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="734dc-1088">URL redirect</span></span>

<span data-ttu-id="734dc-1089">要求をリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="734dc-1090">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="734dc-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="734dc-1091">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="734dc-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="734dc-1092">3 番目のパラメーター (存在する場合) は、状態コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="734dc-1093">状態コードを指定しない場合、状態コードは既定で "*302 - 検出*" に設定されます。これは、リソースが一時的に移動されるか置き換えられることを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="734dc-1094">開発者ツールが有効になっているブラウザーで、パス `/redirect-rule/1234/5678` を指定してサンプル アプリに対する要求を実行します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="734dc-1095">正規表現が `redirect-rule/(.*)` の要求のパスに一致し、パスが `/redirected/1234/5678` に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="734dc-1096">リダイレクト URL が、"*302 - 検出*" 状態コードでクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="734dc-1097">ブラウザーは、ブラウザーのアドレス バーに表示されるリダイレクト URL に新しい要求を実行します</span><span class="sxs-lookup"><span data-stu-id="734dc-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="734dc-1098">リダイレクト URL にはサンプル アプリに一致するルールがないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="734dc-1099">2 番目の要求は、アプリから *200 - OK* 応答を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="734dc-1100">応答の本文では、リダイレクト URL が示されています。</span><span class="sxs-lookup"><span data-stu-id="734dc-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="734dc-1101">URL が "*リダイレクト*" されるときに、サーバーへのラウンドトリップが実行されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="734dc-1102">リダイレクト ルールを設定するときは注意してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="734dc-1103">リダイレクト ルールは、リダイレクト後を含めて、アプリへのすべての要求で評価されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="734dc-1104">"*無限リダイレクトのループ*" が誤って作成されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="734dc-1105">元の要求: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="734dc-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="734dc-1107">かっこ内に含まれる式の一部は、*キャプチャ グループ*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="734dc-1108">式のドット (`.`) は、*どの文字とも一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="734dc-1109">アスタリスク (`*`) は、*直前の文字に 0 回以上一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="734dc-1110">そのため、URL の最後の 2 つのパス セグメント `1234/5678` は、キャプチャ グループ `(.*)` によってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="734dc-1111">要求 URL で `redirect-rule/` の後に指定した任意の値が、この単一のキャプチャ グループによってキャプチャされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="734dc-1112">置換文字列では、キャプチャされたグループがドル記号 (`$`) を使用して文字列に挿入され、その後にキャプチャのシーケンス番号が続きます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="734dc-1113">最初のキャプチャ グループ値は、`$1` で取得され、2 番目は `$2` で取得され、これらは正規表現内のキャプチャ グループの順序で続行されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="734dc-1114">サンプル アプリでは、リダイレクト ルールの正規表現内に 1 つのキャプチャ グループだけがあるので、置換文字列に 1 つの挿入されたグループ (`$1`) だけがあります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="734dc-1115">ルールが適用されるときに、URL `/redirected/1234/5678` になります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="734dc-1116">セキュリティで保護されたエンドポイントへの URL リダイレクト</span><span class="sxs-lookup"><span data-stu-id="734dc-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="734dc-1117">HTTP 要求を、同じホストとパスに HTTPS プロトコルを使用してリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="734dc-1118">状態コードが指定されていない場合、ミドルウェアは既定で "*302 - 検出*" に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="734dc-1119">ポートが指定されていない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="734dc-1120">ミドルウェアの既定値 `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="734dc-1121">スキームは `https` (HTTPS プロトコル) に変更されて、クライアントはポート 443 でリソースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="734dc-1122">次の例では、状態コードを "*301 - 完全な移動*" に設定し、ポートを 5001 に変更する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="734dc-1123">セキュリティで保護されていない要求を、セキュリティで保護された HTTPS プロトコル (ポート 443) を使用して同じホストとパスにリダイレクトするには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="734dc-1124">ミドルウェアは状態コードを "*301 -完全な移動"* に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="734dc-1125">リダイレクト規則を追加せずにセキュリティで保護されたエンドポイントにリダイレクトするときは、HTTPS リダイレクト ミドルウェアを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="734dc-1126">詳細については、「[HTTPS の適用](xref:security/enforcing-ssl#require-https)」のトピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="734dc-1127">サンプル アプリは、`AddRedirectToHttps` または `AddRedirectToHttpsPermanent` の使用方法の例を示すことができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="734dc-1128">拡張メソッドを `RewriteOptions` に追加します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="734dc-1129">任意の URL でセキュリティ保護されていない要求をアプリに対して行います。</span><span class="sxs-lookup"><span data-stu-id="734dc-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="734dc-1130">自己署名証明書が信頼できないことを示すブラウザーのセキュリティ警告を消去するか、証明書を信頼する例外を作成します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="734dc-1131">`AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="734dc-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="734dc-1133">`AddRedirectToHttpsPermanent`: `http://localhost:5000/secure` を使用する元の要求</span><span class="sxs-lookup"><span data-stu-id="734dc-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="734dc-1135">URL 書き換え</span><span class="sxs-lookup"><span data-stu-id="734dc-1135">URL rewrite</span></span>

<span data-ttu-id="734dc-1136">URL 書き換えのルールを作成するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="734dc-1137">最初のパラメーターには、着信 URL のパスに一致する正規表現が含まれています。</span><span class="sxs-lookup"><span data-stu-id="734dc-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="734dc-1138">2 番目のパラメーターは、置換文字列です。</span><span class="sxs-lookup"><span data-stu-id="734dc-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="734dc-1139">3 番目のパラメーター `skipRemainingRules: {true|false}` は、現在のルールが適用される場合に追加の書き換えルールをスキップするかどうかをミドルウェアに示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="734dc-1140">元の要求: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="734dc-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="734dc-1142">式の先頭にあるキャレット (`^`) は、URL パスの先頭からマッチングが開始されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="734dc-1143">前のリダイレクト ルール `redirect-rule/(.*)` の例では、正規表現の先頭にキャレット (`^`) がありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="734dc-1144">したがって、パスの `redirect-rule/` の前にどのような文字があっても一致します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="734dc-1145">パス</span><span class="sxs-lookup"><span data-stu-id="734dc-1145">Path</span></span>                               | <span data-ttu-id="734dc-1146">一致したもの</span><span class="sxs-lookup"><span data-stu-id="734dc-1146">Match</span></span> |
| ---
<span data-ttu-id="734dc-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1148">'Blazor'</span></span>
- <span data-ttu-id="734dc-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1149">'Identity'</span></span>
- <span data-ttu-id="734dc-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1151">'Razor'</span></span>
- <span data-ttu-id="734dc-1152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1154">'Blazor'</span></span>
- <span data-ttu-id="734dc-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1155">'Identity'</span></span>
- <span data-ttu-id="734dc-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1157">'Razor'</span></span>
- <span data-ttu-id="734dc-1158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1160">'Blazor'</span></span>
- <span data-ttu-id="734dc-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1161">'Identity'</span></span>
- <span data-ttu-id="734dc-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1163">'Razor'</span></span>
- <span data-ttu-id="734dc-1164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1166">'Blazor'</span></span>
- <span data-ttu-id="734dc-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1167">'Identity'</span></span>
- <span data-ttu-id="734dc-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1169">'Razor'</span></span>
- <span data-ttu-id="734dc-1170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1172">'Blazor'</span></span>
- <span data-ttu-id="734dc-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1173">'Identity'</span></span>
- <span data-ttu-id="734dc-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1175">'Razor'</span></span>
- <span data-ttu-id="734dc-1176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1178">'Blazor'</span></span>
- <span data-ttu-id="734dc-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1179">'Identity'</span></span>
- <span data-ttu-id="734dc-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1181">'Razor'</span></span>
- <span data-ttu-id="734dc-1182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1184">'Blazor'</span></span>
- <span data-ttu-id="734dc-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1185">'Identity'</span></span>
- <span data-ttu-id="734dc-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1187">'Razor'</span></span>
- <span data-ttu-id="734dc-1188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1190">'Blazor'</span></span>
- <span data-ttu-id="734dc-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1191">'Identity'</span></span>
- <span data-ttu-id="734dc-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1193">'Razor'</span></span>
- <span data-ttu-id="734dc-1194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1196">'Blazor'</span></span>
- <span data-ttu-id="734dc-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1197">'Identity'</span></span>
- <span data-ttu-id="734dc-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1199">'Razor'</span></span>
- <span data-ttu-id="734dc-1200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1202">'Blazor'</span></span>
- <span data-ttu-id="734dc-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1203">'Identity'</span></span>
- <span data-ttu-id="734dc-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1205">'Razor'</span></span>
- <span data-ttu-id="734dc-1206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1208">'Blazor'</span></span>
- <span data-ttu-id="734dc-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1209">'Identity'</span></span>
- <span data-ttu-id="734dc-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1211">'Razor'</span></span>
- <span data-ttu-id="734dc-1212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1214">'Blazor'</span></span>
- <span data-ttu-id="734dc-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1215">'Identity'</span></span>
- <span data-ttu-id="734dc-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1217">'Razor'</span></span>
- <span data-ttu-id="734dc-1218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1220">'Blazor'</span></span>
- <span data-ttu-id="734dc-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1221">'Identity'</span></span>
- <span data-ttu-id="734dc-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1223">'Razor'</span></span>
- <span data-ttu-id="734dc-1224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1226">'Blazor'</span></span>
- <span data-ttu-id="734dc-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1227">'Identity'</span></span>
- <span data-ttu-id="734dc-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1229">'Razor'</span></span>
- <span data-ttu-id="734dc-1230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1232">'Blazor'</span></span>
- <span data-ttu-id="734dc-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1233">'Identity'</span></span>
- <span data-ttu-id="734dc-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1235">'Razor'</span></span>
- <span data-ttu-id="734dc-1236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | はい   | | `/my-cool-redirect-rule/1234/5678` | はい   | | `/anotherredirect-rule/1234/5678`  | はい   |</span><span class="sxs-lookup"><span data-stu-id="734dc-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="734dc-1238">書き換えルール `^rewrite-rule/(\d+)/(\d+)` は、`rewrite-rule/` で始まる場合のみパスと一致します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="734dc-1239">次の表では、一致の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="734dc-1240">パス</span><span class="sxs-lookup"><span data-stu-id="734dc-1240">Path</span></span>                              | <span data-ttu-id="734dc-1241">一致したもの</span><span class="sxs-lookup"><span data-stu-id="734dc-1241">Match</span></span> |
| ---
<span data-ttu-id="734dc-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1243">'Blazor'</span></span>
- <span data-ttu-id="734dc-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1244">'Identity'</span></span>
- <span data-ttu-id="734dc-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1246">'Razor'</span></span>
- <span data-ttu-id="734dc-1247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1249">'Blazor'</span></span>
- <span data-ttu-id="734dc-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1250">'Identity'</span></span>
- <span data-ttu-id="734dc-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1252">'Razor'</span></span>
- <span data-ttu-id="734dc-1253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1255">'Blazor'</span></span>
- <span data-ttu-id="734dc-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1256">'Identity'</span></span>
- <span data-ttu-id="734dc-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1258">'Razor'</span></span>
- <span data-ttu-id="734dc-1259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1261">'Blazor'</span></span>
- <span data-ttu-id="734dc-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1262">'Identity'</span></span>
- <span data-ttu-id="734dc-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1264">'Razor'</span></span>
- <span data-ttu-id="734dc-1265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1267">'Blazor'</span></span>
- <span data-ttu-id="734dc-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1268">'Identity'</span></span>
- <span data-ttu-id="734dc-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1270">'Razor'</span></span>
- <span data-ttu-id="734dc-1271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1273">'Blazor'</span></span>
- <span data-ttu-id="734dc-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1274">'Identity'</span></span>
- <span data-ttu-id="734dc-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1276">'Razor'</span></span>
- <span data-ttu-id="734dc-1277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1279">'Blazor'</span></span>
- <span data-ttu-id="734dc-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1280">'Identity'</span></span>
- <span data-ttu-id="734dc-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1282">'Razor'</span></span>
- <span data-ttu-id="734dc-1283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1285">'Blazor'</span></span>
- <span data-ttu-id="734dc-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1286">'Identity'</span></span>
- <span data-ttu-id="734dc-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1288">'Razor'</span></span>
- <span data-ttu-id="734dc-1289">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1291">'Blazor'</span></span>
- <span data-ttu-id="734dc-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1292">'Identity'</span></span>
- <span data-ttu-id="734dc-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1294">'Razor'</span></span>
- <span data-ttu-id="734dc-1295">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1297">'Blazor'</span></span>
- <span data-ttu-id="734dc-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1298">'Identity'</span></span>
- <span data-ttu-id="734dc-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1300">'Razor'</span></span>
- <span data-ttu-id="734dc-1301">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1303">'Blazor'</span></span>
- <span data-ttu-id="734dc-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1304">'Identity'</span></span>
- <span data-ttu-id="734dc-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1306">'Razor'</span></span>
- <span data-ttu-id="734dc-1307">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1309">'Blazor'</span></span>
- <span data-ttu-id="734dc-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1310">'Identity'</span></span>
- <span data-ttu-id="734dc-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1312">'Razor'</span></span>
- <span data-ttu-id="734dc-1313">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1315">'Blazor'</span></span>
- <span data-ttu-id="734dc-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1316">'Identity'</span></span>
- <span data-ttu-id="734dc-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1318">'Razor'</span></span>
- <span data-ttu-id="734dc-1319">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1321">'Blazor'</span></span>
- <span data-ttu-id="734dc-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1322">'Identity'</span></span>
- <span data-ttu-id="734dc-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1324">'Razor'</span></span>
- <span data-ttu-id="734dc-1325">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | はい   | | `/my-cool-rewrite-rule/1234/5678` | いいえ    | | `/anotherrewrite-rule/1234/5678`  | いいえ    |</span><span class="sxs-lookup"><span data-stu-id="734dc-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="734dc-1327">式の `^rewrite-rule/` に続く部分に、2 つのキャプチャ グループ `(\d+)/(\d+)` があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="734dc-1328">`\d` は、*1 桁 (数字) に一致する*ことを示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="734dc-1329">プラス記号 (`+`) は、*直前の 1 つ以上の文字に一致する*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="734dc-1330">そのため、URL は、数字とその後に続くスラッシュ、さらにその後に続く別の数字を含んでいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="734dc-1331">これらのキャプチャ グループが `$1` および `$2` として書き換えられた URL に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="734dc-1332">書き換えルールの置換文字列は、クエリ文字列にキャプチャされたグループを配置します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="734dc-1333">`/rewrite-rule/1234/5678` の要求されたパスは、`/rewritten?var1=1234&var2=5678` でリソースを取得するように書き換えられます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="734dc-1334">クエリ文字列が元の要求に存在する場合、URL が書き換えられるときに保持されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="734dc-1335">リソースを取得するためのサーバーへのラウンドトリップはありません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="734dc-1336">リソースが存在する場合は、取得され、*200 - OK* 状態コードと共にクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="734dc-1337">クライアントはリダイレクトされていないため、ブラウザーのアドレス バーの URL は変更されません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="734dc-1338">クライアントは、URL の書き換え操作がサーバーで発生したことを検出できません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-1339">式内の照合ルールは計算量が多く、アプリの応答時間が長くなるので、可能な限り `skipRemainingRules: true` を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="734dc-1340">最も高速なアプリの応答を実現するには以下のようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="734dc-1341">一致する頻度が最も多いルールから一致頻度が最も少ないルールへの順番で書き換えルールを並べ替えます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="734dc-1342">一致が発生し、追加の処理が必要ないときに残りのルールの処理をスキップします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="734dc-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="734dc-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="734dc-1344"><xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> を使用して Apache mod_rewrite ルールを適用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="734dc-1345">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="734dc-1346">mod_rewrite ルールの情報と例については、「[Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="734dc-1347">*ApacheModRewrite.txt* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="734dc-1348">サンプル アプリは、要求を `/apache-mod-rules-redirect/(.\*)` から `/redirected?id=$1` にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="734dc-1349">応答の状態コードは "*302 -検出*" です。</span><span class="sxs-lookup"><span data-stu-id="734dc-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="734dc-1350">元の要求: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="734dc-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="734dc-1352">ミドルウェアは、次の Apache mod_rewrite サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="734dc-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="734dc-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="734dc-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="734dc-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="734dc-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="734dc-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="734dc-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="734dc-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="734dc-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="734dc-1358">HTTP_HOST</span></span>
* <span data-ttu-id="734dc-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="734dc-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="734dc-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="734dc-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="734dc-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="734dc-1361">HTTPS</span></span>
* <span data-ttu-id="734dc-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="734dc-1362">IPV6</span></span>
* <span data-ttu-id="734dc-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="734dc-1363">QUERY_STRING</span></span>
* <span data-ttu-id="734dc-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="734dc-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="734dc-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="734dc-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="734dc-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="734dc-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="734dc-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="734dc-1369">REQUEST_URI</span></span>
* <span data-ttu-id="734dc-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="734dc-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="734dc-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-1372">SERVER_PORT</span></span>
* <span data-ttu-id="734dc-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="734dc-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="734dc-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="734dc-1374">TIME</span></span>
* <span data-ttu-id="734dc-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="734dc-1375">TIME_DAY</span></span>
* <span data-ttu-id="734dc-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="734dc-1376">TIME_HOUR</span></span>
* <span data-ttu-id="734dc-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="734dc-1377">TIME_MIN</span></span>
* <span data-ttu-id="734dc-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="734dc-1378">TIME_MON</span></span>
* <span data-ttu-id="734dc-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="734dc-1379">TIME_SEC</span></span>
* <span data-ttu-id="734dc-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="734dc-1380">TIME_WDAY</span></span>
* <span data-ttu-id="734dc-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="734dc-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="734dc-1382">IIS URL リライト モジュールのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="734dc-1383">IIS URL リライト モジュールに適用される同じルール セットを使用するには、<xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="734dc-1384">ルール ファイルがアプリと共に展開されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="734dc-1385">Windows Server IIS で実行されているときにミドルウェアでアプリの *web.config* ファイルを使用するように指定しないでください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="734dc-1386">IIS を使用する場合、IIS リライト モジュールとの競合を避けるため、これらのルールをアプリの *web.config* の外部に保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="734dc-1387">IIS URL リライト モジュールのルールの詳細と例については、「[Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)」(URL リライト モジュール 2.0 の使用 ) と「[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)」(URL リライト モジュール構成リファレンス) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="734dc-1388">*IISUrlRewrite.xml* ルール ファイルからルールを読み取るには、<xref:System.IO.StreamReader> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="734dc-1389">サンプル アプリは、要求を `/iis-rules-rewrite/(.*)` から `/rewritten?id=$1` に書き換えます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="734dc-1390">応答は、*200 - OK* 状態コードでクライアントに送信されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="734dc-1391">元の要求: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="734dc-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="734dc-1393">望ましくない方法でアプリに影響するように構成されたサーバー レベル ルールを使用するアクティブな IIS リライト モジュールがある場合は、アプリに対して IIS リライト モジュールを無効にできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="734dc-1394">詳細については、「[Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules)」 (IIS モジュールの無効化) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="734dc-1395">サポートされていない機能</span><span class="sxs-lookup"><span data-stu-id="734dc-1395">Unsupported features</span></span>

<span data-ttu-id="734dc-1396">ASP.NET Core 2.x でリリースされたミドルウェアは、次の IIS URL リライト モジュールの機能をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="734dc-1397">送信ルール</span><span class="sxs-lookup"><span data-stu-id="734dc-1397">Outbound Rules</span></span>
* <span data-ttu-id="734dc-1398">カスタム サーバー変数</span><span class="sxs-lookup"><span data-stu-id="734dc-1398">Custom Server Variables</span></span>
* <span data-ttu-id="734dc-1399">ワイルドカード</span><span class="sxs-lookup"><span data-stu-id="734dc-1399">Wildcards</span></span>
* <span data-ttu-id="734dc-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="734dc-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="734dc-1401">サポートされるサーバー変数</span><span class="sxs-lookup"><span data-stu-id="734dc-1401">Supported server variables</span></span>

<span data-ttu-id="734dc-1402">ミドルウェアは、次の IIS URL リライト モジュール サーバー変数をサポートします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="734dc-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="734dc-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="734dc-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="734dc-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="734dc-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="734dc-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="734dc-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="734dc-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="734dc-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="734dc-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="734dc-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="734dc-1408">HTTP_HOST</span></span>
* <span data-ttu-id="734dc-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="734dc-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="734dc-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="734dc-1410">HTTP_URL</span></span>
* <span data-ttu-id="734dc-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="734dc-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="734dc-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="734dc-1412">HTTPS</span></span>
* <span data-ttu-id="734dc-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="734dc-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="734dc-1414">QUERY_STRING</span></span>
* <span data-ttu-id="734dc-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="734dc-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="734dc-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="734dc-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="734dc-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="734dc-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="734dc-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="734dc-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="734dc-1419"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> を介して <xref:Microsoft.Extensions.FileProviders.IFileProvider> を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="734dc-1420">このアプローチは、書き換えルール ファイルの場所に関する大きな柔軟性を提供する場合があります。</span><span class="sxs-lookup"><span data-stu-id="734dc-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="734dc-1421">書き換えルール ファイルを指定したパスでサーバーに導入されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="734dc-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="734dc-1422">メソッド ベースのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-1422">Method-based rule</span></span>

<span data-ttu-id="734dc-1423">メソッド内で独自のルール ロジックを実装するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="734dc-1424">`Add` は <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> を公開し、メソッドで <xref:Microsoft.AspNetCore.Http.HttpContext> を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="734dc-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="734dc-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) は、追加のパイプライン処理の実行方法を決定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="734dc-1426">次の表で説明する <xref:Microsoft.AspNetCore.Rewrite.RuleResult> フィールドのいずれかに値を設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="734dc-1427">アクション</span><span class="sxs-lookup"><span data-stu-id="734dc-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="734dc-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1429">'Blazor'</span></span>
- <span data-ttu-id="734dc-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1430">'Identity'</span></span>
- <span data-ttu-id="734dc-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1432">'Razor'</span></span>
- <span data-ttu-id="734dc-1433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1435">'Blazor'</span></span>
- <span data-ttu-id="734dc-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1436">'Identity'</span></span>
- <span data-ttu-id="734dc-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1438">'Razor'</span></span>
- <span data-ttu-id="734dc-1439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1441">'Blazor'</span></span>
- <span data-ttu-id="734dc-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1442">'Identity'</span></span>
- <span data-ttu-id="734dc-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1444">'Razor'</span></span>
- <span data-ttu-id="734dc-1445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1447">'Blazor'</span></span>
- <span data-ttu-id="734dc-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1448">'Identity'</span></span>
- <span data-ttu-id="734dc-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1450">'Razor'</span></span>
- <span data-ttu-id="734dc-1451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1453">'Blazor'</span></span>
- <span data-ttu-id="734dc-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1454">'Identity'</span></span>
- <span data-ttu-id="734dc-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1456">'Razor'</span></span>
- <span data-ttu-id="734dc-1457">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1459">'Blazor'</span></span>
- <span data-ttu-id="734dc-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1460">'Identity'</span></span>
- <span data-ttu-id="734dc-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1462">'Razor'</span></span>
- <span data-ttu-id="734dc-1463">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1465">'Blazor'</span></span>
- <span data-ttu-id="734dc-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1466">'Identity'</span></span>
- <span data-ttu-id="734dc-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1468">'Razor'</span></span>
- <span data-ttu-id="734dc-1469">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1471">'Blazor'</span></span>
- <span data-ttu-id="734dc-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1472">'Identity'</span></span>
- <span data-ttu-id="734dc-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1474">'Razor'</span></span>
- <span data-ttu-id="734dc-1475">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1477">'Blazor'</span></span>
- <span data-ttu-id="734dc-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1478">'Identity'</span></span>
- <span data-ttu-id="734dc-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1480">'Razor'</span></span>
- <span data-ttu-id="734dc-1481">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1483">'Blazor'</span></span>
- <span data-ttu-id="734dc-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1484">'Identity'</span></span>
- <span data-ttu-id="734dc-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1486">'Razor'</span></span>
- <span data-ttu-id="734dc-1487">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1489">'Blazor'</span></span>
- <span data-ttu-id="734dc-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1490">'Identity'</span></span>
- <span data-ttu-id="734dc-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1492">'Razor'</span></span>
- <span data-ttu-id="734dc-1493">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1495">'Blazor'</span></span>
- <span data-ttu-id="734dc-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1496">'Identity'</span></span>
- <span data-ttu-id="734dc-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1498">'Razor'</span></span>
- <span data-ttu-id="734dc-1499">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1501">'Blazor'</span></span>
- <span data-ttu-id="734dc-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1502">'Identity'</span></span>
- <span data-ttu-id="734dc-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1504">'Razor'</span></span>
- <span data-ttu-id="734dc-1505">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1507">'Blazor'</span></span>
- <span data-ttu-id="734dc-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1508">'Identity'</span></span>
- <span data-ttu-id="734dc-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1510">'Razor'</span></span>
- <span data-ttu-id="734dc-1511">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1513">'Blazor'</span></span>
- <span data-ttu-id="734dc-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1514">'Identity'</span></span>
- <span data-ttu-id="734dc-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1516">'Razor'</span></span>
- <span data-ttu-id="734dc-1517">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1519">'Blazor'</span></span>
- <span data-ttu-id="734dc-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1520">'Identity'</span></span>
- <span data-ttu-id="734dc-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1522">'Razor'</span></span>
- <span data-ttu-id="734dc-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1525">'Blazor'</span></span>
- <span data-ttu-id="734dc-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1526">'Identity'</span></span>
- <span data-ttu-id="734dc-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1528">'Razor'</span></span>
- <span data-ttu-id="734dc-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1531">'Blazor'</span></span>
- <span data-ttu-id="734dc-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1532">'Identity'</span></span>
- <span data-ttu-id="734dc-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1534">'Razor'</span></span>
- <span data-ttu-id="734dc-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1537">'Blazor'</span></span>
- <span data-ttu-id="734dc-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1538">'Identity'</span></span>
- <span data-ttu-id="734dc-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1540">'Razor'</span></span>
- <span data-ttu-id="734dc-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1543">'Blazor'</span></span>
- <span data-ttu-id="734dc-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1544">'Identity'</span></span>
- <span data-ttu-id="734dc-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1546">'Razor'</span></span>
- <span data-ttu-id="734dc-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1549">'Blazor'</span></span>
- <span data-ttu-id="734dc-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1550">'Identity'</span></span>
- <span data-ttu-id="734dc-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1552">'Razor'</span></span>
- <span data-ttu-id="734dc-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1555">'Blazor'</span></span>
- <span data-ttu-id="734dc-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1556">'Identity'</span></span>
- <span data-ttu-id="734dc-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1558">'Razor'</span></span>
- <span data-ttu-id="734dc-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1561">'Blazor'</span></span>
- <span data-ttu-id="734dc-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1562">'Identity'</span></span>
- <span data-ttu-id="734dc-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1564">'Razor'</span></span>
- <span data-ttu-id="734dc-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1567">'Blazor'</span></span>
- <span data-ttu-id="734dc-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1568">'Identity'</span></span>
- <span data-ttu-id="734dc-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1570">'Razor'</span></span>
- <span data-ttu-id="734dc-1571">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1573">'Blazor'</span></span>
- <span data-ttu-id="734dc-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1574">'Identity'</span></span>
- <span data-ttu-id="734dc-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1576">'Razor'</span></span>
- <span data-ttu-id="734dc-1577">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1579">'Blazor'</span></span>
- <span data-ttu-id="734dc-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1580">'Identity'</span></span>
- <span data-ttu-id="734dc-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1582">'Razor'</span></span>
- <span data-ttu-id="734dc-1583">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1585">'Blazor'</span></span>
- <span data-ttu-id="734dc-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1586">'Identity'</span></span>
- <span data-ttu-id="734dc-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1588">'Razor'</span></span>
- <span data-ttu-id="734dc-1589">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1591">'Blazor'</span></span>
- <span data-ttu-id="734dc-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1592">'Identity'</span></span>
- <span data-ttu-id="734dc-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1594">'Razor'</span></span>
- <span data-ttu-id="734dc-1595">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1597">'Blazor'</span></span>
- <span data-ttu-id="734dc-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1598">'Identity'</span></span>
- <span data-ttu-id="734dc-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1600">'Razor'</span></span>
- <span data-ttu-id="734dc-1601">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1603">'Blazor'</span></span>
- <span data-ttu-id="734dc-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1604">'Identity'</span></span>
- <span data-ttu-id="734dc-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1606">'Razor'</span></span>
- <span data-ttu-id="734dc-1607">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1609">'Blazor'</span></span>
- <span data-ttu-id="734dc-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1610">'Identity'</span></span>
- <span data-ttu-id="734dc-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1612">'Razor'</span></span>
- <span data-ttu-id="734dc-1613">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1615">'Blazor'</span></span>
- <span data-ttu-id="734dc-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1616">'Identity'</span></span>
- <span data-ttu-id="734dc-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1618">'Razor'</span></span>
- <span data-ttu-id="734dc-1619">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1621">'Blazor'</span></span>
- <span data-ttu-id="734dc-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1622">'Identity'</span></span>
- <span data-ttu-id="734dc-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1624">'Razor'</span></span>
- <span data-ttu-id="734dc-1625">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1627">'Blazor'</span></span>
- <span data-ttu-id="734dc-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1628">'Identity'</span></span>
- <span data-ttu-id="734dc-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1630">'Razor'</span></span>
- <span data-ttu-id="734dc-1631">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1633">'Blazor'</span></span>
- <span data-ttu-id="734dc-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1634">'Identity'</span></span>
- <span data-ttu-id="734dc-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1636">'Razor'</span></span>
- <span data-ttu-id="734dc-1637">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1639">'Blazor'</span></span>
- <span data-ttu-id="734dc-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1640">'Identity'</span></span>
- <span data-ttu-id="734dc-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1642">'Razor'</span></span>
- <span data-ttu-id="734dc-1643">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1645">'Blazor'</span></span>
- <span data-ttu-id="734dc-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1646">'Identity'</span></span>
- <span data-ttu-id="734dc-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1648">'Razor'</span></span>
- <span data-ttu-id="734dc-1649">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1651">'Blazor'</span></span>
- <span data-ttu-id="734dc-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1652">'Identity'</span></span>
- <span data-ttu-id="734dc-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1654">'Razor'</span></span>
- <span data-ttu-id="734dc-1655">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1657">'Blazor'</span></span>
- <span data-ttu-id="734dc-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1658">'Identity'</span></span>
- <span data-ttu-id="734dc-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1660">'Razor'</span></span>
- <span data-ttu-id="734dc-1661">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1663">'Blazor'</span></span>
- <span data-ttu-id="734dc-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1664">'Identity'</span></span>
- <span data-ttu-id="734dc-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1666">'Razor'</span></span>
- <span data-ttu-id="734dc-1667">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1669">'Blazor'</span></span>
- <span data-ttu-id="734dc-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1670">'Identity'</span></span>
- <span data-ttu-id="734dc-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1672">'Razor'</span></span>
- <span data-ttu-id="734dc-1673">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1675">'Blazor'</span></span>
- <span data-ttu-id="734dc-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1676">'Identity'</span></span>
- <span data-ttu-id="734dc-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1678">'Razor'</span></span>
- <span data-ttu-id="734dc-1679">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1681">'Blazor'</span></span>
- <span data-ttu-id="734dc-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1682">'Identity'</span></span>
- <span data-ttu-id="734dc-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1684">'Razor'</span></span>
- <span data-ttu-id="734dc-1685">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1687">'Blazor'</span></span>
- <span data-ttu-id="734dc-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1688">'Identity'</span></span>
- <span data-ttu-id="734dc-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1690">'Razor'</span></span>
- <span data-ttu-id="734dc-1691">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1693">'Blazor'</span></span>
- <span data-ttu-id="734dc-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1694">'Identity'</span></span>
- <span data-ttu-id="734dc-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1696">'Razor'</span></span>
- <span data-ttu-id="734dc-1697">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1699">'Blazor'</span></span>
- <span data-ttu-id="734dc-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1700">'Identity'</span></span>
- <span data-ttu-id="734dc-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1702">'Razor'</span></span>
- <span data-ttu-id="734dc-1703">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1704">-------------------------------- | | `RuleResult.ContinueRules` (既定) | ルールの適用を続けます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="734dc-1705">| | `RuleResult.EndResponse`             | ルールの適用を停止し、応答を送信します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="734dc-1706">| | `RuleResult.SkipRemainingRules`      | ルールの適用を停止し、次のミドルウェアにコンテキストを送信します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="734dc-1707">サンプル アプリは、 *.xml* で終了するパスの要求をリダイレクトするメソッドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="734dc-1708">`/file.xml` に対して要求が行われた場合、要求は `/xmlfiles/file.xml` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="734dc-1709">状態コードは "*301 - 完全な移動*" に設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="734dc-1710">ブラウザーが */xmlfiles/file.xml* に対する新しい要求を行うと、静的ファイル ミドルウェアはクライアントに *wwwroot/xmlfiles* フォルダーからファイルを提供します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="734dc-1711">リダイレクトの場合は、応答の状態コードを明示的に設定します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="734dc-1712">それ以外の場合は、*200 - OK* 状態コードが返され、クライアントでのリダイレクトは発生しません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="734dc-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="734dc-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="734dc-1714">このアプローチでは、要求を書き換えることもできます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="734dc-1715">サンプル アプリでは、*wwwroot* フォルダーから *file.txt* テキスト ファイルを提供するためにテキスト ファイル要求のパスを書き換える処理が示されています。</span><span class="sxs-lookup"><span data-stu-id="734dc-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="734dc-1716">静的ファイル ミドルウェアでは、更新された要求パスに基づいてファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="734dc-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="734dc-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="734dc-1718">IRule ベースのルール</span><span class="sxs-lookup"><span data-stu-id="734dc-1718">IRule-based rule</span></span>

<span data-ttu-id="734dc-1719"><xref:Microsoft.AspNetCore.Rewrite.IRule> インターフェイスを実装するクラス内のルール ロジックを使用するには、<xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> を使用します。</span><span class="sxs-lookup"><span data-stu-id="734dc-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="734dc-1720">`IRule` では、メソッド ベースのルール アプローチを使用する場合より高い柔軟性が提供されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="734dc-1721">実装クラスには、<xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> メソッドに対してパラメーターを渡すことができるコンストラクターを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="734dc-1722">サンプル アプリの `extension` と `newPath` のパラメーターの値は、いくつかの条件を満たすかチェックされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="734dc-1723">`extension` は、値を含んでいる必要があり、値は *.png*、 *.jpg*、または *.gif* でなければなりません。</span><span class="sxs-lookup"><span data-stu-id="734dc-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="734dc-1724">`newPath` が有効ではない場合、<xref:System.ArgumentException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="734dc-1725">*image.png* に対して行われた要求は、`/png-images/image.png` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="734dc-1726">*image.jpg* に対して行われた要求は、`/jpg-images/image.jpg` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="734dc-1727">状態コードは "*301 - 完全な移動*" に設定され、`context.Result` はルールの処理を停止して応答を送信するように設定されます。</span><span class="sxs-lookup"><span data-stu-id="734dc-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="734dc-1728">元の要求: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="734dc-1728">Original Request: `/image.png`</span></span>

![image.png の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="734dc-1730">元の要求: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="734dc-1730">Original Request: `/image.jpg`</span></span>

![image.jpg の要求および応答を追跡する開発者ツールを備えたブラウザー ウィンドウ](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="734dc-1732">正規表現の例</span><span class="sxs-lookup"><span data-stu-id="734dc-1732">Regex examples</span></span>

| <span data-ttu-id="734dc-1733">Goal</span><span class="sxs-lookup"><span data-stu-id="734dc-1733">Goal</span></span> | <span data-ttu-id="734dc-1734">正規表現文字列と</span><span class="sxs-lookup"><span data-stu-id="734dc-1734">Regex String &</span></span><br><span data-ttu-id="734dc-1735">一致の例</span><span class="sxs-lookup"><span data-stu-id="734dc-1735">Match Example</span></span> | <span data-ttu-id="734dc-1736">置換文字列と</span><span class="sxs-lookup"><span data-stu-id="734dc-1736">Replacement String &</span></span><br><span data-ttu-id="734dc-1737">出力の例</span><span class="sxs-lookup"><span data-stu-id="734dc-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="734dc-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1739">'Blazor'</span></span>
- <span data-ttu-id="734dc-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1740">'Identity'</span></span>
- <span data-ttu-id="734dc-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1742">'Razor'</span></span>
- <span data-ttu-id="734dc-1743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1745">'Blazor'</span></span>
- <span data-ttu-id="734dc-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1746">'Identity'</span></span>
- <span data-ttu-id="734dc-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1748">'Razor'</span></span>
- <span data-ttu-id="734dc-1749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1751">'Blazor'</span></span>
- <span data-ttu-id="734dc-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1752">'Identity'</span></span>
- <span data-ttu-id="734dc-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1754">'Razor'</span></span>
- <span data-ttu-id="734dc-1755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1757">'Blazor'</span></span>
- <span data-ttu-id="734dc-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1758">'Identity'</span></span>
- <span data-ttu-id="734dc-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1760">'Razor'</span></span>
- <span data-ttu-id="734dc-1761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1763">'Blazor'</span></span>
- <span data-ttu-id="734dc-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1764">'Identity'</span></span>
- <span data-ttu-id="734dc-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1766">'Razor'</span></span>
- <span data-ttu-id="734dc-1767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1769">'Blazor'</span></span>
- <span data-ttu-id="734dc-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1770">'Identity'</span></span>
- <span data-ttu-id="734dc-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1772">'Razor'</span></span>
- <span data-ttu-id="734dc-1773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1775">'Blazor'</span></span>
- <span data-ttu-id="734dc-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1776">'Identity'</span></span>
- <span data-ttu-id="734dc-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1778">'Razor'</span></span>
- <span data-ttu-id="734dc-1779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1781">'Blazor'</span></span>
- <span data-ttu-id="734dc-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1782">'Identity'</span></span>
- <span data-ttu-id="734dc-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1784">'Razor'</span></span>
- <span data-ttu-id="734dc-1785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1787">'Blazor'</span></span>
- <span data-ttu-id="734dc-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1788">'Identity'</span></span>
- <span data-ttu-id="734dc-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1790">'Razor'</span></span>
- <span data-ttu-id="734dc-1791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1793">'Blazor'</span></span>
- <span data-ttu-id="734dc-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1794">'Identity'</span></span>
- <span data-ttu-id="734dc-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1796">'Razor'</span></span>
- <span data-ttu-id="734dc-1797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1799">'Blazor'</span></span>
- <span data-ttu-id="734dc-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1800">'Identity'</span></span>
- <span data-ttu-id="734dc-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1802">'Razor'</span></span>
- <span data-ttu-id="734dc-1803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1805">'Blazor'</span></span>
- <span data-ttu-id="734dc-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1806">'Identity'</span></span>
- <span data-ttu-id="734dc-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1808">'Razor'</span></span>
- <span data-ttu-id="734dc-1809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1811">'Blazor'</span></span>
- <span data-ttu-id="734dc-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1812">'Identity'</span></span>
- <span data-ttu-id="734dc-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1814">'Razor'</span></span>
- <span data-ttu-id="734dc-1815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1817">'Blazor'</span></span>
- <span data-ttu-id="734dc-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1818">'Identity'</span></span>
- <span data-ttu-id="734dc-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1820">'Razor'</span></span>
- <span data-ttu-id="734dc-1821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1823">'Blazor'</span></span>
- <span data-ttu-id="734dc-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1824">'Identity'</span></span>
- <span data-ttu-id="734dc-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1826">'Razor'</span></span>
- <span data-ttu-id="734dc-1827">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1829">'Blazor'</span></span>
- <span data-ttu-id="734dc-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1830">'Identity'</span></span>
- <span data-ttu-id="734dc-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1832">'Razor'</span></span>
- <span data-ttu-id="734dc-1833">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1835">'Blazor'</span></span>
- <span data-ttu-id="734dc-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1836">'Identity'</span></span>
- <span data-ttu-id="734dc-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1838">'Razor'</span></span>
- <span data-ttu-id="734dc-1839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1841">'Blazor'</span></span>
- <span data-ttu-id="734dc-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1842">'Identity'</span></span>
- <span data-ttu-id="734dc-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1844">'Razor'</span></span>
- <span data-ttu-id="734dc-1845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1847">'Blazor'</span></span>
- <span data-ttu-id="734dc-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1848">'Identity'</span></span>
- <span data-ttu-id="734dc-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1850">'Razor'</span></span>
- <span data-ttu-id="734dc-1851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1853">'Blazor'</span></span>
- <span data-ttu-id="734dc-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1854">'Identity'</span></span>
- <span data-ttu-id="734dc-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1856">'Razor'</span></span>
- <span data-ttu-id="734dc-1857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1859">'Blazor'</span></span>
- <span data-ttu-id="734dc-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1860">'Identity'</span></span>
- <span data-ttu-id="734dc-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1862">'Razor'</span></span>
- <span data-ttu-id="734dc-1863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1865">'Blazor'</span></span>
- <span data-ttu-id="734dc-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1866">'Identity'</span></span>
- <span data-ttu-id="734dc-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1868">'Razor'</span></span>
- <span data-ttu-id="734dc-1869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1871">'Blazor'</span></span>
- <span data-ttu-id="734dc-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1872">'Identity'</span></span>
- <span data-ttu-id="734dc-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1874">'Razor'</span></span>
- <span data-ttu-id="734dc-1875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1877">'Blazor'</span></span>
- <span data-ttu-id="734dc-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1878">'Identity'</span></span>
- <span data-ttu-id="734dc-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1880">'Razor'</span></span>
- <span data-ttu-id="734dc-1881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1883">'Blazor'</span></span>
- <span data-ttu-id="734dc-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1884">'Identity'</span></span>
- <span data-ttu-id="734dc-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1886">'Razor'</span></span>
- <span data-ttu-id="734dc-1887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1889">'Blazor'</span></span>
- <span data-ttu-id="734dc-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1890">'Identity'</span></span>
- <span data-ttu-id="734dc-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1892">'Razor'</span></span>
- <span data-ttu-id="734dc-1893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1895">'Blazor'</span></span>
- <span data-ttu-id="734dc-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1896">'Identity'</span></span>
- <span data-ttu-id="734dc-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1898">'Razor'</span></span>
- <span data-ttu-id="734dc-1899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1901">'Blazor'</span></span>
- <span data-ttu-id="734dc-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1902">'Identity'</span></span>
- <span data-ttu-id="734dc-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1904">'Razor'</span></span>
- <span data-ttu-id="734dc-1905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1907">'Blazor'</span></span>
- <span data-ttu-id="734dc-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1908">'Identity'</span></span>
- <span data-ttu-id="734dc-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1910">'Razor'</span></span>
- <span data-ttu-id="734dc-1911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="734dc-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="734dc-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="734dc-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1913">'Blazor'</span></span>
- <span data-ttu-id="734dc-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="734dc-1914">'Identity'</span></span>
- <span data-ttu-id="734dc-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="734dc-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="734dc-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="734dc-1916">'Razor'</span></span>
- <span data-ttu-id="734dc-1917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="734dc-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="734dc-1918">------------------- | | パスをクエリ文字列に書き換える | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="734dc-1919">`/path?var1=abc&var2=123` | | 末尾のスラッシュを除去する | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="734dc-1920">`/path` | | 末尾のスラッシュを強制する | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="734dc-1921">`/path/` | | 特定の要求を書き換えを回避する | `^(.*)(?<!\.axd)$` または `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="734dc-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="734dc-1922">はい: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="734dc-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="734dc-1923">いいえ: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="734dc-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="734dc-1924">`/resource.axd` | | URL セグメントを再配置する | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="734dc-1925">`path/3/2/1` | | URL セグメントを置き換える | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="734dc-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="734dc-1926">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="734dc-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="734dc-1927">.NET の正規表現</span><span class="sxs-lookup"><span data-stu-id="734dc-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="734dc-1928">正規表現言語 - クイック リファレンス</span><span class="sxs-lookup"><span data-stu-id="734dc-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="734dc-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="734dc-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="734dc-1930">URL リライト モジュール 2.0 (IIS 用) の使用</span><span class="sxs-lookup"><span data-stu-id="734dc-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="734dc-1931">URL リライト モジュール構成リファレンス</span><span class="sxs-lookup"><span data-stu-id="734dc-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="734dc-1932">IIS URL リライト モジュール フォーラム</span><span class="sxs-lookup"><span data-stu-id="734dc-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="734dc-1933">単純な URL 構造を保持する</span><span class="sxs-lookup"><span data-stu-id="734dc-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="734dc-1934">URL 書き換えの 10 のヒントとテクニック</span><span class="sxs-lookup"><span data-stu-id="734dc-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="734dc-1935">スラッシュを使用すべきかどうか</span><span class="sxs-lookup"><span data-stu-id="734dc-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
