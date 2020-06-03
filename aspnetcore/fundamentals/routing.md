---
<span data-ttu-id="23763-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-102">'Blazor'</span></span>
- <span data-ttu-id="23763-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-103">'Identity'</span></span>
- <span data-ttu-id="23763-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-105">'Razor'</span></span>
- <span data-ttu-id="23763-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-106">'SignalR' uid:</span></span> 

---
# <a name="routing-in-aspnet-core"></a><span data-ttu-id="23763-107">ASP.NET Core のルーティング</span><span class="sxs-lookup"><span data-stu-id="23763-107">Routing in ASP.NET Core</span></span>

<span data-ttu-id="23763-108">作成者: [Ryan Nowak](https://github.com/rynowak)、[Kirk Larkin](https://twitter.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23763-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23763-109">ルーティングの役割は、受信した HTTP 要求を照合し、それらの要求をアプリの実行可能なエンドポイントにディスパッチすることです。</span><span class="sxs-lookup"><span data-stu-id="23763-109">Routing is responsible for matching incoming HTTP requests and dispatching those requests to the app's executable endpoints.</span></span> <span data-ttu-id="23763-110">[エンドポイント](#endpoint)は、アプリの実行可能な要求処理コードの単位です。</span><span class="sxs-lookup"><span data-stu-id="23763-110">[Endpoints](#endpoint) are the app's units of executable request-handling code.</span></span> <span data-ttu-id="23763-111">エンドポイントはアプリで定義され、アプリの起動時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-111">Endpoints are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="23763-112">エンドポイントの照合プロセスでは、要求の URL から値を抽出し、それらの値を要求の処理に提供できます。</span><span class="sxs-lookup"><span data-stu-id="23763-112">The endpoint matching process can extract values from the request's URL and provide those values for request processing.</span></span> <span data-ttu-id="23763-113">アプリからのルート情報を使用して、ルーティングでエンドポイントにマップする URL を生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="23763-113">Using endpoint information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="23763-114">アプリでは、次のものを使用してルーティングを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-114">Apps can configure routing using:</span></span>

- <span data-ttu-id="23763-115">Controllers</span><span class="sxs-lookup"><span data-stu-id="23763-115">Controllers</span></span>
- Razor<span data-ttu-id="23763-116"> Pages</span><span class="sxs-lookup"><span data-stu-id="23763-116"> Pages</span></span>
- SignalR
- <span data-ttu-id="23763-117">gRPC サービス</span><span class="sxs-lookup"><span data-stu-id="23763-117">gRPC Services</span></span>
- <span data-ttu-id="23763-118">エンドポイント対応の[ミドルウェア](xref:fundamentals/middleware/index) ([正常性チェックなど)](xref:host-and-deploy/health-checks)。</span><span class="sxs-lookup"><span data-stu-id="23763-118">Endpoint-enabled [middleware](xref:fundamentals/middleware/index) such as [Health Checks](xref:host-and-deploy/health-checks).</span></span>
- <span data-ttu-id="23763-119">ルーティングに登録されているデリゲートとラムダ。</span><span class="sxs-lookup"><span data-stu-id="23763-119">Delegates and lambdas registered with routing.</span></span>

<span data-ttu-id="23763-120">このドキュメントでは、ASP.NET Core のルーティングについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-120">This document covers low-level details of ASP.NET Core routing.</span></span> <span data-ttu-id="23763-121">ルーティングの構成については、以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-121">For information on configuring routing:</span></span>

* <span data-ttu-id="23763-122">コントローラーについては、<xref:mvc/controllers/routing> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-122">For controllers, see <xref:mvc/controllers/routing>.</span></span>
* <span data-ttu-id="23763-123">Razor Pages の規則については、「<xref:razor-pages/razor-pages-conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-123">For Razor Pages conventions, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="23763-124">このドキュメントで説明されているエンドポイント ルーティング システムは、ASP.NET Core 3.0 以降に適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-124">The endpoint routing system described in this document applies to ASP.NET Core 3.0 and later.</span></span> <span data-ttu-id="23763-125"><xref:Microsoft.AspNetCore.Routing.IRouter> に基づく以前のルーティング システムについては、次のいずれかの方法を使用して ASP.NET Core 2.1 バージョンを選択してください。</span><span class="sxs-lookup"><span data-stu-id="23763-125">For information on the previous routing system based on <xref:Microsoft.AspNetCore.Routing.IRouter>, select the ASP.NET Core 2.1 version using one of the following approaches:</span></span>

* <span data-ttu-id="23763-126">以前のバージョンのバージョン セレクター。</span><span class="sxs-lookup"><span data-stu-id="23763-126">The version selector for a previous version.</span></span>
* <span data-ttu-id="23763-127">[ASP.NET Core 2.1 でのルーティング](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)に関する記事を選択します。</span><span class="sxs-lookup"><span data-stu-id="23763-127">Select [ASP.NET Core 2.1 routing](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="23763-128">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="23763-128">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="23763-129">このドキュメントのダウンロード サンプルは、特定の `Startup` クラスによって有効になります。</span><span class="sxs-lookup"><span data-stu-id="23763-129">The download samples for this document are enabled by a specific `Startup` class.</span></span> <span data-ttu-id="23763-130">特定のサンプルを実行するには、目的の `Startup` クラスを呼び出すように *Program.cs* を変更します。</span><span class="sxs-lookup"><span data-stu-id="23763-130">To run a specific sample, modify *Program.cs* to call the desired `Startup` class.</span></span>

## <a name="routing-basics"></a><span data-ttu-id="23763-131">ルーティングの基本</span><span class="sxs-lookup"><span data-stu-id="23763-131">Routing basics</span></span>

<span data-ttu-id="23763-132">すべての ASP.NET Core テンプレートには、生成されたコードでのルーティングが含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-132">All ASP.NET Core templates include routing in the generated code.</span></span> <span data-ttu-id="23763-133">ルーティングは、`Startup.Configure` で[ミドルウェア](xref:fundamentals/middleware/index) パイプラインに登録されます。</span><span class="sxs-lookup"><span data-stu-id="23763-133">Routing is registered in the [middleware](xref:fundamentals/middleware/index) pipeline in `Startup.Configure`.</span></span>

<span data-ttu-id="23763-134">次のコードでは、ルーティングの基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-134">The following code shows a basic example of routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

<span data-ttu-id="23763-135">ルーティングでは、<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> と <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> によって登録されたミドルウェアのペアを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-135">Routing uses a pair of middleware, registered by <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:</span></span>

* <span data-ttu-id="23763-136">`UseRouting` では、ルートの照合がミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-136">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="23763-137">このミドルウェアによって、アプリで定義されているエンドポイントのセットが調べられ、要求に基づいて[最適な一致](#urlm)が選択されます。</span><span class="sxs-lookup"><span data-stu-id="23763-137">This middleware looks at the set of endpoints defined in the app, and selects the [best match](#urlm) based on the request.</span></span>
* <span data-ttu-id="23763-138">`UseEndpoints` では、エンドポイントの実行がミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-138">`UseEndpoints` adds endpoint execution to the middleware pipeline.</span></span> <span data-ttu-id="23763-139">選択されたエンドポイントに関連付けられているデリゲートが実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-139">It runs the delegate associated with the selected endpoint.</span></span>

<span data-ttu-id="23763-140">前の例には、[MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) メソッドを使用する "*コードへのルーティング*" エンドポイントが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-140">The preceding example includes a single *route to code* endpoint using the [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) method:</span></span>

* <span data-ttu-id="23763-141">HTTP `GET` 要求がルート URL `/` に送信された場合:</span><span class="sxs-lookup"><span data-stu-id="23763-141">When an HTTP `GET` request is sent to the root URL `/`:</span></span>
  * <span data-ttu-id="23763-142">示されている要求デリゲートが実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-142">The request delegate shown executes.</span></span>
  * <span data-ttu-id="23763-143">`Hello World!` が HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-143">`Hello World!` is written to the HTTP response.</span></span> <span data-ttu-id="23763-144">既定では、ルート URL `/` は `https://localhost:5001/` です。</span><span class="sxs-lookup"><span data-stu-id="23763-144">By default, the root URL `/` is `https://localhost:5001/`.</span></span>
* <span data-ttu-id="23763-145">要求メソッドが `GET` ではない場合、またはルート URL が `/` ではない場合は、一致するルートはなく、HTTP 404 が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-145">If the request method is not `GET` or the root URL is not `/`, no route matches and an HTTP 404 is returned.</span></span>

### <a name="endpoint"></a><span data-ttu-id="23763-146">エンドポイント</span><span class="sxs-lookup"><span data-stu-id="23763-146">Endpoint</span></span>

<a name="endpoint"></a>

<span data-ttu-id="23763-147">**エンドポイント**を定義するには、`MapGet` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-147">The `MapGet` method is used to define an **endpoint**.</span></span> <span data-ttu-id="23763-148">エンドポイントとは、次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="23763-148">An endpoint is something that can be:</span></span>

* <span data-ttu-id="23763-149">URL と HTTP メソッドを一致させることによって選択できます。</span><span class="sxs-lookup"><span data-stu-id="23763-149">Selected, by matching the URL and HTTP method.</span></span>
* <span data-ttu-id="23763-150">デリゲートを実行することによって実行できます。</span><span class="sxs-lookup"><span data-stu-id="23763-150">Executed, by running the delegate.</span></span>

<span data-ttu-id="23763-151">アプリによって一致させて実行できるエンドポイントは、`UseEndpoints` で構成します。</span><span class="sxs-lookup"><span data-stu-id="23763-151">Endpoints that can be matched and executed by the app are configured in `UseEndpoints`.</span></span> <span data-ttu-id="23763-152">たとえば、<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>、<xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>、および[類似のメソッド](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions)では、要求のデリゲートがルーティング システムに接続されます。</span><span class="sxs-lookup"><span data-stu-id="23763-152">For example, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, and [similar methods](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) connect request delegates to the routing system.</span></span>
<span data-ttu-id="23763-153">他のメソッドを使用して、ASP.NET Core フレームワークの機能をルーティング システムに接続できます。</span><span class="sxs-lookup"><span data-stu-id="23763-153">Additional methods can be used to connect ASP.NET Core framework features to the routing system:</span></span>
- <span data-ttu-id="23763-154">[Razor Pages 用の MapRazorPages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span><span class="sxs-lookup"><span data-stu-id="23763-154">[MapRazorPages for Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)</span></span>
- [<span data-ttu-id="23763-155">コントローラー用の MapControllers</span><span class="sxs-lookup"><span data-stu-id="23763-155">MapControllers for controllers</span></span>](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- <span data-ttu-id="23763-156">[SignalR 用の MapHub\<THub>](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span><span class="sxs-lookup"><span data-stu-id="23763-156">[MapHub\<THub> for SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*)</span></span> 
- [<span data-ttu-id="23763-157">gRPC 用の MapGrpcService\<TService></span><span class="sxs-lookup"><span data-stu-id="23763-157">MapGrpcService\<TService> for gRPC</span></span>](xref:grpc/aspnetcore)

<span data-ttu-id="23763-158">次の例では、より高度なルート テンプレートによるルーティングを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-158">The following example shows routing with a more sophisticated route template:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

<span data-ttu-id="23763-159">文字列 `/hello/{name:alpha}` は、**ルート テンプレート**です。</span><span class="sxs-lookup"><span data-stu-id="23763-159">The string `/hello/{name:alpha}` is a **route template**.</span></span> <span data-ttu-id="23763-160">これは、エンドポイントの一致方法を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-160">It is used to configure how the endpoint is matched.</span></span> <span data-ttu-id="23763-161">この場合、テンプレートは次のものと一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-161">In this case, the template matches:</span></span>

* <span data-ttu-id="23763-162">`/hello/Ryan` のような URL</span><span class="sxs-lookup"><span data-stu-id="23763-162">A URL like `/hello/Ryan`</span></span>
* <span data-ttu-id="23763-163">`/hello/` で始まり、その後に一連の英字が続く任意の URL パス。</span><span class="sxs-lookup"><span data-stu-id="23763-163">Any URL path that begins with `/hello/` followed by a sequence of alphabetic characters.</span></span>  <span data-ttu-id="23763-164">`:alpha` では、英字のみと一致するルート制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-164">`:alpha` applies a route constraint that matches only alphabetic characters.</span></span> <span data-ttu-id="23763-165">[ルート制約](#route-constraint-reference)については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-165">[Route constraints](#route-constraint-reference) are explained later in this document.</span></span>

<span data-ttu-id="23763-166">URL パスの 2 番目のセグメント `{name:alpha}` は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-166">The second segment of the URL path, `{name:alpha}`:</span></span>

* <span data-ttu-id="23763-167">`name` パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23763-167">Is bound to the `name` parameter.</span></span>
* <span data-ttu-id="23763-168">キャプチャされて [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) に格納されます。</span><span class="sxs-lookup"><span data-stu-id="23763-168">Is captured and stored in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).</span></span>

<span data-ttu-id="23763-169">このドキュメントで説明されているエンドポイント ルーティング システムは、ASP.NET Core 3.0 での新機能です。</span><span class="sxs-lookup"><span data-stu-id="23763-169">The endpoint routing system described in this document is new as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="23763-170">ただし、ASP.NET Core のすべてのバージョンで、同じルート テンプレート機能とルート制約のセットがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-170">However, all versions of ASP.NET Core support the same set of route template features and route constraints.</span></span>

<span data-ttu-id="23763-171">次の例では、[正常性チェック](xref:host-and-deploy/health-checks)と承認を使用するルーティングを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-171">The following example shows routing with [health checks](xref:host-and-deploy/health-checks) and authorization:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="23763-172">上の例では、次の方法が示されています。</span><span class="sxs-lookup"><span data-stu-id="23763-172">The preceding example demonstrates how:</span></span>

* <span data-ttu-id="23763-173">ルーティングで承認ミドルウェアを使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-173">The authorization middleware can be used with routing.</span></span>
* <span data-ttu-id="23763-174">エンドポイントを使用して、承認動作を構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-174">Endpoints can be used to configure authorization behavior.</span></span>

<span data-ttu-id="23763-175"><xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> の呼び出しにより、正常性チェック エンドポイントが追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-175">The <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> call adds a health check endpoint.</span></span> <span data-ttu-id="23763-176">この呼び出しに <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> をチェーンすると、エンドポイントに承認ポリシーがアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="23763-176">Chaining <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> on to this call attaches an authorization policy to the endpoint.</span></span>

<span data-ttu-id="23763-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> と <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> を呼び出すと、認証ミドルウェアと承認ミドルウェアが追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-177">Calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> adds the authentication and authorization middleware.</span></span> <span data-ttu-id="23763-178">これらのミドルウェアは、次のことができるように <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> と `UseEndpoints` の間に配置されます。</span><span class="sxs-lookup"><span data-stu-id="23763-178">These middleware are placed between <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and `UseEndpoints` so that they can:</span></span>

* <span data-ttu-id="23763-179">`UseRouting` によって選択されたエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="23763-179">See which endpoint was selected by `UseRouting`.</span></span>
* <span data-ttu-id="23763-180"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> によってエンドポイントにディスパッチされる前に、承認ポリシーを適用します。</span><span class="sxs-lookup"><span data-stu-id="23763-180">Apply an authorization policy before <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> dispatches to the endpoint.</span></span>

<a name="metadata"></a>

### <a name="endpoint-metadata"></a><span data-ttu-id="23763-181">エンドポイントのメタデータ</span><span class="sxs-lookup"><span data-stu-id="23763-181">Endpoint metadata</span></span>

<span data-ttu-id="23763-182">前の例には 2 つのエンドポイントがありますが、承認ポリシーがアタッチされているのは正常性チェック エンドポイントだけです。</span><span class="sxs-lookup"><span data-stu-id="23763-182">In the preceding example, there are two endpoints, but only the health check endpoint has an authorization policy attached.</span></span> <span data-ttu-id="23763-183">要求が正常性チェック エンドポイント `/healthz` と一致した場合、承認チェックが実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-183">If the request matches the health check endpoint, `/healthz`, an authorization check is performed.</span></span> <span data-ttu-id="23763-184">これは、エンドポイントに追加のデータをアタッチできることを示しています。</span><span class="sxs-lookup"><span data-stu-id="23763-184">This demonstrates that endpoints can have extra data attached to them.</span></span> <span data-ttu-id="23763-185">この追加データは、エンドポイントの**メタデータ**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="23763-185">This extra data is called endpoint **metadata**:</span></span>

* <span data-ttu-id="23763-186">メタデータは、ルーティング対応ミドルウェアによって処理できます。</span><span class="sxs-lookup"><span data-stu-id="23763-186">The metadata can be processed by routing-aware middleware.</span></span>
* <span data-ttu-id="23763-187">メタデータには、任意の .NET 型を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-187">The metadata can be of any .NET type.</span></span>

## <a name="routing-concepts"></a><span data-ttu-id="23763-188">ルーティングの概念</span><span class="sxs-lookup"><span data-stu-id="23763-188">Routing concepts</span></span>

<span data-ttu-id="23763-189">ルーティング システムは、ミドルウェア パイプラインを基にして、強力な**エンドポイント**概念を追加することにより、構築されています。</span><span class="sxs-lookup"><span data-stu-id="23763-189">The routing system builds on top of the middleware pipeline by adding the powerful **endpoint** concept.</span></span> <span data-ttu-id="23763-190">エンドポイントは、ルーティング、承認、および任意の数の ASP.NET Core システムに関して相互に独立している、アプリの機能の単位を表します。</span><span class="sxs-lookup"><span data-stu-id="23763-190">Endpoints represent units of the app's functionality that are distinct from each other in terms of routing, authorization, and any number of ASP.NET Core's systems.</span></span>

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a><span data-ttu-id="23763-191">ASP.NET Core エンドポイントの定義</span><span class="sxs-lookup"><span data-stu-id="23763-191">ASP.NET Core endpoint definition</span></span>

<span data-ttu-id="23763-192">ASP.NET Core エンドポイントとは次のようなものです。</span><span class="sxs-lookup"><span data-stu-id="23763-192">An ASP.NET Core endpoint is:</span></span>

* <span data-ttu-id="23763-193">実行可能: <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> を持っています。</span><span class="sxs-lookup"><span data-stu-id="23763-193">Executable: Has a <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>.</span></span>
* <span data-ttu-id="23763-194">拡張可能: [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) コレクションを持っています。</span><span class="sxs-lookup"><span data-stu-id="23763-194">Extensible: Has a [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*) collection.</span></span>
* <span data-ttu-id="23763-195">Selectable: 必要に応じて、[ルーティング情報](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*)を持ちます。</span><span class="sxs-lookup"><span data-stu-id="23763-195">Selectable: Optionally, has [routing information](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).</span></span>
* <span data-ttu-id="23763-196">列挙可能: エンドポイントのコレクションの一覧は、[DI](xref:fundamentals/dependency-injection) から <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> を取得することによって得られます。</span><span class="sxs-lookup"><span data-stu-id="23763-196">Enumerable: The collection of endpoints can be listed by retrieving the <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> from [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="23763-197">次のコードでは、エンドポイントを取得し、現在の要求と一致するものを検査する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-197">The following code shows how to retrieve and inspect the endpoint matching the current request:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

<span data-ttu-id="23763-198">エンドポイントが選択されている場合は、`HttpContext` から取得できます。</span><span class="sxs-lookup"><span data-stu-id="23763-198">The endpoint, if selected, can be retrieved from the `HttpContext`.</span></span> <span data-ttu-id="23763-199">そのプロパティを検査できます。</span><span class="sxs-lookup"><span data-stu-id="23763-199">Its properties can be inspected.</span></span> <span data-ttu-id="23763-200">エンドポイント オブジェクトは不変であり、作成後に変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="23763-200">Endpoint objects are immutable and cannot be modified after creation.</span></span> <span data-ttu-id="23763-201">最も一般的なエンドポイントの型は <xref:Microsoft.AspNetCore.Routing.RouteEndpoint> です。</span><span class="sxs-lookup"><span data-stu-id="23763-201">The most common type of endpoint is a <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>.</span></span> <span data-ttu-id="23763-202">`RouteEndpoint` には、ルーティング システムによる選択を可能にする情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23763-202">`RouteEndpoint` includes information that allows it to be to selected by the routing system.</span></span>

<span data-ttu-id="23763-203">上のコードでは、[app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) によってインライン [ミドルウェア](xref:fundamentals/middleware/index)が構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-203">In the preceding code, [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configures an in-line [middleware](xref:fundamentals/middleware/index).</span></span>

<a name="mt"></a>

<span data-ttu-id="23763-204">次のコードでは、パイプラインで `app.Use` が呼び出される場所によっては、エンドポイントが存在しない場合があることを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-204">The following code shows that, depending on where `app.Use` is called in the pipeline, there may not be an endpoint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

<span data-ttu-id="23763-205">前のサンプルでは、エンドポイントが選択されているかどうかを表示する `Console.WriteLine` ステートメントが追加されています。</span><span class="sxs-lookup"><span data-stu-id="23763-205">This preceding sample adds `Console.WriteLine` statements that display whether or not an endpoint has been selected.</span></span> <span data-ttu-id="23763-206">わかりやすくするため、このサンプルでは、指定された `/` エンドポイントに表示名が割り当てられています。</span><span class="sxs-lookup"><span data-stu-id="23763-206">For clarity, the sample assigns a display name to the provided `/` endpoint.</span></span>

<span data-ttu-id="23763-207">このコードを `/` の URL で実行すると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="23763-207">Running this code with a URL of `/` displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

<span data-ttu-id="23763-208">このコード他の URL で実行すると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="23763-208">Running this code with any other URL displays:</span></span>

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

<span data-ttu-id="23763-209">この出力は次のことを示しています。</span><span class="sxs-lookup"><span data-stu-id="23763-209">This output demonstrates that:</span></span>

* <span data-ttu-id="23763-210">`UseRouting` が呼び出される前は、エンドポイントは常に null になっています。</span><span class="sxs-lookup"><span data-stu-id="23763-210">The endpoint is always null before `UseRouting` is called.</span></span>
* <span data-ttu-id="23763-211">一致が見つかった場合、エンドポイントは `UseRouting` と <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> の間で null 以外の値になります。</span><span class="sxs-lookup"><span data-stu-id="23763-211">If a match is found, the endpoint is non-null between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="23763-212">一致が見つかると、`UseEndpoints` ミドルウェアは**ターミナル**です。</span><span class="sxs-lookup"><span data-stu-id="23763-212">The `UseEndpoints` middleware is **terminal** when a match is found.</span></span> <span data-ttu-id="23763-213">[ターミナル ミドルウェア](#tm)については、このドキュメントで後ほど定義します。</span><span class="sxs-lookup"><span data-stu-id="23763-213">[Terminal middleware](#tm) is defined later in this document.</span></span>
* <span data-ttu-id="23763-214">`UseEndpoints` の後のミドルウェアは、一致が検出されなかった場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-214">The middleware after `UseEndpoints` execute only when no match is found.</span></span>

<span data-ttu-id="23763-215">`UseRouting` ミドルウェアでは、[SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) メソッドを使用して、エンドポイントが現在のコンテキストにアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="23763-215">The `UseRouting` middleware uses the [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) method to attach the endpoint to the current context.</span></span> <span data-ttu-id="23763-216">`UseRouting` ミドルウェアをカスタム ロジックに置き換えることができ、その場合でもエンドポイントを使用する利点を得られます。</span><span class="sxs-lookup"><span data-stu-id="23763-216">It's possible to replace the `UseRouting` middleware with custom logic and still get the benefits of using endpoints.</span></span> <span data-ttu-id="23763-217">エンドポイントはミドルウェアのような低レベルのプリミティブであり、ルーティングの実装には結合されません。</span><span class="sxs-lookup"><span data-stu-id="23763-217">Endpoints are a low-level primitive like middleware, and aren't coupled to the routing implementation.</span></span> <span data-ttu-id="23763-218">ほとんどのアプリでは、`UseRouting` をカスタム ロジックに置き換える必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-218">Most apps don't need to replace `UseRouting` with custom logic.</span></span>

<span data-ttu-id="23763-219">`UseEndpoints` ミドルウェアは、`UseRouting` ミドルウェアと連携して使用するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="23763-219">The `UseEndpoints` middleware is designed to be used in tandem with the `UseRouting` middleware.</span></span> <span data-ttu-id="23763-220">エンドポイントを実行するためのコア ロジックは複雑ではありません。</span><span class="sxs-lookup"><span data-stu-id="23763-220">The core logic to execute an endpoint isn't complicated.</span></span> <span data-ttu-id="23763-221"><xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> を使用してエンドポイントを取得し、その <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> プロパティを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="23763-221">Use <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> to retrieve the endpoint, and then invoke its <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> property.</span></span>

<span data-ttu-id="23763-222">次のコードでは、ミドルウェアがルーティングに与える影響またはルーティングに対応する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-222">The following code demonstrates how middleware can influence or react to routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="23763-223">前の例では、2 つの重要な概念が示されています。</span><span class="sxs-lookup"><span data-stu-id="23763-223">The preceding example demonstrates two important concepts:</span></span>

* <span data-ttu-id="23763-224">ミドルウェアは、`UseRouting` の前に実行して、ルーティングの動作に使用されるデータを変更できます。</span><span class="sxs-lookup"><span data-stu-id="23763-224">Middleware can run before `UseRouting` to modify the data that routing operates upon.</span></span>
    * <span data-ttu-id="23763-225">通常、ルーティングの前に出現するミドルウェアでは、<xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>、<xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>、<xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> などの要求のプロパティが変更されます。</span><span class="sxs-lookup"><span data-stu-id="23763-225">Usually middleware that appears before routing modifies some property of the request, such as <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>, or <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.</span></span>
* <span data-ttu-id="23763-226">ミドルウェアは、`UseRouting` と <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> の間で実行して、エンドポイントが実行される前にルーティングの結果を処理できます。</span><span class="sxs-lookup"><span data-stu-id="23763-226">Middleware can run between `UseRouting` and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> to process the results of routing before the endpoint is executed.</span></span>
    * <span data-ttu-id="23763-227">`UseRouting` と `UseEndpoints` の間で実行されるミドルウェア:</span><span class="sxs-lookup"><span data-stu-id="23763-227">Middleware that runs between `UseRouting` and `UseEndpoints`:</span></span>
      * <span data-ttu-id="23763-228">通常、メタデータを検査してエンドポイントを認識します。</span><span class="sxs-lookup"><span data-stu-id="23763-228">Usually inspects metadata to understand the endpoints.</span></span>
      * <span data-ttu-id="23763-229">多くの場合、`UseAuthorization` や `UseCors` によって実行されるセキュリティに関する決定を行います。</span><span class="sxs-lookup"><span data-stu-id="23763-229">Often makes security decisions, as done by `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="23763-230">ミドルウェアとメタデータを組み合わせることで、エンドポイントごとにポリシーを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-230">The combination of middleware and metadata allows configuring policies per-endpoint.</span></span>

<span data-ttu-id="23763-231">上のコードでは、エンドポイントごとのポリシーをサポートするカスタム ミドルウェアの例が示されています。</span><span class="sxs-lookup"><span data-stu-id="23763-231">The preceding code shows an example of a custom middleware that supports per-endpoint policies.</span></span> <span data-ttu-id="23763-232">ミドルウェアによって、機密データへのアクセスの "*監査ログ*" がコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-232">The middleware writes an *audit log* of access to sensitive data to the console.</span></span> <span data-ttu-id="23763-233">`AuditPolicyAttribute` メタデータを使用して、エンドポイントを "*監査する*" ようにミドルウェアを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-233">The middleware can be configured to *audit* an endpoint with the `AuditPolicyAttribute` metadata.</span></span> <span data-ttu-id="23763-234">このサンプルでは、機密としてマークされているエンドポイントのみが監査される "*オプトイン*" パターンを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-234">This sample demonstrates an *opt-in* pattern where only endpoints that are marked as sensitive are audited.</span></span> <span data-ttu-id="23763-235">このロジックの逆を定義して、たとえば安全としてマークされていないすべてのものを監査することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-235">It's possible to define this logic in reverse, auditing everything that isn't marked as safe, for example.</span></span> <span data-ttu-id="23763-236">エンドポイント メタデータ システムは柔軟です。</span><span class="sxs-lookup"><span data-stu-id="23763-236">The endpoint metadata system is flexible.</span></span> <span data-ttu-id="23763-237">ユース ケースに適したどのような方法でも、このロジックを設計できます。</span><span class="sxs-lookup"><span data-stu-id="23763-237">This logic could be designed in whatever way suits the use case.</span></span>

<span data-ttu-id="23763-238">前のサンプル コードは、エンドポイントの基本的な概念を示すことが意図されています。</span><span class="sxs-lookup"><span data-stu-id="23763-238">The preceding sample code is intended to demonstrate the basic concepts of endpoints.</span></span> <span data-ttu-id="23763-239">**サンプルは運用環境での使用は意図されていません**。</span><span class="sxs-lookup"><span data-stu-id="23763-239">**The sample is not intended for production use**.</span></span> <span data-ttu-id="23763-240">より完全なバージョンの "*監査ログ*" ミドルウェアでは、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-240">A more complete version of an *audit log* middleware would:</span></span>

* <span data-ttu-id="23763-241">ファイルまたはデータベースにログを記録します。</span><span class="sxs-lookup"><span data-stu-id="23763-241">Log to a file or database.</span></span>
* <span data-ttu-id="23763-242">ユーザー、IP アドレス、機密性の高いエンドポイントの名前などの詳細情報が追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-242">Include details such as the user, IP address, name of the sensitive endpoint, and more.</span></span>

<span data-ttu-id="23763-243">コントローラーや SignalR などのクラスベースのフレームワークで簡単に使用できるように、監査ポリシー メタデータ `AuditPolicyAttribute` は `Attribute` と定義されています。</span><span class="sxs-lookup"><span data-stu-id="23763-243">The audit policy metadata `AuditPolicyAttribute` is defined as an `Attribute` for easier use with class-based frameworks such as controllers and SignalR.</span></span> <span data-ttu-id="23763-244">"*コードへのルーティング*" を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-244">When using *route to code*:</span></span>

* <span data-ttu-id="23763-245">メタデータがビルダー API にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="23763-245">Metadata is attached with a builder API.</span></span>
* <span data-ttu-id="23763-246">エンドポイントを作成するとき、クラス ベースのフレームワークに、対応するメソッドとクラスのすべての属性が組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-246">Class-based frameworks include all attributes on the corresponding method and class when creating endpoints.</span></span>

<span data-ttu-id="23763-247">メタデータの型に対するベスト プラクティスは、インターフェイスまたは属性として定義することです。</span><span class="sxs-lookup"><span data-stu-id="23763-247">The best practices for metadata types are to define them either as interfaces or attributes.</span></span> <span data-ttu-id="23763-248">インターフェイスと属性では、コードを再利用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-248">Interfaces and attributes allow code reuse.</span></span> <span data-ttu-id="23763-249">メタデータ システムは柔軟であり、どのような制限もありません。</span><span class="sxs-lookup"><span data-stu-id="23763-249">The metadata system is flexible and doesn't impose any limitations.</span></span>

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a><span data-ttu-id="23763-250">ターミナル ミドルウェアとルーティングの比較</span><span class="sxs-lookup"><span data-stu-id="23763-250">Comparing a terminal middleware and routing</span></span>

<span data-ttu-id="23763-251">次のコード サンプルでは、ミドルウェアの使用とルーティングの使用が比較されています。</span><span class="sxs-lookup"><span data-stu-id="23763-251">The following code sample contrasts using middleware with using routing:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

<span data-ttu-id="23763-252">`Approach 1:` で示されているミドルウェアのスタイルは、**ターミナル ミドルウェア**です。</span><span class="sxs-lookup"><span data-stu-id="23763-252">The style of middleware shown with `Approach 1:` is **terminal middleware**.</span></span> <span data-ttu-id="23763-253">ターミナル ミドルウェアと呼ばれるのは、照合操作を実行するためです。</span><span class="sxs-lookup"><span data-stu-id="23763-253">It's called terminal middleware because it does a matching operation:</span></span>

* <span data-ttu-id="23763-254">前のサンプルの照合操作は、ミドルウェアの場合は `Path == "/"`、ルーティングの場合は `Path == "/Movie"` です。</span><span class="sxs-lookup"><span data-stu-id="23763-254">The matching operation in the preceding sample is `Path == "/"` for the middleware and `Path == "/Movie"` for routing.</span></span>
* <span data-ttu-id="23763-255">照合が成功すると、`next` ミドルウェアを呼び出すのではなく、一部の機能を実行して戻ります。</span><span class="sxs-lookup"><span data-stu-id="23763-255">When a match is successful, it executes some functionality and returns, rather than invoking the `next` middleware.</span></span>

<span data-ttu-id="23763-256">ターミナル ミドルウェアと呼ばれるのは、検索を終了し、いくつかの機能を実行してから制御を返すためです。</span><span class="sxs-lookup"><span data-stu-id="23763-256">It's called terminal middleware because it terminates the search, executes some functionality, and then returns.</span></span>

<span data-ttu-id="23763-257">ターミナル ミドルウェアとルーティングの比較:</span><span class="sxs-lookup"><span data-stu-id="23763-257">Comparing a terminal middleware and routing:</span></span>
* <span data-ttu-id="23763-258">どちらの方法でも、処理パイプラインを終了できます。</span><span class="sxs-lookup"><span data-stu-id="23763-258">Both approaches allow terminating the processing pipeline:</span></span>
    * <span data-ttu-id="23763-259">ミドルウェアでは、`next` を呼び出すのではなく、戻ることによってパイプラインが終了されます。</span><span class="sxs-lookup"><span data-stu-id="23763-259">Middleware terminates the pipeline by returning rather than invoking `next`.</span></span>
    * <span data-ttu-id="23763-260">エンドポイントは常にターミナルです。</span><span class="sxs-lookup"><span data-stu-id="23763-260">Endpoints are always terminal.</span></span>
* <span data-ttu-id="23763-261">ターミナル ミドルウェアを使用すると、パイプライン内の任意の場所にミドルウェアを配置できます。</span><span class="sxs-lookup"><span data-stu-id="23763-261">Terminal middleware allows positioning the middleware at an arbitrary place in the pipeline:</span></span>
    * <span data-ttu-id="23763-262">エンドポイントは、<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> の位置で実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-262">Endpoints execute at the position of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>
* <span data-ttu-id="23763-263">ターミナル ミドルウェアでは、任意のコードを使用してミドルウェアが一致するかどうかを判定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-263">Terminal middleware allows arbitrary code to determine when the middleware matches:</span></span>
    * <span data-ttu-id="23763-264">カスタム ルート一致コードは、冗長で、正しく記述するのが困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="23763-264">Custom route matching code can be verbose and difficult to write correctly.</span></span>
    * <span data-ttu-id="23763-265">ルーティングでは、一般的なアプリに対して簡単なソリューションが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-265">Routing provides straightforward solutions for typical apps.</span></span> <span data-ttu-id="23763-266">ほとんどのアプリでは、カスタム ルート一致コードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="23763-266">Most apps don't require custom route matching code.</span></span>
* <span data-ttu-id="23763-267">`UseAuthorization` や `UseCors` などのミドルウェアを使用したエンドポイント インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="23763-267">Endpoints interface with middleware such as `UseAuthorization` and `UseCors`.</span></span>
    * <span data-ttu-id="23763-268">`UseAuthorization` または `UseCors` でターミナル ミドルウェアを使用するには、承認システムとの手動インターフェイスが必要です。</span><span class="sxs-lookup"><span data-stu-id="23763-268">Using a terminal middleware with `UseAuthorization` or `UseCors` requires manual interfacing with the authorization system.</span></span>

<span data-ttu-id="23763-269">[エンドポイント](#endpoint) では、次の両方が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-269">An [endpoint](#endpoint) defines both:</span></span>

* <span data-ttu-id="23763-270">要求を処理するためのデリゲート。</span><span class="sxs-lookup"><span data-stu-id="23763-270">A delegate to process requests.</span></span>
* <span data-ttu-id="23763-271">任意のメタデータのコレクション。</span><span class="sxs-lookup"><span data-stu-id="23763-271">A collection of arbitrary metadata.</span></span> <span data-ttu-id="23763-272">メタデータは、各エンドポイントにアタッチされている構成とポリシーに基づいて横断的な関心事を実装するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-272">The metadata is used to implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="23763-273">ターミナル ミドルウェアは効果的なツールになる可能性がありますが、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="23763-273">Terminal middleware can be an effective tool, but can require:</span></span>

* <span data-ttu-id="23763-274">大量のコーディングとテスト。</span><span class="sxs-lookup"><span data-stu-id="23763-274">A significant amount of coding and testing.</span></span>
* <span data-ttu-id="23763-275">必要なレベルの柔軟性を実現するための、他のシステムとの手作業による統合。</span><span class="sxs-lookup"><span data-stu-id="23763-275">Manual integration with other systems to achieve the desired level of flexibility.</span></span>

<span data-ttu-id="23763-276">ターミナル ミドルウェアを作成する前に、ルーティングとの統合を検討してください。</span><span class="sxs-lookup"><span data-stu-id="23763-276">Consider integrating with routing before writing a terminal middleware.</span></span>

<span data-ttu-id="23763-277">[Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) または <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> と統合されている既存のターミナル ミドルウェアは、通常、ルーティング対応のエンドポイントにすることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-277">Existing terminal middleware that integrates with [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) or <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> can usually be turned into a routing aware endpoint.</span></span> <span data-ttu-id="23763-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) では、ルーターウェアのパターンが示されています。</span><span class="sxs-lookup"><span data-stu-id="23763-278">[MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) demonstrates the pattern for router-ware:</span></span>
* <span data-ttu-id="23763-279"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> で拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-279">Write an extension method on <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span>
* <span data-ttu-id="23763-280"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*> を使用して、入れ子になったミドルウェア パイプラインを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-280">Create a nested middleware pipeline using <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.</span></span>
* <span data-ttu-id="23763-281">ミドルウェアを新しいパイプラインにアタッチします。</span><span class="sxs-lookup"><span data-stu-id="23763-281">Attach the middleware to the new pipeline.</span></span> <span data-ttu-id="23763-282">例では、 <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-282">In this case, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.</span></span>
* <span data-ttu-id="23763-283"><xref:Microsoft.AspNetCore.Http.RequestDelegate> にミドルウェア パイプラインを <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> します。</span><span class="sxs-lookup"><span data-stu-id="23763-283"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*> the middleware pipeline into a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="23763-284">`Map` を呼び出し、新しいミドルウェア パイプラインを提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-284">Call `Map` and provide the new middleware pipeline.</span></span>
* <span data-ttu-id="23763-285">拡張メソッドから `Map` によって提供されるビルダー オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="23763-285">Return the builder object provided by `Map` from the extension method.</span></span>

<span data-ttu-id="23763-286">次のコードでは、[MapHealthChecks](xref:host-and-deploy/health-checks) の使用方法を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-286">The following code shows use of [MapHealthChecks](xref:host-and-deploy/health-checks):</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

<span data-ttu-id="23763-287">前のサンプルでは、ビルダー オブジェクトを返すことが重要である理由が示されています。</span><span class="sxs-lookup"><span data-stu-id="23763-287">The preceding sample shows why returning the builder object is important.</span></span> <span data-ttu-id="23763-288">ビルダー オブジェクトを返すことで、アプリ開発者はエンドポイントの承認などのポリシーを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-288">Returning the builder object allows the app developer to configure policies such as authorization for the endpoint.</span></span> <span data-ttu-id="23763-289">この例では、正常性チェック ミドルウェアと承認システムは直接統合されていません。</span><span class="sxs-lookup"><span data-stu-id="23763-289">In this example, the health checks middleware has no direct integration with the authorization system.</span></span>

<span data-ttu-id="23763-290">そのメタデータ システムは、ターミナル ミドルウェアを使用する機能拡張作成者によって、発生する問題に対応して作成されました。</span><span class="sxs-lookup"><span data-stu-id="23763-290">The metadata system was created in response to the problems encountered by extensibility authors using terminal middleware.</span></span> <span data-ttu-id="23763-291">各ミドルウェアで承認システムとの独自の統合を実装することには問題があります。</span><span class="sxs-lookup"><span data-stu-id="23763-291">It's problematic for each middleware to implement its own integration with the authorization system.</span></span>

<a name="urlm"></a>

### <a name="url-matching"></a><span data-ttu-id="23763-292">URL 一致</span><span class="sxs-lookup"><span data-stu-id="23763-292">URL matching</span></span>

* <span data-ttu-id="23763-293">ルーティングによって受信要求が[エンドポイント](#endpoint)と照合されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="23763-293">Is the process by which routing matches an incoming request to an [endpoint](#endpoint).</span></span>
* <span data-ttu-id="23763-294">URL パスとヘッダーのデータに基づいています。</span><span class="sxs-lookup"><span data-stu-id="23763-294">Is based on data in the URL path and headers.</span></span>
* <span data-ttu-id="23763-295">要求内の任意のデータを考慮するように拡張できます。</span><span class="sxs-lookup"><span data-stu-id="23763-295">Can be extended to consider any data in the request.</span></span>

<span data-ttu-id="23763-296">実行されたルーティング ミドルウェアでは、`Endpoint` が設定され、現在の要求からの <xref:Microsoft.AspNetCore.Http.HttpContext> で[要求機能](xref:fundamentals/request-features)に値がルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-296">When a routing middleware executes, it sets an `Endpoint` and route values to a [request feature](xref:fundamentals/request-features) on the <xref:Microsoft.AspNetCore.Http.HttpContext> from the current request:</span></span>

* <span data-ttu-id="23763-297">[HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) を呼び出すと、エンドポイントが取得されます。</span><span class="sxs-lookup"><span data-stu-id="23763-297">Calling [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) gets the endpoint.</span></span>
* <span data-ttu-id="23763-298">`HttpRequest.RouteValues` では、ルート値のコレクションが取得されます。</span><span class="sxs-lookup"><span data-stu-id="23763-298">`HttpRequest.RouteValues` gets the collection of route values.</span></span>

<span data-ttu-id="23763-299">ルーティング ミドルウェアの後で実行された[ミドルウェア](xref:fundamentals/middleware/index)では、エンドポイントを調べて、アクションを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-299">[Middleware](xref:fundamentals/middleware/index) running after the routing middleware can inspect the endpoint and take action.</span></span> <span data-ttu-id="23763-300">たとえば、承認ミドルウェアでは、エンドポイントのメタデータ コレクションに対し、承認ポリシーを問い合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-300">For example, an authorization middleware can interrogate the endpoint's metadata collection for an authorization policy.</span></span> <span data-ttu-id="23763-301">要求処理パイプライン内のすべてのミドルウェアが実行された後、選択したエンドポイントのデリゲートが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-301">After all of the middleware in the request processing pipeline is executed, the selected endpoint's delegate is invoked.</span></span>

<span data-ttu-id="23763-302">エンドポイント ルーティングのルーティング システムでは、配布に関するすべての決定が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-302">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="23763-303">ミドルウェアでは選択されたエンドポイントに基づいてポリシーが適用されるため、次のことが重要です。</span><span class="sxs-lookup"><span data-stu-id="23763-303">Because the middleware applies policies based on the selected endpoint, it's important that:</span></span>

* <span data-ttu-id="23763-304">ディスパッチまたはセキュリティ ポリシーの適用に影響を与える可能性のある決定は、ルーティング システム内で行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-304">Any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

> [!WARNING]
> <span data-ttu-id="23763-305">後方互換性のために、コントローラーまたは Razor Pages エンドポイント デリゲートが実行されると、それまでに実行された要求処理に基づいて、[RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) のプロパティが適切な値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-305">For backwards-compatibility, when a Controller or Razor Pages endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>
>
> <span data-ttu-id="23763-306">`RouteContext` の種類は、今後のリリースでは古いものとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="23763-306">The `RouteContext` type will be marked obsolete in a future release:</span></span>
>
> * <span data-ttu-id="23763-307">`RouteData.Values` を `HttpRequest.RouteValues` に移行します。</span><span class="sxs-lookup"><span data-stu-id="23763-307">Migrate `RouteData.Values` to `HttpRequest.RouteValues`.</span></span>
> * <span data-ttu-id="23763-308">`RouteData.DataTokens` を移行して、エンドポイント メタデータから [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) を取得します。</span><span class="sxs-lookup"><span data-stu-id="23763-308">Migrate `RouteData.DataTokens` to retrieve [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) from the endpoint metadata.</span></span>

<span data-ttu-id="23763-309">URL の照合は、構成可能な一連のフェーズで動作します。</span><span class="sxs-lookup"><span data-stu-id="23763-309">URL matching operates in a configurable set of phases.</span></span> <span data-ttu-id="23763-310">各フェーズでの出力は一致のセットとなります。</span><span class="sxs-lookup"><span data-stu-id="23763-310">In each phase, the output is a set of matches.</span></span> <span data-ttu-id="23763-311">一致のセットは、次のフェーズでさらに絞り込むことができます。</span><span class="sxs-lookup"><span data-stu-id="23763-311">The set of matches can be narrowed down further by the next phase.</span></span> <span data-ttu-id="23763-312">ルーティングの実装では、一致するエンドポイントの処理順序は保証されません。</span><span class="sxs-lookup"><span data-stu-id="23763-312">The routing implementation does not guarantee a processing order for matching endpoints.</span></span> <span data-ttu-id="23763-313">一致の可能性のあるものは一度に**すべて**処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-313">**All** possible matches are processed at once.</span></span> <span data-ttu-id="23763-314">URL 照合フェーズは、次の順序で発生します。</span><span class="sxs-lookup"><span data-stu-id="23763-314">The URL matching phases occur in the following order.</span></span> <span data-ttu-id="23763-315">ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="23763-315">ASP.NET Core:</span></span>

1. <span data-ttu-id="23763-316">エンドポイントのセットおよびそれらのルート テンプレートに対して URL パスを処理し、**すべて**の一致を収集します。</span><span class="sxs-lookup"><span data-stu-id="23763-316">Processes the URL path against the set of endpoints and their route templates, collecting **all** of the matches.</span></span>
1. <span data-ttu-id="23763-317">前のリストを取得し、ルート制約が適用されると失敗する一致を削除します。</span><span class="sxs-lookup"><span data-stu-id="23763-317">Takes the preceding list and removes matches that fail with route constraints applied.</span></span>
1. <span data-ttu-id="23763-318">前のリストを取得し、[MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) インスタンスのセットを失敗させる一致を削除します。</span><span class="sxs-lookup"><span data-stu-id="23763-318">Takes the preceding list and removes matches that fail the set of [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy) instances.</span></span>
1. <span data-ttu-id="23763-319">[EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) を使用して、前のリストから最終的な決定を行います。</span><span class="sxs-lookup"><span data-stu-id="23763-319">Uses the [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) to make a final decision from the preceding list.</span></span>

<span data-ttu-id="23763-320">エンドポイントのリストは、次の内容に従って優先度付けが行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-320">The list of endpoints is prioritized according to:</span></span>

* <span data-ttu-id="23763-321">[RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span><span class="sxs-lookup"><span data-stu-id="23763-321">The [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)</span></span>
* <span data-ttu-id="23763-322">[ルート テンプレートの優先順位](#rtp)</span><span class="sxs-lookup"><span data-stu-id="23763-322">The [route template precedence](#rtp)</span></span>

<span data-ttu-id="23763-323"><xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> に到達するまで、各フェーズで一致するすべてのエンドポイントが処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-323">All matching endpoints are processed in each phase until the <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> is reached.</span></span> <span data-ttu-id="23763-324">`EndpointSelector` は最後のフェーズです。</span><span class="sxs-lookup"><span data-stu-id="23763-324">The `EndpointSelector` is the final phase.</span></span> <span data-ttu-id="23763-325">一致の中から最も優先度の高いエンドポイントが最適な一致として選択されます。</span><span class="sxs-lookup"><span data-stu-id="23763-325">It chooses the highest priority endpoint from the matches as the best match.</span></span> <span data-ttu-id="23763-326">最適な一致と優先度が同じである一致が他にもある場合は、あいまい一致の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="23763-326">If there are other matches with the same priority as the best match, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="23763-327">ルートの優先順位は**より具体的な**ルート テンプレートに、より高い優先度が与えられることに基づいて算出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-327">The route precedence is computed based on a **more specific** route template being given a higher priority.</span></span> <span data-ttu-id="23763-328">たとえば、テンプレート `/hello` と `/{message}` を検討してみます。</span><span class="sxs-lookup"><span data-stu-id="23763-328">For example, consider the templates `/hello` and `/{message}`:</span></span>

* <span data-ttu-id="23763-329">どちらも URL パス `/hello` と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-329">Both match the URL path `/hello`.</span></span>
* <span data-ttu-id="23763-330">`/hello` の方がより具体的なので、優先度が高くなります。</span><span class="sxs-lookup"><span data-stu-id="23763-330">`/hello`  is more specific and therefore higher priority.</span></span>

<span data-ttu-id="23763-331">一般に、ルートの優先順位は、実際に使用される URL スキームの種類として最適なものを選択するのに適しています。</span><span class="sxs-lookup"><span data-stu-id="23763-331">In general, route precedence does a good job of choosing the best match for the kinds of URL schemes used in practice.</span></span> <span data-ttu-id="23763-332"><xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> は、あいまいさを避けるために必要な場合にのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-332">Use <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> only when necessary to avoid an ambiguity.</span></span>

<span data-ttu-id="23763-333">拡張性の種類がルーティングによって指定されるため、あいまいなルートを事前にルーティング システムによって計算することはできません。</span><span class="sxs-lookup"><span data-stu-id="23763-333">Due to the kinds of extensibility provided by routing, it isn't possible for the routing system to compute ahead of time the ambiguous routes.</span></span> <span data-ttu-id="23763-334">ルート テンプレート `/{message:alpha}` や `/{message:int}` などの例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="23763-334">Consider an example such as the route templates `/{message:alpha}` and `/{message:int}`:</span></span>

* <span data-ttu-id="23763-335">`alpha` 制約を使用すると、アルファベット文字のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-335">The `alpha` constraint matches only alphabetic characters.</span></span>
* <span data-ttu-id="23763-336">`int` 制約を使用すると、数値のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-336">The `int` constraint matches only numbers.</span></span>
* <span data-ttu-id="23763-337">これらのテンプレートのルート優先順位は同じですが、この両方と一致する単一の URL はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-337">These templates have the same route precedence, but there's no single URL they both match.</span></span>
* <span data-ttu-id="23763-338">起動時にルーティング システムからあいまいエラーが報告された場合、それによってこの有効なユース ケースはブロックされます。</span><span class="sxs-lookup"><span data-stu-id="23763-338">If the routing system reported an ambiguity error at startup, it would block this valid use case.</span></span>

> [!WARNING]
>
> <span data-ttu-id="23763-339"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> 内での処理の順序は、ルーティングの動作には影響しませんが、例外が 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="23763-339">The order of operations inside <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> doesn't influence the behavior of routing, with one exception.</span></span> <span data-ttu-id="23763-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> および <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> では、それぞれが呼び出された順序に基づいて、それぞれのエンドポイントに順序値が自動的に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="23763-340"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="23763-341">これにより、ルーティング システムでより古いルーティング実装と同じ保証を提供しなくても、コントローラーの長時間の動作がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-341">This simulates long-time behavior of controllers without the routing system providing the same guarantees as older routing implementations.</span></span>
>
> <span data-ttu-id="23763-342">ルーティングの従来の実装では、ルートの処理順序に依存するルーティング拡張性を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-342">In the legacy implementation of routing, it's possible to implement routing extensibility that has a dependency on the order in which routes are processed.</span></span> <span data-ttu-id="23763-343">ASP.NET Core 3.0 以降でのエンドポイントのルーティング:</span><span class="sxs-lookup"><span data-stu-id="23763-343">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>
> 
> * <span data-ttu-id="23763-344">ルートの概念がありません。</span><span class="sxs-lookup"><span data-stu-id="23763-344">Doesn't have a concept of routes.</span></span>
> * <span data-ttu-id="23763-345">順序付けが保証されません。</span><span class="sxs-lookup"><span data-stu-id="23763-345">Doesn't provide ordering guarantees.</span></span> <span data-ttu-id="23763-346">すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-346">All endpoints are processed at once.</span></span>
>
> <span data-ttu-id="23763-347">これにより、従来のルーティング システムの使用に行き詰まっている場合、[GitHub のイシューを開いて支援を求めてください](https://github.com/dotnet/aspnetcore/issues)。</span><span class="sxs-lookup"><span data-stu-id="23763-347">If this means you're stuck using the legacy routing system, [open a GitHub issue for assistance](https://github.com/dotnet/aspnetcore/issues).</span></span>

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a><span data-ttu-id="23763-348">ルート テンプレートの優先順位とエンドポイントの選択順序</span><span class="sxs-lookup"><span data-stu-id="23763-348">Route template precedence and endpoint selection order</span></span>

<span data-ttu-id="23763-349">[ルート テンプレートの優先順位](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16)とは、どれほど具体的であるかに基づいて、各ルート テンプレートに値を割り当てるシステムです。</span><span class="sxs-lookup"><span data-stu-id="23763-349">[Route template precedence](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) is a system that assigns each route template a value based on how specific it is.</span></span> <span data-ttu-id="23763-350">ルート テンプレートの優先順位:</span><span class="sxs-lookup"><span data-stu-id="23763-350">Route template precedence:</span></span>

* <span data-ttu-id="23763-351">一般的なケースでは、エンドポイントの順序を調整する必要はなくなります。</span><span class="sxs-lookup"><span data-stu-id="23763-351">Avoids the need to adjust the order of endpoints in common cases.</span></span>
* <span data-ttu-id="23763-352">一般的に期待されるルーティング動作との一致が試みられます。</span><span class="sxs-lookup"><span data-stu-id="23763-352">Attempts to match the common-sense expectations of routing behavior.</span></span>

<span data-ttu-id="23763-353">たとえば、テンプレート `/Products/List` と `/Products/{id}` について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-353">For example, consider templates `/Products/List` and `/Products/{id}`.</span></span> <span data-ttu-id="23763-354">URL パス `/Products/List` に対しては、`/Products/List` の方が `/Products/{id}` よりも適していると想定するのが妥当です。</span><span class="sxs-lookup"><span data-stu-id="23763-354">It would be reasonable to assume that `/Products/List` is a better match than `/Products/{id}` for the URL path `/Products/List`.</span></span> <span data-ttu-id="23763-355">このように言えるのは、リテラル セグメント `/List` がパラメーター セグメント `/{id}` よりも優先順位が高いと見なされるためです。</span><span class="sxs-lookup"><span data-stu-id="23763-355">The works because the literal segment `/List` is considered to have better precedence than the parameter segment `/{id}`.</span></span>

<span data-ttu-id="23763-356">優先順位のしくみの詳細は、ルート テンプレートの定義方法と関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="23763-356">The details of how precedence works are coupled to how route templates are defined:</span></span>

* <span data-ttu-id="23763-357">より多くのセグメントを持つテンプレートは、より具体的なものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-357">Templates with more segments are considered more specific.</span></span>
* <span data-ttu-id="23763-358">リテラル テキストを含むセグメントは、パラメーター セグメントよりも具体的であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-358">A segment with literal text is considered more specific than a parameter segment.</span></span>
* <span data-ttu-id="23763-359">制約が含まれるパラメーター セグメントは、それが含まれないものよりも具体的であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-359">A parameter segment with a constraint is considered more specific than one without.</span></span>
* <span data-ttu-id="23763-360">複雑なセグメントは、制約を含むパラメーター セグメントと同じくらい具体的であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-360">A complex segment is considered as specific as a parameter segment with a constraint.</span></span>
* <span data-ttu-id="23763-361">キャッチオール パラメーターは、まったく具体的ではありません。</span><span class="sxs-lookup"><span data-stu-id="23763-361">Catch-all parameters are the least specific.</span></span> <span data-ttu-id="23763-362">キャッチオール ルートに関する重要な情報については、「[ルート テンプレート参照](#rtr)」の**キャッチオール**を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-362">See **catch-all** in the [Route template reference](#rtr) for important information on catch-all routes.</span></span>

<span data-ttu-id="23763-363">実際の値のリファレンスについては、[GitHub 上のソース コード](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-363">See the [source code on GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) for a reference of exact values.</span></span>

<a name="lg"></a>

### <a name="url-generation-concepts"></a><span data-ttu-id="23763-364">URL 生成の概念</span><span class="sxs-lookup"><span data-stu-id="23763-364">URL generation concepts</span></span>

<span data-ttu-id="23763-365">URL の生成:</span><span class="sxs-lookup"><span data-stu-id="23763-365">URL generation:</span></span>

* <span data-ttu-id="23763-366">ルーティングにおいて、一連のルート値に基づいて URL パスを作成するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="23763-366">Is the process by which routing can create a URL path based on a set of route values.</span></span>
* <span data-ttu-id="23763-367">エンドポイントとそれにアクセスする URL を論理的に分離できます。</span><span class="sxs-lookup"><span data-stu-id="23763-367">Allows for a logical separation between endpoints and the URLs that access them.</span></span>

<span data-ttu-id="23763-368">エンドポイント ルーティングには、<xref:Microsoft.AspNetCore.Routing.LinkGenerator> API が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-368">Endpoint routing includes the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API.</span></span> <span data-ttu-id="23763-369">`LinkGenerator` は [DI](xref:fundamentals/dependency-injection) から使用できるシングルトン サービスです。</span><span class="sxs-lookup"><span data-stu-id="23763-369">`LinkGenerator` is a singleton service available from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="23763-370">`LinkGenerator` API は、実行中の要求のコンテキスト外で使用することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-370">The `LinkGenerator` API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="23763-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) と、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、HTML ヘルパー、[アクション結果](xref:mvc/controllers/actions)など、<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> に依存するシナリオでは `LinkGenerator` API を内部的に使用して、リンク生成機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-371">[Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the `LinkGenerator` API internally to provide link generating capabilities.</span></span>

<span data-ttu-id="23763-372">リンク ジェネレーターは、**アドレス** と**アドレス スキーム** の概念に基づいています。</span><span class="sxs-lookup"><span data-stu-id="23763-372">The link generator is backed by the concept of an **address** and **address schemes**.</span></span> <span data-ttu-id="23763-373">アドレス スキームは、リンク生成で考慮すべきエンドポイントを決定する方法です。</span><span class="sxs-lookup"><span data-stu-id="23763-373">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="23763-374">たとえば、コントローラーおよび Razor Pages からの、多くのユーザーに馴染みのあるルート名やルート値シナリオは、アドレス スキームとして実装されます。</span><span class="sxs-lookup"><span data-stu-id="23763-374">For example, the route name and route values scenarios many users are familiar with from controllers and Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="23763-375">リンク ジェネレーターでは、次の拡張メソッドを介して、コントローラーおよび Razor Pages にリンクできます。</span><span class="sxs-lookup"><span data-stu-id="23763-375">The link generator can link to controllers and Razor Pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="23763-376">これらのメソッドのオーバーロードでは、`HttpContext` を含む引数が受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="23763-376">Overloads of these methods accept arguments that include the `HttpContext`.</span></span> <span data-ttu-id="23763-377">これらのメソッドは [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) および [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) と機能的には同等ですが、柔軟性とオプションがさらに提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-377">These methods are functionally equivalent to [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) and [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), but offer additional flexibility and options.</span></span>

<span data-ttu-id="23763-378">`GetPath*` メソッドは、絶対パスを含む URI を生成するという点で `Url.Action` および `Url.Page` に最も似ています。</span><span class="sxs-lookup"><span data-stu-id="23763-378">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page`, in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="23763-379">`GetUri*` メソッドでは常に、スキームとホストを含む絶対 URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-379">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="23763-380">`HttpContext` を受け入れるメソッドでは、実行中の要求のコンテキストで URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-380">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="23763-381">実行中の要求からの[アンビエント](#ambient) ルート値、URL ベース パス、スキーム、およびホストは、オーバーライドされない限り使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-381">The [ambient](#ambient) route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="23763-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> はアドレスと共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-382"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="23763-383">URI の生成は、次の 2 つの手順で行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-383">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="23763-384">アドレスは、そのアドレスと一致するエンドポイントのリストにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23763-384">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="23763-385">各エンドポイントの <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> は、指定された値と一致するルート パターンが見つかるまで評価されます。</span><span class="sxs-lookup"><span data-stu-id="23763-385">Each endpoint's <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="23763-386">結果の出力は、リンク ジェネレーターに指定された他の URI 部分と結合され、返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-386">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="23763-387"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> によって提供されるメソッドでは、すべての種類のアドレスの標準的なリンク生成機能がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-387">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="23763-388">リンク ジェネレーターを使用する最も便利な方法は、特定のアドレスの種類の操作を実行する拡張メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="23763-388">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type:</span></span>

| <span data-ttu-id="23763-389">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="23763-389">Extension Method</span></span> | <span data-ttu-id="23763-390">説明</span><span class="sxs-lookup"><span data-stu-id="23763-390">Description</span></span> |
| ---
<span data-ttu-id="23763-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-391">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-392">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-392">'Blazor'</span></span>
- <span data-ttu-id="23763-393">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-393">'Identity'</span></span>
- <span data-ttu-id="23763-394">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-394">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-395">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-395">'Razor'</span></span>
- <span data-ttu-id="23763-396">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-396">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-397">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-398">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-398">'Blazor'</span></span>
- <span data-ttu-id="23763-399">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-399">'Identity'</span></span>
- <span data-ttu-id="23763-400">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-400">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-401">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-401">'Razor'</span></span>
- <span data-ttu-id="23763-402">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-402">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-403">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-404">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-404">'Blazor'</span></span>
- <span data-ttu-id="23763-405">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-405">'Identity'</span></span>
- <span data-ttu-id="23763-406">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-406">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-407">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-407">'Razor'</span></span>
- <span data-ttu-id="23763-408">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-408">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-409">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-410">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-410">'Blazor'</span></span>
- <span data-ttu-id="23763-411">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-411">'Identity'</span></span>
- <span data-ttu-id="23763-412">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-412">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-413">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-413">'Razor'</span></span>
- <span data-ttu-id="23763-414">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-414">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-416">'Blazor'</span></span>
- <span data-ttu-id="23763-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-417">'Identity'</span></span>
- <span data-ttu-id="23763-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-419">'Razor'</span></span>
- <span data-ttu-id="23763-420">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-422">'Blazor'</span></span>
- <span data-ttu-id="23763-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-423">'Identity'</span></span>
- <span data-ttu-id="23763-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-425">'Razor'</span></span>
- <span data-ttu-id="23763-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-426">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-427">-------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-428">'Blazor'</span></span>
- <span data-ttu-id="23763-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-429">'Identity'</span></span>
- <span data-ttu-id="23763-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-431">'Razor'</span></span>
- <span data-ttu-id="23763-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-434">'Blazor'</span></span>
- <span data-ttu-id="23763-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-435">'Identity'</span></span>
- <span data-ttu-id="23763-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-437">'Razor'</span></span>
- <span data-ttu-id="23763-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-440">'Blazor'</span></span>
- <span data-ttu-id="23763-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-441">'Identity'</span></span>
- <span data-ttu-id="23763-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-443">'Razor'</span></span>
- <span data-ttu-id="23763-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-444">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 指定された値に基づき、絶対パスを含む URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-445">------ | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="23763-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 指定された値に基づき、絶対 URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-446">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="23763-447"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> メソッド呼び出しによる次の影響に注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-447">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="23763-448">受信要求の `Host` ヘッダーが確認されないアプリ構成では、`GetUri*` 拡張メソッドは注意して使用してください。</span><span class="sxs-lookup"><span data-stu-id="23763-448">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="23763-449">受信要求の `Host` ヘッダーが確認されていない場合、信頼されていない要求入力を、ビューまたはページの URI でクライアントに送り返すことができます。</span><span class="sxs-lookup"><span data-stu-id="23763-449">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view or page.</span></span> <span data-ttu-id="23763-450">すべての運用アプリで、`Host` ヘッダーを既知の有効な値と照らし合わせて確認するようにサーバーを構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23763-450">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="23763-451">ミドルウェアで `Map` または `MapWhen` と組み合わせて、<xref:Microsoft.AspNetCore.Routing.LinkGenerator> を使用する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-451">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="23763-452">`Map*` では、実行中の要求の基本パスが変更され、リンク生成の出力に影響します。</span><span class="sxs-lookup"><span data-stu-id="23763-452">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="23763-453">すべての <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API で基本パスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-453">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="23763-454">リンク生成への `Map*` の影響を元に戻すための空の基本パスを指定してください。</span><span class="sxs-lookup"><span data-stu-id="23763-454">Specify an empty base path to undo the `Map*` affect on link generation.</span></span>

### <a name="middleware-example"></a><span data-ttu-id="23763-455">ミドルウェアの例</span><span class="sxs-lookup"><span data-stu-id="23763-455">Middleware example</span></span>

<span data-ttu-id="23763-456">次の例では、ミドルウェアで <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API を使用して、商品をリストするアクション メソッドへのリンクを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-456">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create a link to an action method that lists store products.</span></span> <span data-ttu-id="23763-457">リンク ジェネレーターは、クラスに挿入し、`GenerateLink` を呼び出すことで、アプリのどのクラスでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-457">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a><span data-ttu-id="23763-458">ルート テンプレート参照</span><span class="sxs-lookup"><span data-stu-id="23763-458">Route template reference</span></span>

<span data-ttu-id="23763-459">`{}` 内のトークンでは、ルートが一致した場合にバインドされるルート パラメーターが定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-459">Tokens within `{}` define route parameters that are bound if the route is matched.</span></span> <span data-ttu-id="23763-460">1 つのルート セグメントに複数のルート パラメーターを定義できますが、各ルート パラメーターをリテラル値で区切る必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-460">More than one route parameter can be defined in a route segment, but route parameters  must be separated by a literal value.</span></span> <span data-ttu-id="23763-461">たとえば、`{controller=Home}{action=Index}` は有効なルートになりません。`{controller}` と `{action}` の間にリテラル値がないためです。</span><span class="sxs-lookup"><span data-stu-id="23763-461">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span>  <span data-ttu-id="23763-462">ルート パラメーターには名前を付ける必要があります。付加的な属性を指定することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-462">Route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="23763-463">ルート パラメーター以外のリテラル テキスト (`{id}` など) とパス区切り `/` は URL のテキストに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-463">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="23763-464">テキスト照合は復号された URL のパスを基盤とし、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="23763-464">Text matching is case-insensitive and based on the decoded representation of the URL's path.</span></span> <span data-ttu-id="23763-465">リテラル ルート パラメーターの区切り記号 (`{` または `}`) を照合するには、文字を繰り返して区切り記号をエスケープします。</span><span class="sxs-lookup"><span data-stu-id="23763-465">To match a literal route parameter delimiter `{` or `}`, escape the delimiter by repeating the character.</span></span> <span data-ttu-id="23763-466">たとえば、`{{` または `}}` です。</span><span class="sxs-lookup"><span data-stu-id="23763-466">For example `{{` or `}}`.</span></span>

<span data-ttu-id="23763-467">アスタリスク `*` または二重アスタリスク`**`:</span><span class="sxs-lookup"><span data-stu-id="23763-467">Asterisk `*` or double asterisk `**`:</span></span>

* <span data-ttu-id="23763-468">ルート パラメーターのプレフィックスとして使用して、URI の残りの部分にバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-468">Can be used as a prefix to a route parameter to bind to the rest of the URI.</span></span>
* <span data-ttu-id="23763-469">**キャッチオール** パラメーターと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="23763-469">Are called a **catch-all** parameters.</span></span> <span data-ttu-id="23763-470">`blog/{**slug}` の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="23763-470">For example, `blog/{**slug}`:</span></span>
  * <span data-ttu-id="23763-471">`/blog` で始まり、その後に任意の値が続く URI と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-471">Matches any URI that starts with `/blog` and has any value following it.</span></span>
  * <span data-ttu-id="23763-472">`/blog` に続く値は、[slug](https://developer.mozilla.org/docs/Glossary/Slug) ルート値に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="23763-472">The value following `/blog` is assigned to the [slug](https://developer.mozilla.org/docs/Glossary/Slug) route value.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<span data-ttu-id="23763-473">キャッチオール パラメーターは空の文字列に一致することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-473">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="23763-474">キャッチオール パラメーターでは、パス区切り `/` 文字を含め、URL の生成にルートが使用されるときに適切な文字がエスケープされます。</span><span class="sxs-lookup"><span data-stu-id="23763-474">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator `/` characters.</span></span> <span data-ttu-id="23763-475">たとえば、ルート値が `{ path = "my/path" }` のルート `foo/{*path}` では、`foo/my%2Fpath` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-475">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="23763-476">エスケープされたスラッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-476">Note the escaped forward slash.</span></span> <span data-ttu-id="23763-477">パス区切り文字をラウンドトリップさせるには、`**` ルート パラメーター プレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-477">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="23763-478">`{ path = "my/path" }` のルート `foo/{**path}` では、`foo/my/path` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-478">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="23763-479">任意のファイル拡張子が付いたファイル名のキャプチャを試行する URL パターンには、追加の考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="23763-479">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="23763-480">たとえば、テンプレート `files/{filename}.{ext?}` について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-480">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="23763-481">`filename` と `ext` の両方の値が存在するときに、両方の値が入力されます。</span><span class="sxs-lookup"><span data-stu-id="23763-481">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="23763-482">URL に `filename` の値だけが存在する場合、末尾の `.` は任意であるため、このルートは一致となります。</span><span class="sxs-lookup"><span data-stu-id="23763-482">If only a value for `filename` exists in the URL, the route matches because the trailing `.` is  optional.</span></span> <span data-ttu-id="23763-483">次の URL はこのルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-483">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="23763-484">ルート パラメーターには、**既定値** が含まれることがあります。パラメーター名の後に既定値を指定し、等号 (`=`) で区切ることで指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-484">Route parameters may have **default values** designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="23763-485">たとえば、`{controller=Home}` では、`controller` の既定値として `Home` が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-485">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="23763-486">パラメーターの URL に値がない場合、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-486">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="23763-487">ルート パラメーターは、パラメーター名の終わりに疑問符 (`?`) を追加することでオプションとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-487">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name.</span></span> <span data-ttu-id="23763-488">たとえば、`id?` のようにします。</span><span class="sxs-lookup"><span data-stu-id="23763-488">For example, `id?`.</span></span> <span data-ttu-id="23763-489">省略可能な値と既定のルート パラメーターの違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="23763-489">The difference between optional values and default route parameters is:</span></span>

* <span data-ttu-id="23763-490">既定値を持つルート パラメーターでは常に値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-490">A route parameter with a default value always produces a value.</span></span>
* <span data-ttu-id="23763-491">省略可能なパラメーターには、要求 URL によって値が指定された場合にのみ値が含められます。</span><span class="sxs-lookup"><span data-stu-id="23763-491">An optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="23763-492">ルート パラメーターには、URL からバインドされるルート値に一致しなければならないという制約が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-492">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="23763-493">`:` と制約名をルート パラメーター名の後に追加すると、ルート パラメーターのインライン制約が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-493">Adding `:` and constraint name after the route parameter name specifies an inline constraint on a route parameter.</span></span> <span data-ttu-id="23763-494">その制約で引数が要求される場合、制約名の後にかっこ `(...)` で囲まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-494">If the constraint requires arguments, they're enclosed in parentheses `(...)` after the constraint name.</span></span> <span data-ttu-id="23763-495">複数の "*インライン制約*" を指定するには、別の `:` と制約名を追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-495">Multiple *inline constraints* can be specified by appending another `:` and constraint name.</span></span>

<span data-ttu-id="23763-496">制約名と引数が <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> サービスに渡され、URL 処理で使用する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-496">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="23763-497">たとえば、ルート テンプレート `blog/{article:minlength(10)}` によって、制約 `minlength` と引数 `10` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-497">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="23763-498">ルート制約の詳細とこのフレームワークによって指定される制約のリストについては、「[ルート制約参照](#route-constraint-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-498">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="23763-499">ルート パラメーターには、パラメーター トランスフォーマーを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="23763-499">Route parameters may also have parameter transformers.</span></span> <span data-ttu-id="23763-500">パラメーター トランスフォーマーを指定すると、リンクを生成し、アクションおよびページを URL と一致させるときにパラメーターの値が変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-500">Parameter transformers transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="23763-501">制約と同様に、パラメーター トランスフォーマーをルート パラメーターにインラインで追加することができます。その場合、ルート パラメーター名の後に `:` とトランスフォーマー名を追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-501">Like constraints, parameter transformers can be added inline to a route parameter by adding a `:` and transformer name after the route parameter name.</span></span> <span data-ttu-id="23763-502">たとえば、ルート テンプレート `blog/{article:slugify}` では、`slugify` トランスフォーマーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-502">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="23763-503">パラメーター トランスフォーマーの詳細については、「[パラメーター トランスフォーマー参照](#parameter-transformer-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-503">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="23763-504">次の表に、ルート テンプレートの例とその動作を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-504">The following table demonstrates example route templates and their behavior:</span></span>

| <span data-ttu-id="23763-505">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="23763-505">Route Template</span></span>                           | <span data-ttu-id="23763-506">一致する URI の例</span><span class="sxs-lookup"><span data-stu-id="23763-506">Example Matching URI</span></span>    | <span data-ttu-id="23763-507">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="23763-507">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="23763-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-508">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-509">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-509">'Blazor'</span></span>
- <span data-ttu-id="23763-510">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-510">'Identity'</span></span>
- <span data-ttu-id="23763-511">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-511">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-512">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-512">'Razor'</span></span>
- <span data-ttu-id="23763-513">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-513">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-514">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-515">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-515">'Blazor'</span></span>
- <span data-ttu-id="23763-516">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-516">'Identity'</span></span>
- <span data-ttu-id="23763-517">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-517">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-518">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-518">'Razor'</span></span>
- <span data-ttu-id="23763-519">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-519">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-520">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-521">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-521">'Blazor'</span></span>
- <span data-ttu-id="23763-522">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-522">'Identity'</span></span>
- <span data-ttu-id="23763-523">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-523">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-524">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-524">'Razor'</span></span>
- <span data-ttu-id="23763-525">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-525">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-526">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-527">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-527">'Blazor'</span></span>
- <span data-ttu-id="23763-528">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-528">'Identity'</span></span>
- <span data-ttu-id="23763-529">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-529">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-530">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-530">'Razor'</span></span>
- <span data-ttu-id="23763-531">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-531">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-532">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-533">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-533">'Blazor'</span></span>
- <span data-ttu-id="23763-534">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-534">'Identity'</span></span>
- <span data-ttu-id="23763-535">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-535">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-536">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-536">'Razor'</span></span>
- <span data-ttu-id="23763-537">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-537">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-538">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-539">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-539">'Blazor'</span></span>
- <span data-ttu-id="23763-540">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-540">'Identity'</span></span>
- <span data-ttu-id="23763-541">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-541">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-542">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-542">'Razor'</span></span>
- <span data-ttu-id="23763-543">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-543">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-544">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-545">'Blazor'</span></span>
- <span data-ttu-id="23763-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-546">'Identity'</span></span>
- <span data-ttu-id="23763-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-548">'Razor'</span></span>
- <span data-ttu-id="23763-549">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-550">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-551">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-551">'Blazor'</span></span>
- <span data-ttu-id="23763-552">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-552">'Identity'</span></span>
- <span data-ttu-id="23763-553">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-553">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-554">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-554">'Razor'</span></span>
- <span data-ttu-id="23763-555">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-555">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-556">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-557">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-557">'Blazor'</span></span>
- <span data-ttu-id="23763-558">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-558">'Identity'</span></span>
- <span data-ttu-id="23763-559">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-559">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-560">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-560">'Razor'</span></span>
- <span data-ttu-id="23763-561">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-561">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-562">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-563">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-563">'Blazor'</span></span>
- <span data-ttu-id="23763-564">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-564">'Identity'</span></span>
- <span data-ttu-id="23763-565">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-565">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-566">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-566">'Razor'</span></span>
- <span data-ttu-id="23763-567">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-567">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-568">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-569">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-569">'Blazor'</span></span>
- <span data-ttu-id="23763-570">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-570">'Identity'</span></span>
- <span data-ttu-id="23763-571">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-571">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-572">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-572">'Razor'</span></span>
- <span data-ttu-id="23763-573">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-573">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-574">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-575">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-575">'Blazor'</span></span>
- <span data-ttu-id="23763-576">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-576">'Identity'</span></span>
- <span data-ttu-id="23763-577">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-577">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-578">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-578">'Razor'</span></span>
- <span data-ttu-id="23763-579">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-579">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-580">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-581">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-581">'Blazor'</span></span>
- <span data-ttu-id="23763-582">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-582">'Identity'</span></span>
- <span data-ttu-id="23763-583">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-583">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-584">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-584">'Razor'</span></span>
- <span data-ttu-id="23763-585">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-585">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-586">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-587">'Blazor'</span></span>
- <span data-ttu-id="23763-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-588">'Identity'</span></span>
- <span data-ttu-id="23763-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-590">'Razor'</span></span>
- <span data-ttu-id="23763-591">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-592">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-593">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-593">'Blazor'</span></span>
- <span data-ttu-id="23763-594">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-594">'Identity'</span></span>
- <span data-ttu-id="23763-595">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-595">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-596">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-596">'Razor'</span></span>
- <span data-ttu-id="23763-597">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-597">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-598">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-599">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-599">'Blazor'</span></span>
- <span data-ttu-id="23763-600">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-600">'Identity'</span></span>
- <span data-ttu-id="23763-601">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-601">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-602">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-602">'Razor'</span></span>
- <span data-ttu-id="23763-603">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-603">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-604">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-605">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-605">'Blazor'</span></span>
- <span data-ttu-id="23763-606">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-606">'Identity'</span></span>
- <span data-ttu-id="23763-607">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-607">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-608">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-608">'Razor'</span></span>
- <span data-ttu-id="23763-609">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-609">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-611">'Blazor'</span></span>
- <span data-ttu-id="23763-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-612">'Identity'</span></span>
- <span data-ttu-id="23763-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-614">'Razor'</span></span>
- <span data-ttu-id="23763-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-615">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-616">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-617">'Blazor'</span></span>
- <span data-ttu-id="23763-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-618">'Identity'</span></span>
- <span data-ttu-id="23763-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-620">'Razor'</span></span>
- <span data-ttu-id="23763-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-623">'Blazor'</span></span>
- <span data-ttu-id="23763-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-624">'Identity'</span></span>
- <span data-ttu-id="23763-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-626">'Razor'</span></span>
- <span data-ttu-id="23763-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-629">'Blazor'</span></span>
- <span data-ttu-id="23763-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-630">'Identity'</span></span>
- <span data-ttu-id="23763-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-632">'Razor'</span></span>
- <span data-ttu-id="23763-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-635">'Blazor'</span></span>
- <span data-ttu-id="23763-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-636">'Identity'</span></span>
- <span data-ttu-id="23763-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-638">'Razor'</span></span>
- <span data-ttu-id="23763-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-641">'Blazor'</span></span>
- <span data-ttu-id="23763-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-642">'Identity'</span></span>
- <span data-ttu-id="23763-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-644">'Razor'</span></span>
- <span data-ttu-id="23763-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-647">'Blazor'</span></span>
- <span data-ttu-id="23763-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-648">'Identity'</span></span>
- <span data-ttu-id="23763-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-650">'Razor'</span></span>
- <span data-ttu-id="23763-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-653">'Blazor'</span></span>
- <span data-ttu-id="23763-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-654">'Identity'</span></span>
- <span data-ttu-id="23763-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-656">'Razor'</span></span>
- <span data-ttu-id="23763-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-657">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-658">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-659">'Blazor'</span></span>
- <span data-ttu-id="23763-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-660">'Identity'</span></span>
- <span data-ttu-id="23763-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-662">'Razor'</span></span>
- <span data-ttu-id="23763-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-663">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-664">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-665">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-665">'Blazor'</span></span>
- <span data-ttu-id="23763-666">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-666">'Identity'</span></span>
- <span data-ttu-id="23763-667">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-667">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-668">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-668">'Razor'</span></span>
- <span data-ttu-id="23763-669">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-669">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-670">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-671">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-671">'Blazor'</span></span>
- <span data-ttu-id="23763-672">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-672">'Identity'</span></span>
- <span data-ttu-id="23763-673">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-673">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-674">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-674">'Razor'</span></span>
- <span data-ttu-id="23763-675">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-675">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-676">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-677">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-677">'Blazor'</span></span>
- <span data-ttu-id="23763-678">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-678">'Identity'</span></span>
- <span data-ttu-id="23763-679">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-679">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-680">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-680">'Razor'</span></span>
- <span data-ttu-id="23763-681">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-681">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-683">'Blazor'</span></span>
- <span data-ttu-id="23763-684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-684">'Identity'</span></span>
- <span data-ttu-id="23763-685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-685">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-686">'Razor'</span></span>
- <span data-ttu-id="23763-687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-689">'Blazor'</span></span>
- <span data-ttu-id="23763-690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-690">'Identity'</span></span>
- <span data-ttu-id="23763-691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-691">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-692">'Razor'</span></span>
- <span data-ttu-id="23763-693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-695">'Blazor'</span></span>
- <span data-ttu-id="23763-696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-696">'Identity'</span></span>
- <span data-ttu-id="23763-697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-697">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-698">'Razor'</span></span>
- <span data-ttu-id="23763-699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-701">'Blazor'</span></span>
- <span data-ttu-id="23763-702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-702">'Identity'</span></span>
- <span data-ttu-id="23763-703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-703">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-704">'Razor'</span></span>
- <span data-ttu-id="23763-705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-707">'Blazor'</span></span>
- <span data-ttu-id="23763-708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-708">'Identity'</span></span>
- <span data-ttu-id="23763-709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-709">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-710">'Razor'</span></span>
- <span data-ttu-id="23763-711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-713">'Blazor'</span></span>
- <span data-ttu-id="23763-714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-714">'Identity'</span></span>
- <span data-ttu-id="23763-715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-715">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-716">'Razor'</span></span>
- <span data-ttu-id="23763-717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-719">'Blazor'</span></span>
- <span data-ttu-id="23763-720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-720">'Identity'</span></span>
- <span data-ttu-id="23763-721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-721">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-722">'Razor'</span></span>
- <span data-ttu-id="23763-723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-725">'Blazor'</span></span>
- <span data-ttu-id="23763-726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-726">'Identity'</span></span>
- <span data-ttu-id="23763-727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-727">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-728">'Razor'</span></span>
- <span data-ttu-id="23763-729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-731">'Blazor'</span></span>
- <span data-ttu-id="23763-732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-732">'Identity'</span></span>
- <span data-ttu-id="23763-733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-733">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-734">'Razor'</span></span>
- <span data-ttu-id="23763-735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-737">'Blazor'</span></span>
- <span data-ttu-id="23763-738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-738">'Identity'</span></span>
- <span data-ttu-id="23763-739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-739">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-740">'Razor'</span></span>
- <span data-ttu-id="23763-741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-741">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-742">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-743">'Blazor'</span></span>
- <span data-ttu-id="23763-744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-744">'Identity'</span></span>
- <span data-ttu-id="23763-745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-745">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-746">'Razor'</span></span>
- <span data-ttu-id="23763-747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-749">'Blazor'</span></span>
- <span data-ttu-id="23763-750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-750">'Identity'</span></span>
- <span data-ttu-id="23763-751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-751">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-752">'Razor'</span></span>
- <span data-ttu-id="23763-753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-755">'Blazor'</span></span>
- <span data-ttu-id="23763-756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-756">'Identity'</span></span>
- <span data-ttu-id="23763-757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-757">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-758">'Razor'</span></span>
- <span data-ttu-id="23763-759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-761">'Blazor'</span></span>
- <span data-ttu-id="23763-762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-762">'Identity'</span></span>
- <span data-ttu-id="23763-763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-763">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-764">'Razor'</span></span>
- <span data-ttu-id="23763-765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-767">'Blazor'</span></span>
- <span data-ttu-id="23763-768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-768">'Identity'</span></span>
- <span data-ttu-id="23763-769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-769">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-770">'Razor'</span></span>
- <span data-ttu-id="23763-771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-773">'Blazor'</span></span>
- <span data-ttu-id="23763-774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-774">'Identity'</span></span>
- <span data-ttu-id="23763-775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-775">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-776">'Razor'</span></span>
- <span data-ttu-id="23763-777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-779">'Blazor'</span></span>
- <span data-ttu-id="23763-780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-780">'Identity'</span></span>
- <span data-ttu-id="23763-781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-781">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-782">'Razor'</span></span>
- <span data-ttu-id="23763-783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-785">'Blazor'</span></span>
- <span data-ttu-id="23763-786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-786">'Identity'</span></span>
- <span data-ttu-id="23763-787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-787">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-788">'Razor'</span></span>
- <span data-ttu-id="23763-789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-791">'Blazor'</span></span>
- <span data-ttu-id="23763-792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-792">'Identity'</span></span>
- <span data-ttu-id="23763-793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-793">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-794">'Razor'</span></span>
- <span data-ttu-id="23763-795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-797">'Blazor'</span></span>
- <span data-ttu-id="23763-798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-798">'Identity'</span></span>
- <span data-ttu-id="23763-799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-799">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-800">'Razor'</span></span>
- <span data-ttu-id="23763-801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-803">'Blazor'</span></span>
- <span data-ttu-id="23763-804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-804">'Identity'</span></span>
- <span data-ttu-id="23763-805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-805">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-806">'Razor'</span></span>
- <span data-ttu-id="23763-807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-807">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-808">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-809">'Blazor'</span></span>
- <span data-ttu-id="23763-810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-810">'Identity'</span></span>
- <span data-ttu-id="23763-811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-811">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-812">'Razor'</span></span>
- <span data-ttu-id="23763-813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-815">'Blazor'</span></span>
- <span data-ttu-id="23763-816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-816">'Identity'</span></span>
- <span data-ttu-id="23763-817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-817">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-818">'Razor'</span></span>
- <span data-ttu-id="23763-819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-821">'Blazor'</span></span>
- <span data-ttu-id="23763-822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-822">'Identity'</span></span>
- <span data-ttu-id="23763-823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-823">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-824">'Razor'</span></span>
- <span data-ttu-id="23763-825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-827">'Blazor'</span></span>
- <span data-ttu-id="23763-828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-828">'Identity'</span></span>
- <span data-ttu-id="23763-829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-829">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-830">'Razor'</span></span>
- <span data-ttu-id="23763-831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-833">'Blazor'</span></span>
- <span data-ttu-id="23763-834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-834">'Identity'</span></span>
- <span data-ttu-id="23763-835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-835">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-836">'Razor'</span></span>
- <span data-ttu-id="23763-837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-839">'Blazor'</span></span>
- <span data-ttu-id="23763-840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-840">'Identity'</span></span>
- <span data-ttu-id="23763-841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-841">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-842">'Razor'</span></span>
- <span data-ttu-id="23763-843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-845">'Blazor'</span></span>
- <span data-ttu-id="23763-846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-846">'Identity'</span></span>
- <span data-ttu-id="23763-847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-847">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-848">'Razor'</span></span>
- <span data-ttu-id="23763-849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-851">'Blazor'</span></span>
- <span data-ttu-id="23763-852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-852">'Identity'</span></span>
- <span data-ttu-id="23763-853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-853">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-854">'Razor'</span></span>
- <span data-ttu-id="23763-855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-857">'Blazor'</span></span>
- <span data-ttu-id="23763-858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-858">'Identity'</span></span>
- <span data-ttu-id="23763-859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-859">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-860">'Razor'</span></span>
- <span data-ttu-id="23763-861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-863">'Blazor'</span></span>
- <span data-ttu-id="23763-864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-864">'Identity'</span></span>
- <span data-ttu-id="23763-865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-865">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-866">'Razor'</span></span>
- <span data-ttu-id="23763-867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-869">'Blazor'</span></span>
- <span data-ttu-id="23763-870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-870">'Identity'</span></span>
- <span data-ttu-id="23763-871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-871">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-872">'Razor'</span></span>
- <span data-ttu-id="23763-873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-875">'Blazor'</span></span>
- <span data-ttu-id="23763-876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-876">'Identity'</span></span>
- <span data-ttu-id="23763-877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-877">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-878">'Razor'</span></span>
- <span data-ttu-id="23763-879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-879">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-880">------------------------------------- | | `hello`                                  | `/hello`                | 単一パス `/hello` にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-880">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="23763-881">| | `{Page=Home}`                            | `/`                     | 一致し、`Page` が `Home` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-881">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="23763-882">| | `{Page=Home}`                            | `/Contact`              | 一致し、`Page` が `Contact` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-882">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="23763-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` コントローラーと `List` アクションにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-883">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="23763-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` コントローラーと `Details` アクションにマッピングされ、`id` は 123 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-884">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action with`id` set to 123.</span></span> <span data-ttu-id="23763-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` コントローラーと `Index` メソッドにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-885">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method.</span></span> <span data-ttu-id="23763-886">`id` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-886">`id` is ignored.</span></span>        <span data-ttu-id="23763-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | `Products` コントローラーと `Index` メソッドにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-887">| | `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Maps to the `Products` controller and `Index` method.</span></span> <span data-ttu-id="23763-888">`id` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-888">`id` is ignored.</span></span>        |

<span data-ttu-id="23763-889">一般的に、テンプレートの利用が最も簡単なルーティングの手法となります。</span><span class="sxs-lookup"><span data-stu-id="23763-889">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="23763-890">ルート テンプレート以外では、制約と既定値も指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-890">Constraints and defaults can also be specified outside the route template.</span></span>

### <a name="complex-segments"></a><span data-ttu-id="23763-891">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="23763-891">Complex segments</span></span>

<span data-ttu-id="23763-892">複雑なセグメントは、リテラル区切り文字を右から左に[最短一致](#greedy)の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-892">Complex segments are processed by matching up literal delimiters from right to left in a [non-greedy](#greedy) way.</span></span> <span data-ttu-id="23763-893">たとえば、`[Route("/a{b}c{d}")]` は複雑なセグメントです。</span><span class="sxs-lookup"><span data-stu-id="23763-893">For example, `[Route("/a{b}c{d}")]` is a complex segment.</span></span>
<span data-ttu-id="23763-894">複雑なセグメントは、それらを適切に使用する上で理解する必要がある特定の方法で機能します。</span><span class="sxs-lookup"><span data-stu-id="23763-894">Complex segments work in a particular way that must be understood to use them successfully.</span></span> <span data-ttu-id="23763-895">このセクションの例では、パラメーター値の中に区切り文字が含まれていない場合にのみ、複雑なセグメントが本当にうまく機能する理由を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-895">The example in this section demonstrates why complex segments only really work well when the delimiter text doesn't appear inside the parameter values.</span></span> <span data-ttu-id="23763-896">より複雑なケースでは、[regex](/dotnet/standard/base-types/regular-expressions) を使用し、値を手動で抽出する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-896">Using a [regex](/dotnet/standard/base-types/regular-expressions) and then manually extracting the values is needed for more complex cases.</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="23763-897">これは、ルーティングがテンプレート `/a{b}c{d}` と URL パス `/abcd` を使用して実行するステップの概要です。</span><span class="sxs-lookup"><span data-stu-id="23763-897">This is a summary of the steps that routing performs with the template `/a{b}c{d}` and the URL path `/abcd`.</span></span> <span data-ttu-id="23763-898">`|` は、アルゴリズムの動作を視覚化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-898">The `|` is used to help visualize how the algorithm works:</span></span>

* <span data-ttu-id="23763-899">最初のリテラル (右から左へ) は `c` です。</span><span class="sxs-lookup"><span data-stu-id="23763-899">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="23763-900">そこで、`/abcd` は右から検索され、`/ab|c|d` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-900">So `/abcd` is searched from right and finds `/ab|c|d`.</span></span>
* <span data-ttu-id="23763-901">ここで、右にあるすべてのもの (`d`) がルート パラメーター `{d}` と照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-901">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="23763-902">次のリテラル (右から左へ) は `a` です。</span><span class="sxs-lookup"><span data-stu-id="23763-902">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="23763-903">そのため `/ab|c|d` は中断したところから検索されて、`a` が見つかり、`/|a|b|c|d` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-903">So `/ab|c|d` is searched starting where we left off, then `a` is found `/|a|b|c|d`.</span></span>
* <span data-ttu-id="23763-904">ここで、右の値 (`b`) がルート パラメーター `{b}` と照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-904">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="23763-905">残りのテキストも残りのルート テンプレートも存在しないため、これは一致となります。</span><span class="sxs-lookup"><span data-stu-id="23763-905">There is no remaining text and no remaining route template, so this is a match.</span></span>

<span data-ttu-id="23763-906">同じテンプレート `/a{b}c{d}` と、URL パス `/aabcd` を使用した場合の否定の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="23763-906">Here's an example of a negative case using the same template `/a{b}c{d}` and the URL path `/aabcd`.</span></span> <span data-ttu-id="23763-907">`|` は、アルゴリズムの動作を視覚化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-907">The `|` is used to help visualize how the algorithm works.</span></span> <span data-ttu-id="23763-908">このケースは一致ではありませんが、同じアルゴリズムで説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-908">This case isn't a match, which is explained by the same algorithm:</span></span>
* <span data-ttu-id="23763-909">最初のリテラル (右から左へ) は `c` です。</span><span class="sxs-lookup"><span data-stu-id="23763-909">The first literal, right to left, is `c`.</span></span> <span data-ttu-id="23763-910">そこで、`/aabcd` は右から検索され、`/aab|c|d` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-910">So `/aabcd` is searched from right and finds `/aab|c|d`.</span></span>
* <span data-ttu-id="23763-911">ここで、右にあるすべてのもの (`d`) がルート パラメーター `{d}` と照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-911">Everything to the right (`d`) is now matched to the route parameter `{d}`.</span></span>
* <span data-ttu-id="23763-912">次のリテラル (右から左へ) は `a` です。</span><span class="sxs-lookup"><span data-stu-id="23763-912">The next literal, right to left, is `a`.</span></span> <span data-ttu-id="23763-913">そのため `/aab|c|d` は中断したところから検索されて、`a` が見つかり、`/a|a|b|c|d` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-913">So `/aab|c|d` is searched starting where we left off, then `a` is found `/a|a|b|c|d`.</span></span>
* <span data-ttu-id="23763-914">ここで、右の値 (`b`) がルート パラメーター `{b}` と照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-914">The value to the right (`b`) is now matched to the route parameter `{b}`.</span></span>
* <span data-ttu-id="23763-915">この時点で、テキスト `a` が残っていますが、アルゴリズムは解析するためのルート テンプレートを使い果たしたので、これは一致とはなりません。</span><span class="sxs-lookup"><span data-stu-id="23763-915">At this point there is remaining text `a`, but the algorithm has run out of route template to parse, so this is not a match.</span></span>

<span data-ttu-id="23763-916">照合アルゴリズムは[最短一致](#greedy)のため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-916">Since the matching algorithm is [non-greedy](#greedy):</span></span>

* <span data-ttu-id="23763-917">各ステップで可能な限りの最短のテキストに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-917">It matches the smallest amount of text possible in each step.</span></span>
* <span data-ttu-id="23763-918">パラメーター値の内部に区切り記号の値が表示されている場合は一致しません。</span><span class="sxs-lookup"><span data-stu-id="23763-918">Any case where the delimiter value appears inside the parameter values results in not matching.</span></span>

<span data-ttu-id="23763-919">正規表現を使用すると、一致の動作をより細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="23763-919">Regular expressions provide much more control over their matching behavior.</span></span>

<a name="greedy"></a>

<span data-ttu-id="23763-920">最長一致 ([遅延一致](https://wikipedia.org/wiki/Regular_expression#Lazy_matching) とも呼ばれる) を使用すると、可能な限り長い文字列と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-920">Greedy matching, also know as [lazy matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), matches the largest possible string.</span></span> <span data-ttu-id="23763-921">最短一致は、可能な限り最短の文字列と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-921">Non-greedy matches the smallest possible string.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="23763-922">ルート制約参照</span><span class="sxs-lookup"><span data-stu-id="23763-922">Route constraint reference</span></span>

<span data-ttu-id="23763-923">ルート制約は、受信 URL と一致し、URL パスがルート値にトークン化されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-923">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="23763-924">ルート制約では、通常、ルート テンプレート経由で関連付けられるルート値を調べ、値が許容できるかどうかを true または false で決定します。</span><span class="sxs-lookup"><span data-stu-id="23763-924">Route constraints generally inspect the route value associated via the route template and make a true or false decision about whether the value is acceptable.</span></span> <span data-ttu-id="23763-925">一部のルート制約では、ルート値以外のデータを使用し、要求をルーティングできるかどうかが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="23763-925">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="23763-926">たとえば、<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> はその HTTP Verb に基づいて要求を承認または却下します。</span><span class="sxs-lookup"><span data-stu-id="23763-926">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="23763-927">制約は、要求のルーティングとリンクの生成で使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-927">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="23763-928">入力の検証には制約を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="23763-928">Don't use constraints for input validation.</span></span> <span data-ttu-id="23763-929">入力の検証に制約を使用した場合、入力が無効だと "`404` 見つかりません" が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-929">If constraints are used for input validation, invalid input results in a `404` Not Found response.</span></span> <span data-ttu-id="23763-930">無効な入力の場合は、"`400` 要求が無効です" と適切なエラー メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-930">Invalid input should produce a `400` Bad Request with an appropriate error message.</span></span> <span data-ttu-id="23763-931">ルート制約は、特定のルートに対する入力の妥当性を検証するためではなく、似たようなルートの違いを明らかにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-931">Route constraints are used to disambiguate similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="23763-932">次の表では、ルート制約の例とそれに求められる動作をまとめています。</span><span class="sxs-lookup"><span data-stu-id="23763-932">The following table demonstrates example route constraints and their expected behavior:</span></span>

| <span data-ttu-id="23763-933">制約</span><span class="sxs-lookup"><span data-stu-id="23763-933">constraint</span></span> | <span data-ttu-id="23763-934">例</span><span class="sxs-lookup"><span data-stu-id="23763-934">Example</span></span> | <span data-ttu-id="23763-935">一致の例</span><span class="sxs-lookup"><span data-stu-id="23763-935">Example Matches</span></span> | <span data-ttu-id="23763-936">メモ</span><span class="sxs-lookup"><span data-stu-id="23763-936">Notes</span></span> |
| ---
<span data-ttu-id="23763-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-938">'Blazor'</span></span>
- <span data-ttu-id="23763-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-939">'Identity'</span></span>
- <span data-ttu-id="23763-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-941">'Razor'</span></span>
- <span data-ttu-id="23763-942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-944">'Blazor'</span></span>
- <span data-ttu-id="23763-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-945">'Identity'</span></span>
- <span data-ttu-id="23763-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-947">'Razor'</span></span>
- <span data-ttu-id="23763-948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-950">'Blazor'</span></span>
- <span data-ttu-id="23763-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-951">'Identity'</span></span>
- <span data-ttu-id="23763-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-953">'Razor'</span></span>
- <span data-ttu-id="23763-954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-954">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-955">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-956">'Blazor'</span></span>
- <span data-ttu-id="23763-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-957">'Identity'</span></span>
- <span data-ttu-id="23763-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-959">'Razor'</span></span>
- <span data-ttu-id="23763-960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-960">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-961">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-962">'Blazor'</span></span>
- <span data-ttu-id="23763-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-963">'Identity'</span></span>
- <span data-ttu-id="23763-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-965">'Razor'</span></span>
- <span data-ttu-id="23763-966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-968">'Blazor'</span></span>
- <span data-ttu-id="23763-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-969">'Identity'</span></span>
- <span data-ttu-id="23763-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-971">'Razor'</span></span>
- <span data-ttu-id="23763-972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-974">'Blazor'</span></span>
- <span data-ttu-id="23763-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-975">'Identity'</span></span>
- <span data-ttu-id="23763-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-977">'Razor'</span></span>
- <span data-ttu-id="23763-978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-980">'Blazor'</span></span>
- <span data-ttu-id="23763-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-981">'Identity'</span></span>
- <span data-ttu-id="23763-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-983">'Razor'</span></span>
- <span data-ttu-id="23763-984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-986">'Blazor'</span></span>
- <span data-ttu-id="23763-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-987">'Identity'</span></span>
- <span data-ttu-id="23763-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-989">'Razor'</span></span>
- <span data-ttu-id="23763-990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-990">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-991">-------- | ----- | | `int` | `{id:int}` | `123456789`、`-123456789` | 任意の整数に一致します | | `bool` | `{active:bool}` | `true`、`FALSE` | `true` または `false` に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-991">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false`.</span></span> <span data-ttu-id="23763-992">大文字と小文字は区別されません | | `datetime` | `{dob:datetime}` | `2016-12-31`、`2016-12-31 7:32pm` | インバリアント カルチャの有効な `DateTime` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-992">Case-insensitive | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="23763-993">前の警告を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-993">See preceding warning.</span></span> <span data-ttu-id="23763-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | インバリアント カルチャの有効な `decimal` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-994">| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="23763-995">前の警告を参照してください。| | `double` | `{weight:double}` | `1.234`、`-1,001.01e8` | インバリアント カルチャの有効な `double` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-995">See preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="23763-996">前の警告を参照してください。| | `float` | `{weight:float}` | `1.234`、`-1,001.01e8` | インバリアント カルチャの有効な `float` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-996">See preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="23763-997">前の警告を参照してください。| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | 有効な `Guid` 値に一致します | | `long` | `{ticks:long}` | `123456789`、`-123456789` | 有効な `long` 値に一致します | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 文字列は 4 文字以上でなければなりません | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | 文字列は 8 文字以下でなければなりません | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 文字列は正確に 12 文字でなければなりません | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 文字列は 8 文字以上 16 文字以下でなければなりません | | `min(value)` | `{age:min(18)}` | `19` | 整数値は 18 以上でなければなりません | | `max(value)` | `{age:max(120)}` | `91` | 整数値は 120 以下でなければなりません | | `range(min,max)` | `{age:range(18,120)}` | `91` | 整数値は 18 以上 120 以下でなければなりません | | `alpha` | `{name:alpha}` | `Rick` | 文字列は 1 つ以上の英字 `a`-`z` で構成されている必要があり、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="23763-997">See preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters, `a`-`z` and case-insensitive.</span></span> <span data-ttu-id="23763-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 文字列は正規表現と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-998">| | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression.</span></span> <span data-ttu-id="23763-999">正規表現の定義に関するヒントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-999">See tips about defining a regular expression.</span></span> <span data-ttu-id="23763-1000">| | `required` | `{name:required}` | `Rick` | URL 生成中、非パラメーター値が提示されるように強制するために使用されます |</span><span class="sxs-lookup"><span data-stu-id="23763-1000">| | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="23763-1001">1 のパラメーターには、複数の制約をコロンで区切って適用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1001">Multiple, colon delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="23763-1002">たとえば、次の制約では、パラメーターが 1 以上の整数値に制限されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1002">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="23763-1003">URL の妥当性を検証し、CLR 型に変換するルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1003">Route constraints that verify the URL and are converted to a CLR type always use the invariant culture.</span></span> <span data-ttu-id="23763-1004">たとえば、`int` または `DateTime` の CLR 型に変換される場合などです。</span><span class="sxs-lookup"><span data-stu-id="23763-1004">For example, conversion to the CLR type `int` or `DateTime`.</span></span> <span data-ttu-id="23763-1005">これらの制約では、URL のローカライズが不可であることが前提です。</span><span class="sxs-lookup"><span data-stu-id="23763-1005">These constraints assume that the URL is not localizable.</span></span> <span data-ttu-id="23763-1006">フレームワークから提供されるルート制約がルート値に格納されている値を変更することはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1006">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="23763-1007">URL から解析されたルート値はすべて文字列として格納されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1007">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="23763-1008">たとえば、`float` 制約はルート値を浮動小数に変換しますが、変換された値は、浮動小数に変換できることを検証するためにだけ利用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1008">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

### <a name="regular-expressions-in-constraints"></a><span data-ttu-id="23763-1009">正規表現の制約</span><span class="sxs-lookup"><span data-stu-id="23763-1009">Regular expressions in constraints</span></span>

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="23763-1010">正規表現は、`regex(...)` ルート制約を使用して、インライン制約として指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1010">Regular expressions can be specified as inline constraints using the `regex(...)` route constraint.</span></span> <span data-ttu-id="23763-1011"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> ファミリのメソッドでも、制約のオブジェクト リテラルを取ります。</span><span class="sxs-lookup"><span data-stu-id="23763-1011">Methods in the <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> family also accept an object literal of constraints.</span></span> <span data-ttu-id="23763-1012">この形式が使用されている場合、文字列値は正規表現として解釈されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1012">If that form is used, string values are interpreted as regular expressions.</span></span>

<span data-ttu-id="23763-1013">次のコードでは、インラインで regex 制約が使用されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1013">The following code uses an inline regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

<span data-ttu-id="23763-1014">次のコードでは、regex 制約の指定にオブジェクト リテラルが使用されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1014">The following code uses an object literal to specify a regex constraint:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

<span data-ttu-id="23763-1015">ASP.NET Core フレームワークでは、正規表現コンストラクターに `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1015">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="23763-1016">これらのメンバーの詳細については、「<xref:System.Text.RegularExpressions.RegexOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1016">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="23763-1017">正規表現では、ルーティングや C# 言語で使用されるものに似た区切り記号とトークンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1017">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="23763-1018">正規表現トークンはエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1018">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="23763-1019">インライン制約で正規表現 `^\d{3}-\d{2}-\d{4}$` を使用するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1019">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in an inline constraint, use one of the following:</span></span>

* <span data-ttu-id="23763-1020">`\` 文字列エスケープ文字をエスケープするには、文字列で指定した `\` 文字を、C# ソース ファイル内の `\\` 文字に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="23763-1020">Replace `\` characters provided in the string as `\\` characters in the C# source file in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="23763-1021">[逐語的文字列リテラル](/dotnet/csharp/language-reference/keywords/string)。</span><span class="sxs-lookup"><span data-stu-id="23763-1021">[Verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="23763-1022">ルーティング パラメーター区切り記号文字 (`{`、`}`、`[`、`]`) をエスケープするには、表現の文字を二重にします (例: `{{`、`}}`、`[[`、`]]`)。</span><span class="sxs-lookup"><span data-stu-id="23763-1022">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression, for example, `{{`, `}}`, `[[`, `]]`.</span></span> <span data-ttu-id="23763-1023">次の表に、正規表現とそれにエスケープを適用した後のものを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-1023">The following table shows a regular expression and its escaped version:</span></span>

| <span data-ttu-id="23763-1024">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-1024">Regular expression</span></span>    | <span data-ttu-id="23763-1025">エスケープ適用後の正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-1025">Escaped regular expression</span></span>     |
| ---
<span data-ttu-id="23763-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1026">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1027">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1027">'Blazor'</span></span>
- <span data-ttu-id="23763-1028">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1028">'Identity'</span></span>
- <span data-ttu-id="23763-1029">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1029">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1030">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1030">'Razor'</span></span>
- <span data-ttu-id="23763-1031">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1031">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1032">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1033">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1033">'Blazor'</span></span>
- <span data-ttu-id="23763-1034">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1034">'Identity'</span></span>
- <span data-ttu-id="23763-1035">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1035">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1036">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1036">'Razor'</span></span>
- <span data-ttu-id="23763-1037">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1037">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1038">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1039">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1039">'Blazor'</span></span>
- <span data-ttu-id="23763-1040">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1040">'Identity'</span></span>
- <span data-ttu-id="23763-1041">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1041">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1042">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1042">'Razor'</span></span>
- <span data-ttu-id="23763-1043">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1043">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1044">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1045">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1045">'Blazor'</span></span>
- <span data-ttu-id="23763-1046">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1046">'Identity'</span></span>
- <span data-ttu-id="23763-1047">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1047">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1048">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1048">'Razor'</span></span>
- <span data-ttu-id="23763-1049">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1049">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1050">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1051">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1051">'Blazor'</span></span>
- <span data-ttu-id="23763-1052">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1052">'Identity'</span></span>
- <span data-ttu-id="23763-1053">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1053">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1054">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1054">'Razor'</span></span>
- <span data-ttu-id="23763-1055">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1055">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1056">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1057">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1057">'Blazor'</span></span>
- <span data-ttu-id="23763-1058">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1058">'Identity'</span></span>
- <span data-ttu-id="23763-1059">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1059">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1060">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1060">'Razor'</span></span>
- <span data-ttu-id="23763-1061">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1061">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1062">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1063">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1063">'Blazor'</span></span>
- <span data-ttu-id="23763-1064">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1064">'Identity'</span></span>
- <span data-ttu-id="23763-1065">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1065">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1066">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1066">'Razor'</span></span>
- <span data-ttu-id="23763-1067">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1067">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1068">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1069">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1069">'Blazor'</span></span>
- <span data-ttu-id="23763-1070">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1070">'Identity'</span></span>
- <span data-ttu-id="23763-1071">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1071">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1072">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1072">'Razor'</span></span>
- <span data-ttu-id="23763-1073">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1073">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1074">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1075">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1075">'Blazor'</span></span>
- <span data-ttu-id="23763-1076">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1076">'Identity'</span></span>
- <span data-ttu-id="23763-1077">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1077">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1078">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1078">'Razor'</span></span>
- <span data-ttu-id="23763-1079">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1079">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1080">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1081">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1081">'Blazor'</span></span>
- <span data-ttu-id="23763-1082">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1082">'Identity'</span></span>
- <span data-ttu-id="23763-1083">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1083">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1084">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1084">'Razor'</span></span>
- <span data-ttu-id="23763-1085">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1085">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1086">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1087">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1087">'Blazor'</span></span>
- <span data-ttu-id="23763-1088">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1088">'Identity'</span></span>
- <span data-ttu-id="23763-1089">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1089">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1090">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1090">'Razor'</span></span>
- <span data-ttu-id="23763-1091">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1091">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1092">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1093">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1093">'Blazor'</span></span>
- <span data-ttu-id="23763-1094">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1094">'Identity'</span></span>
- <span data-ttu-id="23763-1095">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1095">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1096">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1096">'Razor'</span></span>
- <span data-ttu-id="23763-1097">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1097">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1098">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1099">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1099">'Blazor'</span></span>
- <span data-ttu-id="23763-1100">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1100">'Identity'</span></span>
- <span data-ttu-id="23763-1101">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1101">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1102">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1102">'Razor'</span></span>
- <span data-ttu-id="23763-1103">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1103">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1104">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1105">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1105">'Blazor'</span></span>
- <span data-ttu-id="23763-1106">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1106">'Identity'</span></span>
- <span data-ttu-id="23763-1107">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1107">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1108">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1108">'Razor'</span></span>
- <span data-ttu-id="23763-1109">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1109">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1110">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1111">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1111">'Blazor'</span></span>
- <span data-ttu-id="23763-1112">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1112">'Identity'</span></span>
- <span data-ttu-id="23763-1113">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1113">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1114">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1114">'Razor'</span></span>
- <span data-ttu-id="23763-1115">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1115">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1116">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1117">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1117">'Blazor'</span></span>
- <span data-ttu-id="23763-1118">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1118">'Identity'</span></span>
- <span data-ttu-id="23763-1119">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1119">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1120">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1120">'Razor'</span></span>
- <span data-ttu-id="23763-1121">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1121">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1122">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1123">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1123">'Blazor'</span></span>
- <span data-ttu-id="23763-1124">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1124">'Identity'</span></span>
- <span data-ttu-id="23763-1125">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1125">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1126">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1126">'Razor'</span></span>
- <span data-ttu-id="23763-1127">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1127">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1128">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1129">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1129">'Blazor'</span></span>
- <span data-ttu-id="23763-1130">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1130">'Identity'</span></span>
- <span data-ttu-id="23763-1131">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1131">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1132">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1132">'Razor'</span></span>
- <span data-ttu-id="23763-1133">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1133">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1134">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1135">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1135">'Blazor'</span></span>
- <span data-ttu-id="23763-1136">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1136">'Identity'</span></span>
- <span data-ttu-id="23763-1137">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1137">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1138">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1138">'Razor'</span></span>
- <span data-ttu-id="23763-1139">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1139">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1140">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1141">'Blazor'</span></span>
- <span data-ttu-id="23763-1142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1142">'Identity'</span></span>
- <span data-ttu-id="23763-1143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1143">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1144">'Razor'</span></span>
- <span data-ttu-id="23763-1145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1146">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1147">'Blazor'</span></span>
- <span data-ttu-id="23763-1148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1148">'Identity'</span></span>
- <span data-ttu-id="23763-1149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1149">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1150">'Razor'</span></span>
- <span data-ttu-id="23763-1151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1151">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="23763-1152">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="23763-1153">ルーティングで使用される正規表現は、多くの場合、`^` 文字で始まり、これは文字列の開始位置と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1153">Regular expressions used in routing often start with the `^` character and match the starting position of the string.</span></span> <span data-ttu-id="23763-1154">この式は、多くの場合、`$` 文字で終わり、文字列の末尾と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1154">The expressions often end with the `$` character and match the end of the string.</span></span> <span data-ttu-id="23763-1155">`^` 文字と `$` 文字により、正規表現がルート パラメーター値全体に一致することが保証されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1155">The `^` and `$` characters ensure that the regular expression matches the entire route parameter value.</span></span> <span data-ttu-id="23763-1156">`^` 文字と `$` 文字がなければ、意図に反し、正規表現は文字列内のあらゆる部分文字列に一致してしまいます。</span><span class="sxs-lookup"><span data-stu-id="23763-1156">Without the `^` and `$` characters, the regular expression matches any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="23763-1157">下の表では、一致または不一致の理由を例を示し説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-1157">The following table provides examples and explains why they match or fail to match:</span></span>

| <span data-ttu-id="23763-1158">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-1158">Expression</span></span>   | <span data-ttu-id="23763-1159">String</span><span class="sxs-lookup"><span data-stu-id="23763-1159">String</span></span>    | <span data-ttu-id="23763-1160">一致したもの</span><span class="sxs-lookup"><span data-stu-id="23763-1160">Match</span></span> | <span data-ttu-id="23763-1161">コメント</span><span class="sxs-lookup"><span data-stu-id="23763-1161">Comment</span></span>               |
| ---
<span data-ttu-id="23763-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1163">'Blazor'</span></span>
- <span data-ttu-id="23763-1164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1164">'Identity'</span></span>
- <span data-ttu-id="23763-1165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1165">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1166">'Razor'</span></span>
- <span data-ttu-id="23763-1167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1169">'Blazor'</span></span>
- <span data-ttu-id="23763-1170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1170">'Identity'</span></span>
- <span data-ttu-id="23763-1171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1171">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1172">'Razor'</span></span>
- <span data-ttu-id="23763-1173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1175">'Blazor'</span></span>
- <span data-ttu-id="23763-1176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1176">'Identity'</span></span>
- <span data-ttu-id="23763-1177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1177">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1178">'Razor'</span></span>
- <span data-ttu-id="23763-1179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1179">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1180">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1181">'Blazor'</span></span>
- <span data-ttu-id="23763-1182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1182">'Identity'</span></span>
- <span data-ttu-id="23763-1183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1183">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1184">'Razor'</span></span>
- <span data-ttu-id="23763-1185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1185">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1186">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1187">'Blazor'</span></span>
- <span data-ttu-id="23763-1188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1188">'Identity'</span></span>
- <span data-ttu-id="23763-1189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1189">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1190">'Razor'</span></span>
- <span data-ttu-id="23763-1191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1193">'Blazor'</span></span>
- <span data-ttu-id="23763-1194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1194">'Identity'</span></span>
- <span data-ttu-id="23763-1195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1195">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1196">'Razor'</span></span>
- <span data-ttu-id="23763-1197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1197">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1198">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1199">'Blazor'</span></span>
- <span data-ttu-id="23763-1200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1200">'Identity'</span></span>
- <span data-ttu-id="23763-1201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1201">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1202">'Razor'</span></span>
- <span data-ttu-id="23763-1203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1205">'Blazor'</span></span>
- <span data-ttu-id="23763-1206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1206">'Identity'</span></span>
- <span data-ttu-id="23763-1207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1207">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1208">'Razor'</span></span>
- <span data-ttu-id="23763-1209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1211">'Blazor'</span></span>
- <span data-ttu-id="23763-1212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1212">'Identity'</span></span>
- <span data-ttu-id="23763-1213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1213">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1214">'Razor'</span></span>
- <span data-ttu-id="23763-1215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1217">'Blazor'</span></span>
- <span data-ttu-id="23763-1218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1218">'Identity'</span></span>
- <span data-ttu-id="23763-1219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1219">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1220">'Razor'</span></span>
- <span data-ttu-id="23763-1221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1223">'Blazor'</span></span>
- <span data-ttu-id="23763-1224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1224">'Identity'</span></span>
- <span data-ttu-id="23763-1225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1225">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1226">'Razor'</span></span>
- <span data-ttu-id="23763-1227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1229">'Blazor'</span></span>
- <span data-ttu-id="23763-1230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1230">'Identity'</span></span>
- <span data-ttu-id="23763-1231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1231">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1232">'Razor'</span></span>
- <span data-ttu-id="23763-1233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1235">'Blazor'</span></span>
- <span data-ttu-id="23763-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1236">'Identity'</span></span>
- <span data-ttu-id="23763-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1238">'Razor'</span></span>
- <span data-ttu-id="23763-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1241">'Blazor'</span></span>
- <span data-ttu-id="23763-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1242">'Identity'</span></span>
- <span data-ttu-id="23763-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1244">'Razor'</span></span>
- <span data-ttu-id="23763-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1245">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1246">---------- | | `[a-z]{2}`   | hello     | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | 123abc456 | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | mz        | はい   | 式が一致します    | | `[a-z]{2}`   | MZ        | はい   | 大文字と小文字は区別されません    | | `^[a-z]{2}$` | hello     | いいえ    | 上の `^` と `$` を参照してください | | `^[a-z]{2}$` | 123abc456 | いいえ    | 上の `^` と `$` を参照してください |</span><span class="sxs-lookup"><span data-stu-id="23763-1246">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="23763-1247">正規表現構文の詳細については、[.NET Framework 正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1247">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="23763-1248">既知の入力可能値の集まりにパラメーターを制限するには、正規表現を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1248">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="23763-1249">たとえば、`{action:regex(^(list|get|create)$)}` の場合、`action` ルート値は `list`、`get`、`create` とのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1249">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="23763-1250">制約ディクショナリに渡された場合、文字列 `^(list|get|create)$` で同じものになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1250">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="23763-1251">既知の制約に一致しない、制約ディクショナリに渡された制約も、正規表現として扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1251">Constraints that are passed in the constraints dictionary that don't match one of the known constraints are also treated as regular expressions.</span></span> <span data-ttu-id="23763-1252">既知の制約に一致しない、テンプレート内で渡される制約は、正規表現としては扱われません。</span><span class="sxs-lookup"><span data-stu-id="23763-1252">Constraints that are passed  within a template that don't match one of the known constraints are not treated as regular expressions.</span></span>

### <a name="custom-route-constraints"></a><span data-ttu-id="23763-1253">カスタム ルート制約</span><span class="sxs-lookup"><span data-stu-id="23763-1253">Custom route constraints</span></span>

<span data-ttu-id="23763-1254">カスタム ルート制約は、<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスを実装して作成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1254">Custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="23763-1255">`IRouteConstraint` インターフェイスには、<xref:System.Web.Routing.IRouteConstraint.Match*> が含まれています。これでは、制約が満たされている場合は `true` を返し、それ以外の場合は `false` を返します。</span><span class="sxs-lookup"><span data-stu-id="23763-1255">The `IRouteConstraint` interface contains <xref:System.Web.Routing.IRouteConstraint.Match*>, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="23763-1256">カスタム ルート制約は通常必要ありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1256">Custom route constraints are rarely needed.</span></span> <span data-ttu-id="23763-1257">カスタム ルート制約を実装する前に、モデル バインドなどの代替手段を検討してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1257">Before implementing a custom route constraint, consider alternatives, such as model binding.</span></span>

<span data-ttu-id="23763-1258">ASP.NET Core の [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) フォルダーには、制約を作成するための適切な例が用意されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1258">The ASP.NET Core [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) folder provides good examples of creating a constraints.</span></span> <span data-ttu-id="23763-1259">たとえば、[GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18) です。</span><span class="sxs-lookup"><span data-stu-id="23763-1259">For example, [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).</span></span>

<span data-ttu-id="23763-1260">カスタムの `IRouteConstraint` を使うには、サービス コンテナー内の <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> に、ルート制約の種類が登録されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1260">To use a custom `IRouteConstraint`, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the service container.</span></span> <span data-ttu-id="23763-1261">`ConstraintMap` は、ルート制約キーを、その制約を検証する `IRouteConstraint` の実装にマッピングするディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-1261">A `ConstraintMap` is a dictionary that maps route constraint keys to `IRouteConstraint` implementations that validate those constraints.</span></span> <span data-ttu-id="23763-1262">アプリの `ConstraintMap` は、`Startup.ConfigureServices` で、[services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼び出しの一部として、または `services.Configure<RouteOptions>` を使って <xref:Microsoft.AspNetCore.Routing.RouteOptions> を直接構成することで、更新できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1262">An app's `ConstraintMap` can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="23763-1263">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-1263">For example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

<span data-ttu-id="23763-1264">上記の制約は、次のコードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1264">The preceding constraint is applied in the following code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<span data-ttu-id="23763-1265">`MyCustomConstraint` を実装することにより、ルート パラメーターに `0` が適用されなくなります。</span><span class="sxs-lookup"><span data-stu-id="23763-1265">The implementation of `MyCustomConstraint` prevents `0` being applied to a route parameter:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

<span data-ttu-id="23763-1266">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1266">The preceding code:</span></span>

* <span data-ttu-id="23763-1267">ルートの `{id}` セグメントの `0` を禁止します。</span><span class="sxs-lookup"><span data-stu-id="23763-1267">Prevents `0` in the `{id}` segment of the route.</span></span>
* <span data-ttu-id="23763-1268">カスタム制約を実装する基本的な例を示しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1268">Is shown to provide a basic example of implementing a custom constraint.</span></span> <span data-ttu-id="23763-1269">実稼働しているアプリでは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="23763-1269">It should not be used in a production app.</span></span>

<span data-ttu-id="23763-1270">次のコードは、`0` を含む `id` が処理されないようにする優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="23763-1270">The following code is a better approach to preventing an `id` containing a `0` from being processed:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="23763-1271">上記のコードには `MyCustomConstraint` アプローチに対し、次の利点があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1271">The preceding code has the following advantages over the `MyCustomConstraint` approach:</span></span>

* <span data-ttu-id="23763-1272">カスタム制約が必要ありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1272">It doesn't require a custom constraint.</span></span>
* <span data-ttu-id="23763-1273">ルート パラメーターに `0` が含まれている場合は、よりわかりやすいエラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1273">It returns a more descriptive error when the route parameter includes `0`.</span></span>

## <a name="parameter-transformer-reference"></a><span data-ttu-id="23763-1274">パラメーター トランスフォーマー参照</span><span class="sxs-lookup"><span data-stu-id="23763-1274">Parameter transformer reference</span></span>

<span data-ttu-id="23763-1275">パラメーター トランスフォーマー:</span><span class="sxs-lookup"><span data-stu-id="23763-1275">Parameter transformers:</span></span>

* <span data-ttu-id="23763-1276"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> を使用したリンクの生成時に実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1276">Execute when generating a link using <xref:Microsoft.AspNetCore.Routing.LinkGenerator>.</span></span>
* <span data-ttu-id="23763-1277"><xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>を実装します。</span><span class="sxs-lookup"><span data-stu-id="23763-1277">Implement <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.</span></span>
* <span data-ttu-id="23763-1278"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1278">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="23763-1279">パラメーターのルート値を取得し、それを新しい文字列値に変換します。</span><span class="sxs-lookup"><span data-stu-id="23763-1279">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="23763-1280">変換された値は生成されたリンクで使用されるようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1280">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="23763-1281">たとえば、`Url.Action(new { article = "MyTestArticle" })` のルート パターン `blog\{article:slugify}` のカスタム `slugify` パラメーター トランスフォーマーでは、`blog\my-test-article` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1281">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="23763-1282">`IOutboundParameterTransformer` の次の実装を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="23763-1282">Consider the following `IOutboundParameterTransformer` implementation:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

<span data-ttu-id="23763-1283">ルート パターンでパラメーター トランスフォーマーを使用するには、これを `Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用して構成します。</span><span class="sxs-lookup"><span data-stu-id="23763-1283">To use a parameter transformer in a route pattern, configure it using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

<span data-ttu-id="23763-1284">ASP.NET Core フレームワークでは、エンドポイントを解決する URI の変換にパラメーター トランスフォーマーを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1284">The ASP.NET Core framework uses parameter transformers to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="23763-1285">たとえば、パラメーター トランスフォーマーでは、`area`、`controller`、`action`、`page` を照合するために使用されるルート値が変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1285">For example, parameter transformers transform the route values used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="23763-1286">上記のルート テンプレートでは、アクション `SubscriptionManagementController.GetAll` は URI `/subscription-management/get-all` と照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1286">With the preceding route template, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="23763-1287">パラメーター トランスフォーマーでは、リンクを生成するために使用されるルート値は変更されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1287">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="23763-1288">たとえば、`Url.Action("GetAll", "SubscriptionManagement")` では `/subscription-management/get-all` が出力されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1288">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="23763-1289">ASP.NET Core には、生成されたルートでパラメーター トランスフォーマーを使用する API 規則があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1289">ASP.NET Core provides API conventions for using parameter transformers with generated routes:</span></span>

* <span data-ttu-id="23763-1290"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC 規則では、指定されたパラメーター トランスフォーマーをアプリ内のすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1290">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> MVC convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="23763-1291">パラメーター トランスフォーマーでは、置き換えられる属性ルート トークンが変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1291">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="23763-1292">詳細については、「[パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-1292">For more information, see [Use a parameter transformer to customize token replacement](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="23763-1293"> Pages には、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API 規則を使用しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1293"> Pages uses the <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> API convention.</span></span> <span data-ttu-id="23763-1294">この規則では、指定されたパラメーター トランスフォーマーが、自動で検出されたすべての Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1294">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="23763-1295">パラメーター トランスフォーマーでは、Razor Pages ルートのフォルダーとファイル名のセグメントが変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1295">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="23763-1296">詳細については、[パラメーター トランスフォーマーを使用したページ ルートのカスタマイズ](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-1296">For more information, see [Use a parameter transformer to customize page routes](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

<a name="ugr"></a>

## <a name="url-generation-reference"></a><span data-ttu-id="23763-1297">URL 生成参照</span><span class="sxs-lookup"><span data-stu-id="23763-1297">URL generation reference</span></span>

<span data-ttu-id="23763-1298">このセクションには、URL の生成で実装するアルゴリズムの参照情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23763-1298">This section contains a reference for the algorithm implemented by URL generation.</span></span> <span data-ttu-id="23763-1299">実際には、URL 生成の最も複雑な例で、コントローラーまたは Razor Pages が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1299">In practice, most complex examples of URL generation use controllers or Razor Pages.</span></span> <span data-ttu-id="23763-1300">詳細については、[コントローラーでのルーティング](xref:mvc/controllers/routing)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1300">See  [routing in controllers](xref:mvc/controllers/routing) for additional information.</span></span>

<span data-ttu-id="23763-1301">URL の生成プロセスは、[LinkGenerator. GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*)、または類似のメソッドへの呼び出しで開始されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1301">The URL generation process begins with a call to [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) or a similar method.</span></span> <span data-ttu-id="23763-1302">このメソッドは、アドレス、一連のルート値、およびオプションで `HttpContext` からの現在の要求に関する情報と共に渡されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1302">The method is provided with an address, a set of route values, and optionally information about the current request from `HttpContext`.</span></span>

<span data-ttu-id="23763-1303">まずは、アドレスを使用して、アドレスの型に一致する [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) を使用して、一連の候補のエンドポイントが解決されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1303">The first step is to use the address to resolve a set of candidate endpoints using an [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) that matches the address's type.</span></span>

<span data-ttu-id="23763-1304">アドレス スキームによって一連の候補が検出されると、URL の生成操作が成功するまで、エンドポイントは順に並べ替えられ、処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1304">Once of set of candidates is found by the address scheme, the endpoints are ordered and processed iteratively until a URL generation operation succeeds.</span></span> <span data-ttu-id="23763-1305">URL が生成される際には、あいまいさの確認は**行われず**、最初に返される結果が最終的な結果になります。</span><span class="sxs-lookup"><span data-stu-id="23763-1305">URL generation does **not** check for ambiguities, the first result returned is the final result.</span></span>

### <a name="troubleshooting-url-generation-with-logging"></a><span data-ttu-id="23763-1306">ログを使用した URL 生成のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="23763-1306">Troubleshooting URL generation with logging</span></span>

<span data-ttu-id="23763-1307">URL の生成のトラブルシューティングを行う場合、まずは `Microsoft.AspNetCore.Routing` のログ記録レベルを `TRACE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="23763-1307">The first step in troubleshooting URL generation is setting the logging level of `Microsoft.AspNetCore.Routing` to `TRACE`.</span></span> <span data-ttu-id="23763-1308">`LinkGenerator` では、問題のトラブルシューティングに役立つ、処理に関する多くの詳細がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1308">`LinkGenerator` logs many details about its processing which can be useful to troubleshoot problems.</span></span>

<span data-ttu-id="23763-1309">URL 生成の詳細については、「[URL 生成参照](#ugr)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1309">See [URL generation reference](#ugr) for details on URL generation.</span></span>

### <a name="addresses"></a><span data-ttu-id="23763-1310">アドレス</span><span class="sxs-lookup"><span data-stu-id="23763-1310">Addresses</span></span>

<span data-ttu-id="23763-1311">アドレスとは、リンク ジェネレーターへの呼び出しを一連の候補エンドポイントにバインドするために使用する、URL 生成の概念です。</span><span class="sxs-lookup"><span data-stu-id="23763-1311">Addresses are the concept in URL generation used to bind a call into the link generator to a set of candidate endpoints.</span></span>

<span data-ttu-id="23763-1312">アドレスとは、次の 2 つの実装を既定で備えた拡張可能な概念です。</span><span class="sxs-lookup"><span data-stu-id="23763-1312">Addresses are an extensible concept that come with two implementations by default:</span></span>

* <span data-ttu-id="23763-1313">アドレスとして "*エンドポイント名*" (`string`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1313">Using *endpoint name* (`string`) as the address:</span></span>
    * <span data-ttu-id="23763-1314">MVC のルート名と同様の機能があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1314">Provides similar functionality to MVC's route name.</span></span>
    * <span data-ttu-id="23763-1315"><xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> メタデータ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1315">Uses the <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> metadata type.</span></span>
    * <span data-ttu-id="23763-1316">指定された文字列を、登録されているすべてのエンドポイントのメタデータに対して解決します。</span><span class="sxs-lookup"><span data-stu-id="23763-1316">Resolves the provided string against the metadata of all registered endpoints.</span></span>
    * <span data-ttu-id="23763-1317">複数のエンドポイントが同じ名前を使用している場合は、起動時に例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="23763-1317">Throws an exception on startup if multiple endpoints use the same name.</span></span>
    * <span data-ttu-id="23763-1318">コントローラーと Razor Pages 以外で汎用的に使用する場合にお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23763-1318">Recommended for general-purpose use outside of controllers and Razor Pages.</span></span>
* <span data-ttu-id="23763-1319">*ルート値* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) をアドレスとして使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1319">Using *route values* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) as the address:</span></span>
    * <span data-ttu-id="23763-1320">コントローラーおよび Razor Pages での従来の URL 生成と同様の機能があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1320">Provides similar functionality to controllers and Razor Pages legacy URL generation.</span></span>
    * <span data-ttu-id="23763-1321">拡張およびデバッグする場合に非常に複雑です。</span><span class="sxs-lookup"><span data-stu-id="23763-1321">Very complex to extend and debug.</span></span>
    * <span data-ttu-id="23763-1322">`IUrlHelper`、タグ ヘルパー、HTML ヘルパー、アクションの結果などで使用される実装を提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1322">Provides the implementation used by `IUrlHelper`, Tag Helpers, HTML Helpers, Action Results, etc.</span></span>

<span data-ttu-id="23763-1323">アドレス スキームの役割は、任意の条件によって、アドレスと一致するエンドポイント間の関連付けを作成することです。</span><span class="sxs-lookup"><span data-stu-id="23763-1323">The role of the address scheme is to make the association between the address and matching endpoints by arbitrary criteria:</span></span>

* <span data-ttu-id="23763-1324">エンドポイント名スキームでは、基本的な辞書検索が実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1324">The endpoint name scheme performs a basic dictionary lookup.</span></span>
* <span data-ttu-id="23763-1325">ルート値のスキームには、セット アルゴリズムの複雑な最良のサブセットがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-1325">The route values scheme has a complex best subset of set algorithm.</span></span>

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a><span data-ttu-id="23763-1326">アンビエント値と明示的な値</span><span class="sxs-lookup"><span data-stu-id="23763-1326">Ambient values and explicit values</span></span>

<span data-ttu-id="23763-1327">ルーティングは、現在の要求から現在の要求 `HttpContext.Request.RouteValues` のルート値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="23763-1327">From the current request, routing accesses the route values of the current request `HttpContext.Request.RouteValues`.</span></span> <span data-ttu-id="23763-1328">現在の要求に関連付けられている値は、**アンビエント値**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="23763-1328">The values associated with the current request are referred to as the **ambient values**.</span></span> <span data-ttu-id="23763-1329">このドキュメントでは、わかりやすくするために、メソッドに渡されるルート値を**明示的な値**と呼びます。</span><span class="sxs-lookup"><span data-stu-id="23763-1329">For the purpose of clarity, the documentation refers to the route values passed in to methods as **explicit values**.</span></span>

<span data-ttu-id="23763-1330">次の例では、アンビエント値と明示的な値を示しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1330">The following example shows ambient values and explicit values.</span></span> <span data-ttu-id="23763-1331">これでは、アンビエント値を現在の要求と明示的な値から提供します (`{ id = 17, }`)。</span><span class="sxs-lookup"><span data-stu-id="23763-1331">It provides ambient values from the current request and explicit values: `{ id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

<span data-ttu-id="23763-1332">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1332">The preceding code:</span></span>

* <span data-ttu-id="23763-1333">`/Widget/Index/17` を返します。</span><span class="sxs-lookup"><span data-stu-id="23763-1333">Returns `/Widget/Index/17`</span></span>
* <span data-ttu-id="23763-1334">[DI](xref:fundamentals/dependency-injection) を介して <xref:Microsoft.AspNetCore.Routing.LinkGenerator> を取得します。</span><span class="sxs-lookup"><span data-stu-id="23763-1334">Gets <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="23763-1335">次のコードでは、アンビエント値と明示的な値は提供されていません (`{ controller = "Home", action = "Subscribe", id = 17, }`)。</span><span class="sxs-lookup"><span data-stu-id="23763-1335">The following code provides no ambient values and explicit values: `{ controller = "Home", action = "Subscribe", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

<span data-ttu-id="23763-1336">上記のメソッドでは `/Home/Subscribe/17` が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1336">The preceding  method returns `/Home/Subscribe/17`</span></span>

<span data-ttu-id="23763-1337">`WidgetController` の次のコードでは、`/Widget/Subscribe/17` が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1337">The following code in the `WidgetController` returns `/Widget/Subscribe/17`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

<span data-ttu-id="23763-1338">次のコードは、現在の要求と明示的な値のアンビエント値 (`{ action = "Edit", id = 17, }`) からコントローラーを提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1338">The following code provides the controller from ambient values in the current request and explicit values: `{ action = "Edit", id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

<span data-ttu-id="23763-1339">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1339">In the preceding code:</span></span>

* <span data-ttu-id="23763-1340">`/Gadget/Edit/17` が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1340">`/Gadget/Edit/17` is returned.</span></span>
* <span data-ttu-id="23763-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> が <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> を取得します。</span><span class="sxs-lookup"><span data-stu-id="23763-1341"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> gets the <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>.</span></span>
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
<span data-ttu-id="23763-1342">が、アクション メソッドの絶対パスを使用して URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-1342">generates a URL with an absolute path for an action method.</span></span> <span data-ttu-id="23763-1343">URL には、指定した `action` 名と `route` 値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-1343">The URL contains the specified `action` name and `route` values.</span></span>

<span data-ttu-id="23763-1344">次のコードでは、現在の要求と明示的な値 (`{ page = "./Edit, id = 17, }`) からアンビエント値を提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1344">The following code provides ambient values from the current request and explicit values: `{ page = "./Edit, id = 17, }`:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="23763-1345">前のコードでは、[Edit Razor Page]\(Razor ページの編集\) に次のページ ディレクティブが含まれている場合に、`url` を `/Edit/17` に設定します。</span><span class="sxs-lookup"><span data-stu-id="23763-1345">The preceding code sets `url` to  `/Edit/17` when the Edit Razor Page contains the following page directive:</span></span>

 `@page "{id:int}"`

<span data-ttu-id="23763-1346">[編集] ページに `"{id:int}"` ルート テンプレートが含まれない場合は、`url` は `/Edit?id=17` になります。</span><span class="sxs-lookup"><span data-stu-id="23763-1346">If the Edit page doesn't contain the `"{id:int}"` route template, `url` is `/Edit?id=17`.</span></span>

<span data-ttu-id="23763-1347">MVC の <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> の動作により、ここで説明するルールに加えて、複雑なレイヤーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1347">The behavior of MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> adds a layer of complexity in addition to the rules described here:</span></span>

* <span data-ttu-id="23763-1348">`IUrlHelper` は、常に現在の要求からルート値をアンビエント値として提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1348">`IUrlHelper` always provides the route values from the current request as ambient values.</span></span>
* <span data-ttu-id="23763-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) は、開発者がオーバーライドしない場合を除き、常に現在の `action` と `controller` ルート値を明示的な値としてコピーします。</span><span class="sxs-lookup"><span data-stu-id="23763-1349">[IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) always copies the current `action` and `controller` route values as explicit values unless overridden by the developer.</span></span>
* <span data-ttu-id="23763-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) は、オーバーライドされない場合を除き、常に現在の `page` ルート値を明示的な値としてコピーします。</span><span class="sxs-lookup"><span data-stu-id="23763-1350">[IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) always copies the current `page` route value as an explicit value unless overridden.</span></span> <!--by the user-->
* <span data-ttu-id="23763-1351">`IUrlHelper.Page` は、オーバーライドされない場合を除き、常に現在の `handler` ルート値を明示的な値として `null` にオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="23763-1351">`IUrlHelper.Page` always overrides the current `handler` route value with `null` as an explicit values unless overridden.</span></span>

<span data-ttu-id="23763-1352">ユーザーは、MVC が独自のルールに従っていないように見えるため、アンビエント値の動作の詳細にしばしば驚きます。</span><span class="sxs-lookup"><span data-stu-id="23763-1352">Users are often surprised by the behavioral details of ambient values, because MVC doesn't seem to follow its own rules.</span></span> <span data-ttu-id="23763-1353">これまでの経緯および互換性の理由により、`action`、`controller`、`page`、`handler` などの特定のルート値には、独自の特殊な動作があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1353">For historical and compatibility reasons, certain route values such as `action`, `controller`, `page`, and `handler` have their own special-case behavior.</span></span>

<span data-ttu-id="23763-1354">`LinkGenerator.GetPathByAction` と `LinkGenerator.GetPathByPage` の同等の機能では、互換性のために `IUrlHelper` のこの異常と同じ動作が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1354">The equivalent functionality provided by `LinkGenerator.GetPathByAction` and `LinkGenerator.GetPathByPage` duplicates these anomalies of `IUrlHelper` for compatibility.</span></span>

### <a name="url-generation-process"></a><span data-ttu-id="23763-1355">URL の生成処理</span><span class="sxs-lookup"><span data-stu-id="23763-1355">URL generation process</span></span>

<span data-ttu-id="23763-1356">一連の候補エンドポイントが見つかると、URL の生成アルゴリズムでは次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1356">Once the set of candidate endpoints are found, the URL generation algorithm:</span></span>

* <span data-ttu-id="23763-1357">エンドポイントが反復処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1357">Processes the endpoints iteratively.</span></span>
* <span data-ttu-id="23763-1358">最初に成功した結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1358">Returns the first successful result.</span></span>

<span data-ttu-id="23763-1359">このプロセスの最初の手順は**ルート値の無効化**と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="23763-1359">The first step in this process is called **route value invalidation**.</span></span>  <span data-ttu-id="23763-1360">ルート値の無効化は、アンビエント値からどのルート値を使用する必要があり、無視する必要があるかをルーティングが決定するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="23763-1360">Route value invalidation is the process by which routing decides which route values from the ambient values should be used and which should be ignored.</span></span> <span data-ttu-id="23763-1361">各アンビエント値が検討され、明示的な値と組み合わされるか、または無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1361">Each ambient value is considered and either combined with the explicit values, or ignored.</span></span>

<span data-ttu-id="23763-1362">アンビエント値の役割について一番わかりやすい考え方は、一部の一般的なケースでアプリケーション開発者の入力作業が省かれるということです。</span><span class="sxs-lookup"><span data-stu-id="23763-1362">The best way to think about the role of ambient values is that they attempt to save application developers typing, in some common cases.</span></span> <span data-ttu-id="23763-1363">従来、アンビエント値が役に立つシナリオは MVC に関連しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1363">Traditionally, the scenarios where ambient values are helpful are related to MVC:</span></span>

* <span data-ttu-id="23763-1364">同じコントローラー内の別のアクションにリンクする場合、コントローラー名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1364">When linking to another action in the same controller, the controller name doesn't need to be specified.</span></span>
* <span data-ttu-id="23763-1365">同じ領域内の別のコントローラーにリンクする場合、領域名を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1365">When linking to another controller in the same area, the area name doesn't need to be specified.</span></span>
* <span data-ttu-id="23763-1366">同じアクション メソッドにリンクする場合は、ルート値を指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1366">When linking to the same action method, route values don't need to be specified.</span></span>
* <span data-ttu-id="23763-1367">アプリの別の部分にリンクする場合は、アプリのその部分には意味のないルート値は引き継ぎません。</span><span class="sxs-lookup"><span data-stu-id="23763-1367">When linking to another part of the app, you don't want to carry over route values that have no meaning in that part of the app.</span></span>

<span data-ttu-id="23763-1368">`null` を返す `LinkGenerator` または `IUrlHelper` の呼び出しは、通常、ルート値の無効化について理解していないことが原因で発生します。</span><span class="sxs-lookup"><span data-stu-id="23763-1368">Calls to `LinkGenerator` or `IUrlHelper` that return `null` are usually caused by not understanding route value invalidation.</span></span> <span data-ttu-id="23763-1369">ルート値の無効化のトラブルシューティングを行うには、さらにルート値を明示的に指定して、これにより問題が解決されるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="23763-1369">Troubleshoot route value invalidation by explicitly specifying more of the route values to see if that solves the problem.</span></span>

<span data-ttu-id="23763-1370">ルート値の無効化は、アプリの URL スキームが階層的であり、階層が左から右に形成されていることを前提として機能します。</span><span class="sxs-lookup"><span data-stu-id="23763-1370">Route value invalidation works on the assumption that the app's URL scheme is hierarchical, with a hierarchy formed from left-to-right.</span></span> <span data-ttu-id="23763-1371">基本的なコントローラー ルート テンプレート `{controller}/{action}/{id?}` について考えてみましょう。これが実際にどのように動作するかを直感的に理解できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1371">Consider the basic controller route template `{controller}/{action}/{id?}` to get an intuitive sense of how this works in practice.</span></span> <span data-ttu-id="23763-1372">値に対する**変更**により、右側に表示されるすべてのルート値が**無効化**されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1372">A **change** to a value **invalidates** all of the route values that appear to the right.</span></span> <span data-ttu-id="23763-1373">これには、階層に関する前提が反映されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1373">This reflects the assumption about hierarchy.</span></span> <span data-ttu-id="23763-1374">アプリに `id` のアンビエント値があり、操作によって `controller` に対して異なる値が指定された場合、</span><span class="sxs-lookup"><span data-stu-id="23763-1374">If the app has an ambient value for `id`, and the operation specifies a different value for the `controller`:</span></span>

* <span data-ttu-id="23763-1375">`{controller}` が `{id?}` の左側にあるため、`id` は再利用されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1375">`id` won't be reused because `{controller}` is to the left of `{id?}`.</span></span>

<span data-ttu-id="23763-1376">この原則を示すいくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="23763-1376">Some examples demonstrating this principle:</span></span>

* <span data-ttu-id="23763-1377">明示的な値に `id` の値が含まれている場合、`id` のアンビエント値は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1377">If the explicit values contain a value for `id`, the ambient value for `id` is ignored.</span></span> <span data-ttu-id="23763-1378">`controller` と `action` のアンビエント値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1378">The ambient values for `controller` and `action` can be used.</span></span>
* <span data-ttu-id="23763-1379">明示的な値に `action` の値が含まれている場合、`action` のアンビエント値はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1379">If the explicit values contain a value for `action`, any ambient value for `action` is ignored.</span></span> <span data-ttu-id="23763-1380">`controller` のアンビエント値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1380">The ambient values for `controller` can be used.</span></span> <span data-ttu-id="23763-1381">`action` の明示的な値が `action` のアンビエント値と異なる場合、`id` 値は使用されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1381">If the explicit value for `action` is different from the ambient value for `action`, the `id` value won't be used.</span></span>  <span data-ttu-id="23763-1382">`action` の明示的な値が `action` のアンビエント値と同じ場合、`id` 値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1382">If the explicit value for `action` is the same as the ambient value for `action`, the `id` value can be used.</span></span>
* <span data-ttu-id="23763-1383">明示的な値に `controller` の値が含まれている場合、`controller` のアンビエント値はすべて無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1383">If the explicit values contain a value for `controller`, any ambient value for `controller` is ignored.</span></span> <span data-ttu-id="23763-1384">`controller` の明示的な値が `controller` のアンビエント値と異なる場合、`action` と `id` の値は使用されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1384">If the explicit value for `controller` is different from the ambient value for `controller`, the `action` and `id` values won't be used.</span></span> <span data-ttu-id="23763-1385">`controller` の明示的な値が `controller` のアンビエント値と同じ場合、`action` と `id` の値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1385">If the explicit value for `controller` is the same as the ambient value for `controller`, the `action` and `id` values can be used.</span></span>

<span data-ttu-id="23763-1386">このプロセスは、属性ルートと専用規則ルートが存在することでさらに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="23763-1386">This process is further complicated by the existence of attribute routes and dedicated conventional routes.</span></span> <span data-ttu-id="23763-1387">`{controller}/{action}/{id?}` などのコントローラーの規則ルートでは、ルート パラメーターを使用して階層が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1387">Controller conventional routes such as `{controller}/{action}/{id?}` specify a hierarchy using route parameters.</span></span> <span data-ttu-id="23763-1388">コントローラーと Razor Pages に対する[専用規則ルート](xref:mvc/controllers/routing#dcr)と[属性ルート](xref:mvc/controllers/routing#ar)の場合、</span><span class="sxs-lookup"><span data-stu-id="23763-1388">For [dedicated conventional routes](xref:mvc/controllers/routing#dcr) and [attribute routes](xref:mvc/controllers/routing#ar) to controllers and Razor Pages:</span></span>

* <span data-ttu-id="23763-1389">ルート値の階層があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1389">There is a hierarchy of route values.</span></span>
* <span data-ttu-id="23763-1390">テンプレートには表示されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1390">They don't appear in the template.</span></span>

<span data-ttu-id="23763-1391">このような場合は、URL の生成によって**必要な値**の概念が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1391">For these cases, URL generation defines the **required values** concept.</span></span> <span data-ttu-id="23763-1392">コントローラーおよび Razor Pages によって作成されたエンドポイントには、ルート値の無効化を機能させるために必要な値が指定されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1392">Endpoints created by controllers and Razor Pages have required values specified that allow route value invalidation to work.</span></span>

<span data-ttu-id="23763-1393">ルート値の無効化アルゴリズムの詳細は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="23763-1393">The route value invalidation algorithm in detail:</span></span>

* <span data-ttu-id="23763-1394">必要な値の名前がルート パラメーターと組み合わされ、左から右に処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1394">The required value names are combined with the route parameters, then processed from left-to-right.</span></span>
* <span data-ttu-id="23763-1395">各パラメーターについて、アンビエント値と明示的な値が比較されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1395">For each parameter, the ambient value and explicit value are compared:</span></span>
    * <span data-ttu-id="23763-1396">アンビエント値と明示的な値が同じ場合、プロセスは続行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1396">If the ambient value and explicit value are the same, the process continues.</span></span>
    * <span data-ttu-id="23763-1397">アンビエント値が存在し、明示的な値が存在しない場合は、URL を生成するときにアンビエント値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1397">If the ambient value is present and the explicit value isn't, the ambient value is used when generating the URL.</span></span>
    * <span data-ttu-id="23763-1398">アンビエント値が存在せず、明示的な値が存在する場合は、そのアンビエント値とそれ以降のすべてのアンビエント値が拒否されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1398">If the ambient value isn't present and the explicit value is, reject the ambient value and all subsequent ambient values.</span></span>
    * <span data-ttu-id="23763-1399">アンビエント値と明示的な値が存在し、2 つの値が異なる場合は、そのアンビエント値とそれ以降のすべてのアンビエント値が拒否されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1399">If the ambient value and the explicit value are present, and the two values are different, reject the ambient value and all subsequent ambient values.</span></span>

<span data-ttu-id="23763-1400">この時点で、URL の生成操作はルート制約を評価する準備ができています。</span><span class="sxs-lookup"><span data-stu-id="23763-1400">At this point, the URL generation operation is ready to evaluate route constraints.</span></span> <span data-ttu-id="23763-1401">許容可能な値のセットがパラメーターの既定値と組み合わされ、制約に提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1401">The set of accepted values is combined with the parameter default values, which is provided to constraints.</span></span> <span data-ttu-id="23763-1402">すべての制約について合格した場合、操作が続行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1402">If the constraints all pass, the operation continues.</span></span>

<span data-ttu-id="23763-1403">次に、**許容可能な値**を使用してルート テンプレートを展開できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1403">Next, the **accepted values** can be used to expand the route template.</span></span> <span data-ttu-id="23763-1404">ルート テンプレートは次のように処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1404">The route template is processed:</span></span>

* <span data-ttu-id="23763-1405">左から右。</span><span class="sxs-lookup"><span data-stu-id="23763-1405">From left-to-right.</span></span>
* <span data-ttu-id="23763-1406">各パラメーターに、許容可能な値が代入されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1406">Each parameter has its accepted value substituted.</span></span>
* <span data-ttu-id="23763-1407">次の特殊なケースがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-1407">With the following special cases:</span></span>
  * <span data-ttu-id="23763-1408">許容可能な値がなく、パラメーターに既定値がある場合は、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1408">If the accepted values is missing a value and the parameter has a default value, the default value is used.</span></span>
  * <span data-ttu-id="23763-1409">許容可能な値がなく、パラメーターが省略可能な場合は、処理が続行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1409">If the accepted values is missing a value and the parameter is optional, processing continues.</span></span>
  * <span data-ttu-id="23763-1410">存在しない省略可能なパラメーターの右側にあるルート パラメーターのいずれかに値がある場合、操作は失敗します。</span><span class="sxs-lookup"><span data-stu-id="23763-1410">If any route parameter to the right of a missing optional parameter has a value, the operation fails.</span></span>
  * <!-- review default-valued parameters optional parameters --> <span data-ttu-id="23763-1411">連続する既定値パラメーターと省略可能なパラメーターは、可能な場合、折りたたまれています。</span><span class="sxs-lookup"><span data-stu-id="23763-1411">Contiguous default-valued parameters and optional parameters are collapsed where possible.</span></span>

<span data-ttu-id="23763-1412">ルートのセグメントと一致しない明示的に指定された値は、クエリ文字列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1412">Values explicitly provided that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="23763-1413">次の表は、ルート テンプレート `{controller}/{action}/{id?}` の使用時の結果をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-1413">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="23763-1414">アンビエント値</span><span class="sxs-lookup"><span data-stu-id="23763-1414">Ambient Values</span></span>                     | <span data-ttu-id="23763-1415">明示的な値</span><span class="sxs-lookup"><span data-stu-id="23763-1415">Explicit Values</span></span>                        | <span data-ttu-id="23763-1416">結果</span><span class="sxs-lookup"><span data-stu-id="23763-1416">Result</span></span>                  |
| ---
<span data-ttu-id="23763-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1418">'Blazor'</span></span>
- <span data-ttu-id="23763-1419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1419">'Identity'</span></span>
- <span data-ttu-id="23763-1420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1420">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1421">'Razor'</span></span>
- <span data-ttu-id="23763-1422">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1424">'Blazor'</span></span>
- <span data-ttu-id="23763-1425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1425">'Identity'</span></span>
- <span data-ttu-id="23763-1426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1426">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1427">'Razor'</span></span>
- <span data-ttu-id="23763-1428">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1430">'Blazor'</span></span>
- <span data-ttu-id="23763-1431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1431">'Identity'</span></span>
- <span data-ttu-id="23763-1432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1432">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1433">'Razor'</span></span>
- <span data-ttu-id="23763-1434">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1436">'Blazor'</span></span>
- <span data-ttu-id="23763-1437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1437">'Identity'</span></span>
- <span data-ttu-id="23763-1438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1438">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1439">'Razor'</span></span>
- <span data-ttu-id="23763-1440">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1442">'Blazor'</span></span>
- <span data-ttu-id="23763-1443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1443">'Identity'</span></span>
- <span data-ttu-id="23763-1444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1444">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1445">'Razor'</span></span>
- <span data-ttu-id="23763-1446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1448">'Blazor'</span></span>
- <span data-ttu-id="23763-1449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1449">'Identity'</span></span>
- <span data-ttu-id="23763-1450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1450">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1451">'Razor'</span></span>
- <span data-ttu-id="23763-1452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1454">'Blazor'</span></span>
- <span data-ttu-id="23763-1455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1455">'Identity'</span></span>
- <span data-ttu-id="23763-1456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1456">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1457">'Razor'</span></span>
- <span data-ttu-id="23763-1458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1460">'Blazor'</span></span>
- <span data-ttu-id="23763-1461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1461">'Identity'</span></span>
- <span data-ttu-id="23763-1462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1462">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1463">'Razor'</span></span>
- <span data-ttu-id="23763-1464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1466">'Blazor'</span></span>
- <span data-ttu-id="23763-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1467">'Identity'</span></span>
- <span data-ttu-id="23763-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1469">'Razor'</span></span>
- <span data-ttu-id="23763-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1472">'Blazor'</span></span>
- <span data-ttu-id="23763-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1473">'Identity'</span></span>
- <span data-ttu-id="23763-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1475">'Razor'</span></span>
- <span data-ttu-id="23763-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1478">'Blazor'</span></span>
- <span data-ttu-id="23763-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1479">'Identity'</span></span>
- <span data-ttu-id="23763-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1481">'Razor'</span></span>
- <span data-ttu-id="23763-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1484">'Blazor'</span></span>
- <span data-ttu-id="23763-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1485">'Identity'</span></span>
- <span data-ttu-id="23763-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1487">'Razor'</span></span>
- <span data-ttu-id="23763-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1490">'Blazor'</span></span>
- <span data-ttu-id="23763-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1491">'Identity'</span></span>
- <span data-ttu-id="23763-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1493">'Razor'</span></span>
- <span data-ttu-id="23763-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1496">'Blazor'</span></span>
- <span data-ttu-id="23763-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1497">'Identity'</span></span>
- <span data-ttu-id="23763-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1499">'Razor'</span></span>
- <span data-ttu-id="23763-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1502">'Blazor'</span></span>
- <span data-ttu-id="23763-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1503">'Identity'</span></span>
- <span data-ttu-id="23763-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1505">'Razor'</span></span>
- <span data-ttu-id="23763-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1506">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1507">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1508">'Blazor'</span></span>
- <span data-ttu-id="23763-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1509">'Identity'</span></span>
- <span data-ttu-id="23763-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1511">'Razor'</span></span>
- <span data-ttu-id="23763-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1514">'Blazor'</span></span>
- <span data-ttu-id="23763-1515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1515">'Identity'</span></span>
- <span data-ttu-id="23763-1516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1516">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1517">'Razor'</span></span>
- <span data-ttu-id="23763-1518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1520">'Blazor'</span></span>
- <span data-ttu-id="23763-1521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1521">'Identity'</span></span>
- <span data-ttu-id="23763-1522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1522">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1523">'Razor'</span></span>
- <span data-ttu-id="23763-1524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1526">'Blazor'</span></span>
- <span data-ttu-id="23763-1527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1527">'Identity'</span></span>
- <span data-ttu-id="23763-1528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1528">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1529">'Razor'</span></span>
- <span data-ttu-id="23763-1530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1532">'Blazor'</span></span>
- <span data-ttu-id="23763-1533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1533">'Identity'</span></span>
- <span data-ttu-id="23763-1534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1534">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1535">'Razor'</span></span>
- <span data-ttu-id="23763-1536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1538">'Blazor'</span></span>
- <span data-ttu-id="23763-1539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1539">'Identity'</span></span>
- <span data-ttu-id="23763-1540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1540">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1541">'Razor'</span></span>
- <span data-ttu-id="23763-1542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1544">'Blazor'</span></span>
- <span data-ttu-id="23763-1545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1545">'Identity'</span></span>
- <span data-ttu-id="23763-1546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1546">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1547">'Razor'</span></span>
- <span data-ttu-id="23763-1548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1550">'Blazor'</span></span>
- <span data-ttu-id="23763-1551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1551">'Identity'</span></span>
- <span data-ttu-id="23763-1552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1552">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1553">'Razor'</span></span>
- <span data-ttu-id="23763-1554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1556">'Blazor'</span></span>
- <span data-ttu-id="23763-1557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1557">'Identity'</span></span>
- <span data-ttu-id="23763-1558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1558">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1559">'Razor'</span></span>
- <span data-ttu-id="23763-1560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1562">'Blazor'</span></span>
- <span data-ttu-id="23763-1563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1563">'Identity'</span></span>
- <span data-ttu-id="23763-1564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1564">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1565">'Razor'</span></span>
- <span data-ttu-id="23763-1566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1568">'Blazor'</span></span>
- <span data-ttu-id="23763-1569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1569">'Identity'</span></span>
- <span data-ttu-id="23763-1570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1570">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1571">'Razor'</span></span>
- <span data-ttu-id="23763-1572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1574">'Blazor'</span></span>
- <span data-ttu-id="23763-1575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1575">'Identity'</span></span>
- <span data-ttu-id="23763-1576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1576">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1577">'Razor'</span></span>
- <span data-ttu-id="23763-1578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1580">'Blazor'</span></span>
- <span data-ttu-id="23763-1581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1581">'Identity'</span></span>
- <span data-ttu-id="23763-1582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1582">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1583">'Razor'</span></span>
- <span data-ttu-id="23763-1584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1586">'Blazor'</span></span>
- <span data-ttu-id="23763-1587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1587">'Identity'</span></span>
- <span data-ttu-id="23763-1588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1588">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1589">'Razor'</span></span>
- <span data-ttu-id="23763-1590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1592">'Blazor'</span></span>
- <span data-ttu-id="23763-1593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1593">'Identity'</span></span>
- <span data-ttu-id="23763-1594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1594">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1595">'Razor'</span></span>
- <span data-ttu-id="23763-1596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1598">'Blazor'</span></span>
- <span data-ttu-id="23763-1599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1599">'Identity'</span></span>
- <span data-ttu-id="23763-1600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1600">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1601">'Razor'</span></span>
- <span data-ttu-id="23763-1602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1604">'Blazor'</span></span>
- <span data-ttu-id="23763-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1605">'Identity'</span></span>
- <span data-ttu-id="23763-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1607">'Razor'</span></span>
- <span data-ttu-id="23763-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1608">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1609">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1610">'Blazor'</span></span>
- <span data-ttu-id="23763-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1611">'Identity'</span></span>
- <span data-ttu-id="23763-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1613">'Razor'</span></span>
- <span data-ttu-id="23763-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1616">'Blazor'</span></span>
- <span data-ttu-id="23763-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1617">'Identity'</span></span>
- <span data-ttu-id="23763-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1619">'Razor'</span></span>
- <span data-ttu-id="23763-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1622">'Blazor'</span></span>
- <span data-ttu-id="23763-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1623">'Identity'</span></span>
- <span data-ttu-id="23763-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1625">'Razor'</span></span>
- <span data-ttu-id="23763-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1628">'Blazor'</span></span>
- <span data-ttu-id="23763-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1629">'Identity'</span></span>
- <span data-ttu-id="23763-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1631">'Razor'</span></span>
- <span data-ttu-id="23763-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1634">'Blazor'</span></span>
- <span data-ttu-id="23763-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1635">'Identity'</span></span>
- <span data-ttu-id="23763-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1637">'Razor'</span></span>
- <span data-ttu-id="23763-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1640">'Blazor'</span></span>
- <span data-ttu-id="23763-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1641">'Identity'</span></span>
- <span data-ttu-id="23763-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1643">'Razor'</span></span>
- <span data-ttu-id="23763-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1646">'Blazor'</span></span>
- <span data-ttu-id="23763-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1647">'Identity'</span></span>
- <span data-ttu-id="23763-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1649">'Razor'</span></span>
- <span data-ttu-id="23763-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1652">'Blazor'</span></span>
- <span data-ttu-id="23763-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1653">'Identity'</span></span>
- <span data-ttu-id="23763-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1655">'Razor'</span></span>
- <span data-ttu-id="23763-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1658">'Blazor'</span></span>
- <span data-ttu-id="23763-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1659">'Identity'</span></span>
- <span data-ttu-id="23763-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1661">'Razor'</span></span>
- <span data-ttu-id="23763-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1663">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order"、action = "About" | `/Order/About`          |
| controller = "Home"、color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About"、color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="23763-1663">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

### <a name="problems-with-route-value-invalidation"></a><span data-ttu-id="23763-1664">ルート値の無効化に関する問題</span><span class="sxs-lookup"><span data-stu-id="23763-1664">Problems with route value invalidation</span></span>

<span data-ttu-id="23763-1665">ASP.NET Core 3.0 の時点では、以前の ASP.NET Core バージョンで使用されている一部の URL 生成スキームが URL の生成においてうまく機能しません。</span><span class="sxs-lookup"><span data-stu-id="23763-1665">As of ASP.NET Core 3.0, some URL generation schemes used in earlier ASP.NET Core versions don't work well with URL generation.</span></span> <span data-ttu-id="23763-1666">ASP.NET Core チームは、今後のリリースでこれらのニーズに対応する機能を追加することを計画しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1666">The ASP.NET Core team plans to add features to address these needs in a future release.</span></span> <span data-ttu-id="23763-1667">当面は、従来のルーティングを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23763-1667">For now the best solution is to use legacy routing.</span></span>

<span data-ttu-id="23763-1668">次のコードは、ルーティングでサポートされていない URL 生成スキームの例を示しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1668">The following code shows an example of a URL generation scheme that's not supported by routing.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

<span data-ttu-id="23763-1669">上記のコードでは、`culture` ルート パラメーターがローカライズに使用されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1669">In the preceding code, the `culture` route parameter is used for localization.</span></span> <span data-ttu-id="23763-1670">`culture` パラメーターを常にアンビエント値として許容されるようにすることが望まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-1670">The desire is to have the `culture` parameter always accepted as an ambient value.</span></span> <span data-ttu-id="23763-1671">しかし、必要な値の動作方法が理由で、`culture` パラメーターはアンビエント値として許容されません。</span><span class="sxs-lookup"><span data-stu-id="23763-1671">However, the `culture` parameter is not accepted as an ambient value because of the way required values work:</span></span>

* <span data-ttu-id="23763-1672">`"default"` ルート テンプレートでは、`culture` ルート パラメーターは `controller` の左側にあるため、`controller` を変更しても `culture` は無効になりません。</span><span class="sxs-lookup"><span data-stu-id="23763-1672">In the `"default"` route template, the `culture` route parameter is to the left of `controller`, so changes to `controller` won't invalidate `culture`.</span></span>
* <span data-ttu-id="23763-1673">`"blog"` ルート テンプレートでは、`culture` ルート パラメーターは `controller` の右側にあると見なされ、必要な値に表示されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1673">In the `"blog"` route template, the `culture` route parameter is considered to be to the right of `controller`, which appears in the required values.</span></span>

## <a name="configuring-endpoint-metadata"></a><span data-ttu-id="23763-1674">エンドポイント メタデータの構成</span><span class="sxs-lookup"><span data-stu-id="23763-1674">Configuring endpoint metadata</span></span>

<span data-ttu-id="23763-1675">次のリンクでは、エンドポイント メタデータの構成に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1675">The following links provide information on configuring endpoint metadata:</span></span>

* [<span data-ttu-id="23763-1676">エンドポイント ルーティングで CORS を有効にする</span><span class="sxs-lookup"><span data-stu-id="23763-1676">Enable Cors with endpoint routing</span></span>](xref:security/cors#enable-cors-with-endpoint-routing)
* <span data-ttu-id="23763-1677">カスタム `[MinimumAgeAuthorize]` 属性を使用する [IAuthorizationPolicyProvider のサンプル](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)</span><span class="sxs-lookup"><span data-stu-id="23763-1677">[IAuthorizationPolicyProvider sample](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) using a custom `[MinimumAgeAuthorize]` attribute</span></span>
* <span data-ttu-id="23763-1678">[[Authorize] 属性を使用して認証をテストする](xref:security/authentication/identity#test-identity)</span><span class="sxs-lookup"><span data-stu-id="23763-1678">[Test authentication with the [Authorize] attribute](xref:security/authentication/identity#test-identity)</span></span>
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* <span data-ttu-id="23763-1679">[[Authorize] 属性を使用してスキームを選択する](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span><span class="sxs-lookup"><span data-stu-id="23763-1679">[Selecting the scheme with the [Authorize] attribute](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)</span></span>
* <span data-ttu-id="23763-1680">[[Authorize] 属性を使用してポリシーを適用する](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span><span class="sxs-lookup"><span data-stu-id="23763-1680">[Applying policies using the [Authorize] attribute](xref:security/authorization/policies#applying-policies-to-mvc-controllers)</span></span>
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a><span data-ttu-id="23763-1681">RequireHost とルートが一致するホスト</span><span class="sxs-lookup"><span data-stu-id="23763-1681">Host matching in routes with RequireHost</span></span>

<span data-ttu-id="23763-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> では、指定したホストが必要であるという制約がルートに適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1682"><xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> applies a constraint to the route which requires the specified host.</span></span> <span data-ttu-id="23763-1683">`RequireHost` または [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) パラメーターには、次のいずれかを指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1683">The `RequireHost` or [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute) parameter can be:</span></span>

* <span data-ttu-id="23763-1684">ホスト: `www.domain.com`。任意のポートの `www.domain.com` と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1684">Host: `www.domain.com`, matches `www.domain.com` with any port.</span></span>
* <span data-ttu-id="23763-1685">ホストとワイルドカード: `*.domain.com`。任意のポートの `www.domain.com`、`subdomain.domain.com`、または `www.subdomain.domain.com` と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1685">Host with wildcard: `*.domain.com`, matches `www.domain.com`, `subdomain.domain.com`, or `www.subdomain.domain.com` on any port.</span></span>
* <span data-ttu-id="23763-1686">ポート: `*:5000`。任意のホストのポート 5000 と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1686">Port: `*:5000`, matches port 5000 with any host.</span></span>
* <span data-ttu-id="23763-1687">ホストとポート: `www.domain.com:5000` または `*.domain.com:5000`。ホストとポートと一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1687">Host and port: `www.domain.com:5000` or `*.domain.com:5000`, matches host and port.</span></span>

<span data-ttu-id="23763-1688">`RequireHost` または `[Host]` を使用して、複数のパラメーターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1688">Multiple parameters can be specified using `RequireHost` or `[Host]`.</span></span> <span data-ttu-id="23763-1689">制約は、いずれかのパラメーターに対して有効なホストと一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1689">The constraint  matches hosts valid for any of the parameters.</span></span> <span data-ttu-id="23763-1690">たとえば、`[Host("domain.com", "*.domain.com")]` は `domain.com`、`www.domain.com`、および `subdomain.domain.com` と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-1690">For example, `[Host("domain.com", "*.domain.com")]` matches `domain.com`, `www.domain.com`, and `subdomain.domain.com`.</span></span>

<span data-ttu-id="23763-1691">次のコードでは、`RequireHost` を使用して、指定したホストをルートに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="23763-1691">The following code uses `RequireHost` to require the specified host on the route:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

<span data-ttu-id="23763-1692">次のコードでは、コントローラー上の `[Host]` 属性を使用して、指定したホストのいずれかを要求します。</span><span class="sxs-lookup"><span data-stu-id="23763-1692">The following code uses the `[Host]` attribute on the controller to require any of the specified hosts:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

<span data-ttu-id="23763-1693">`[Host]` 属性がコントローラーとアクション メソッドの両方に適用される場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1693">When the `[Host]` attribute is applied to both the controller and action method:</span></span>

* <span data-ttu-id="23763-1694">アクションの属性が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1694">The attribute on the action is used.</span></span>
* <span data-ttu-id="23763-1695">コントローラーの属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1695">The controller attribute is ignored.</span></span>

## <a name="performance-guidance-for-routing"></a><span data-ttu-id="23763-1696">ルーティングに関するパフォーマンス ガイダンス</span><span class="sxs-lookup"><span data-stu-id="23763-1696">Performance guidance for routing</span></span>

<span data-ttu-id="23763-1697">ASP.NET Core 3.0 では、ほとんどのルーティングが更新され、パフォーマンスが向上しました。</span><span class="sxs-lookup"><span data-stu-id="23763-1697">Most of routing was updated in ASP.NET Core 3.0 to increase performance.</span></span>

<span data-ttu-id="23763-1698">アプリにパフォーマンス上の問題がある場合、多くの場合ルーティングが問題として疑われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1698">When an app has performance problems, routing is often suspected as the problem.</span></span> <span data-ttu-id="23763-1699">ルーティングが疑われる理由は、コントローラーや Razor Pages などのフレームワークにより、フレームワーク内で費やされた時間がログ メッセージで報告されるためです。</span><span class="sxs-lookup"><span data-stu-id="23763-1699">The reason routing is suspected is that frameworks like controllers and Razor Pages report the amount of time spent inside the framework in their logging messages.</span></span> <span data-ttu-id="23763-1700">コントローラーによって報告された時間と要求の合計時間の間に大きな違いがある場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1700">When there's a significant difference between the time reported by controllers and the total time of the request:</span></span>

* <span data-ttu-id="23763-1701">開発者は、問題の発生源としてアプリ コードを排除します。</span><span class="sxs-lookup"><span data-stu-id="23763-1701">Developers eliminate their app code as the source of the problem.</span></span>
* <span data-ttu-id="23763-1702">ルーティングが原因であると考えるのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="23763-1702">It's common to assume routing is the cause.</span></span>

<span data-ttu-id="23763-1703">ルーティングは、数千のエンドポイントを使用してパフォーマンス テストされています。</span><span class="sxs-lookup"><span data-stu-id="23763-1703">Routing is performance tested using thousands of endpoints.</span></span> <span data-ttu-id="23763-1704">一般的なアプリでは、大きすぎるだけでパフォーマンスの問題が発生する可能性はほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1704">It's unlikely that a typical app will encounter a performance problem just by being too large.</span></span> <span data-ttu-id="23763-1705">ルーティングのパフォーマンス低下の最も一般的な根本原因は、通常、正しく動作していないカスタム ミドルウェアです。</span><span class="sxs-lookup"><span data-stu-id="23763-1705">The most common root cause of slow routing performance is usually a badly-behaving custom middleware.</span></span>

<span data-ttu-id="23763-1706">次のコード サンプルは、遅延の原因を絞り込むための基本的な手法を示したものです。</span><span class="sxs-lookup"><span data-stu-id="23763-1706">This following code sample demonstrates a basic technique for narrowing down the source of delay:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

<span data-ttu-id="23763-1707">ルーティングの時間は次のように計ります。</span><span class="sxs-lookup"><span data-stu-id="23763-1707">To time routing:</span></span>

* <span data-ttu-id="23763-1708">各ミドルウェアを、上記のコードに示されている時間を計るミドルウェアのコピーでインターリーブします。</span><span class="sxs-lookup"><span data-stu-id="23763-1708">Interleave each middleware with a copy of the timing middleware shown in the preceding code.</span></span>
* <span data-ttu-id="23763-1709">計られた時間データをコードと関連付けるための一意の識別子を追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-1709">Add a unique identifier to correlate the timing data with the code.</span></span>

<span data-ttu-id="23763-1710">これは、遅延が `10ms` を超えるなど顕著な場合に絞り込むための基本的な方法です。</span><span class="sxs-lookup"><span data-stu-id="23763-1710">This is a basic way to narrow down the delay when it's significant, for example, more than `10ms`.</span></span>  <span data-ttu-id="23763-1711">`Time 1` から `Time 2` を引くことで、`UseRouting` ミドルウェア内で費やされた時間を報告します。</span><span class="sxs-lookup"><span data-stu-id="23763-1711">Subtracting `Time 2` from `Time 1` reports the time spent inside the `UseRouting` middleware.</span></span>

<span data-ttu-id="23763-1712">次のコードでは、前の時間を計るコードに対して、よりコンパクトなアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-1712">The following code uses a more compact approach to the preceding timing code:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a><span data-ttu-id="23763-1713">潜在的にコストが高いルーティング機能</span><span class="sxs-lookup"><span data-stu-id="23763-1713">Potentially expensive routing features</span></span>

<span data-ttu-id="23763-1714">次の一覧に、基本的なルート テンプレートと比べて比較的コストが高いルーティング機能についての洞察を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-1714">The following list provides some insight into routing features that are relatively expensive compared with basic route templates:</span></span>

* <span data-ttu-id="23763-1715">正規表現: 複雑な正規表現を作成すること、つまり少量の入力で長い実行時間を実現することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1715">Regular expressions: It's possible to write regular expressions that are complex, or have long running time with a small amount of input.</span></span>

* <span data-ttu-id="23763-1716">複雑なセグメント (`{x}-{y}-{z}`):</span><span class="sxs-lookup"><span data-stu-id="23763-1716">Complex segments (`{x}-{y}-{z}`):</span></span> 
  * <span data-ttu-id="23763-1717">通常の URL パス セグメントを解析するよりもかなりコストがかかります。</span><span class="sxs-lookup"><span data-stu-id="23763-1717">Are significantly more expensive than parsing a regular URL path segment.</span></span>
  * <span data-ttu-id="23763-1718">より多くの部分文字列が割り当てられることになります。</span><span class="sxs-lookup"><span data-stu-id="23763-1718">Result in many more substrings being allocated.</span></span>
  * <span data-ttu-id="23763-1719">ASP.NET Core 3.0 ルーティング パフォーマンスの更新では、複雑なセグメントのロジックは更新されませんでした。</span><span class="sxs-lookup"><span data-stu-id="23763-1719">The complex segment logic was not updated in ASP.NET Core 3.0 routing performance update.</span></span>

* <span data-ttu-id="23763-1720">同期データ アクセス: 多くの複雑なアプリでは、ルーティングの一部としてデータベースにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="23763-1720">Synchronous data access: Many complex apps have database access as part of their routing.</span></span> <span data-ttu-id="23763-1721">ASP.NET Core 2.2 以前のルーティングでは、データベース アクセス ルーティングをサポートするための適切な拡張ポイントが提供されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1721">ASP.NET Core 2.2 and earlier routing might not provide the right extensibility points to support database access routing.</span></span> <span data-ttu-id="23763-1722">たとえば、<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> と <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> は同期的です。</span><span class="sxs-lookup"><span data-stu-id="23763-1722">For example, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, and <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are synchronous.</span></span> <span data-ttu-id="23763-1723"><xref:Microsoft.AspNetCore.Routing.MatcherPolicy> や <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> などの拡張ポイントは非同期的です。</span><span class="sxs-lookup"><span data-stu-id="23763-1723">Extensibility points such as <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> and <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> are asynchronous.</span></span>

## <a name="guidance-for-library-authors"></a><span data-ttu-id="23763-1724">ライブラリ作成者向けのガイダンス</span><span class="sxs-lookup"><span data-stu-id="23763-1724">Guidance for library authors</span></span>

<span data-ttu-id="23763-1725">このセクションでは、ルーティングを基盤とするライブラリ作成者向けのガイダンスを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-1725">This section contains guidance for library authors building on top of routing.</span></span> <span data-ttu-id="23763-1726">これらの詳細情報は、アプリの開発者が、ルーティングを拡張するライブラリとフレームワークを使用して優れたエクスペリエンスを実現できるようにすることを目的としています。</span><span class="sxs-lookup"><span data-stu-id="23763-1726">These details are intended to ensure that app developers have a good experience using libraries and frameworks that extend routing.</span></span>

### <a name="define-endpoints"></a><span data-ttu-id="23763-1727">エンドポイントを定義する</span><span class="sxs-lookup"><span data-stu-id="23763-1727">Define endpoints</span></span>

<span data-ttu-id="23763-1728">URL 照合にルーティングを使用するフレームワークを作成するには、まず <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> の上に構築されるユーザー エクスペリエンスを定義します。</span><span class="sxs-lookup"><span data-stu-id="23763-1728">To create a framework that uses routing for URL matching, start by defining a user experience that builds on top of <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span>

<span data-ttu-id="23763-1729"><xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> の上に**構築します**。</span><span class="sxs-lookup"><span data-stu-id="23763-1729">**DO** build on top of <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>.</span></span> <span data-ttu-id="23763-1730">これにより、ユーザーは、他の ASP.NET Core 機能と混同せずにフレームワークを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1730">This allows users to compose your framework with other ASP.NET Core features without confusion.</span></span> <span data-ttu-id="23763-1731">すべての ASP.NET Core テンプレートには、ルーティングが含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-1731">Every ASP.NET Core template includes routing.</span></span> <span data-ttu-id="23763-1732">ルーティングが存在し、ユーザーになじみのあるものとします。</span><span class="sxs-lookup"><span data-stu-id="23763-1732">Assume routing is present and familiar for users.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="23763-1733"><xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> を実装する `MapMyFramework(...)` の呼び出しから、シールドの具象型を**返します**。</span><span class="sxs-lookup"><span data-stu-id="23763-1733">**DO** return a sealed concrete type from a call to `MapMyFramework(...)` that implements <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>.</span></span> <span data-ttu-id="23763-1734">ほとんどのフレームワーク `Map...` メソッドは、このパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="23763-1734">Most framework `Map...` methods follow this pattern.</span></span> <span data-ttu-id="23763-1735">`IEndpointConventionBuilder` インターフェイスは、</span><span class="sxs-lookup"><span data-stu-id="23763-1735">The `IEndpointConventionBuilder` interface:</span></span>

* <span data-ttu-id="23763-1736">メタデータの構成可能性を許可します。</span><span class="sxs-lookup"><span data-stu-id="23763-1736">Allows composability of metadata.</span></span>
* <span data-ttu-id="23763-1737">さまざまな拡張メソッドの対象とされています。</span><span class="sxs-lookup"><span data-stu-id="23763-1737">Is targeted by a variety of extension methods.</span></span>

<span data-ttu-id="23763-1738">独自の型を宣言すると、独自のフレームワーク固有の機能をビルダーに追加できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1738">Declaring your own type allows you to add your own framework-specific functionality to the builder.</span></span> <span data-ttu-id="23763-1739">フレームワークで宣言されたビルダーをラップし、呼び出しを転送するのは問題ありません。</span><span class="sxs-lookup"><span data-stu-id="23763-1739">It's ok to wrap a framework-declared builder and forward calls to it.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

<span data-ttu-id="23763-1740">独自の <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> を作成することを**検討します**。</span><span class="sxs-lookup"><span data-stu-id="23763-1740">**CONSIDER** writing your own <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>.</span></span> <span data-ttu-id="23763-1741">`EndpointDataSource` は、エンドポイントのコレクションを宣言および更新するための低レベルのプリミティブです。</span><span class="sxs-lookup"><span data-stu-id="23763-1741">`EndpointDataSource` is the low-level primitive for declaring and updating a collection of endpoints.</span></span> <span data-ttu-id="23763-1742">`EndpointDataSource` は、コントローラーと Razor Pages によって使用される強力な API です。</span><span class="sxs-lookup"><span data-stu-id="23763-1742">`EndpointDataSource` is a powerful API used by controllers and Razor Pages.</span></span>

<span data-ttu-id="23763-1743">ルーティング テストには、更新されていないデータ ソースの[基本的な例](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17)があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1743">The routing tests have a [basic example](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) of a non-updating data source.</span></span>

<span data-ttu-id="23763-1744">既定では、`EndpointDataSource` の登録を**試行しないでください**。</span><span class="sxs-lookup"><span data-stu-id="23763-1744">**DO NOT** attempt to register an `EndpointDataSource` by default.</span></span> <span data-ttu-id="23763-1745"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> にフレームワークを登録するようユーザーに要求してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1745">Require users to register your framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.</span></span> <span data-ttu-id="23763-1746">ルーティングの原理では、既定では何も含まれておらず、`UseEndpoints` がエンドポイントを登録する場所です。</span><span class="sxs-lookup"><span data-stu-id="23763-1746">The philosophy of routing is that nothing is included by default, and that `UseEndpoints` is the place to register endpoints.</span></span>

### <a name="creating-routing-integrated-middleware"></a><span data-ttu-id="23763-1747">ルーティング統合ミドルウェアを作成する</span><span class="sxs-lookup"><span data-stu-id="23763-1747">Creating routing-integrated middleware</span></span>

<span data-ttu-id="23763-1748">メタデータ型をインターフェイスとして定義することを**検討します**。</span><span class="sxs-lookup"><span data-stu-id="23763-1748">**CONSIDER** defining metadata types as an interface.</span></span>

<span data-ttu-id="23763-1749">クラスおよびメソッドの属性としてメタデータ型を**使用できるようにします**。</span><span class="sxs-lookup"><span data-stu-id="23763-1749">**DO** make it possible to use metadata types as an attribute on classes and methods.</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

<span data-ttu-id="23763-1750">コントローラーや Razor Pages などのフレームワークでは、型およびメソッドへのメタデータ属性の適用がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="23763-1750">Frameworks like controllers and Razor Pages support applying metadata attributes to types and methods.</span></span> <span data-ttu-id="23763-1751">メタデータ型を宣言する場合:</span><span class="sxs-lookup"><span data-stu-id="23763-1751">If you declare metadata types:</span></span>

* <span data-ttu-id="23763-1752">[属性](/dotnet/csharp/programming-guide/concepts/attributes/)としてアクセスできるようにします。</span><span class="sxs-lookup"><span data-stu-id="23763-1752">Make them accessible as [attributes](/dotnet/csharp/programming-guide/concepts/attributes/).</span></span>
* <span data-ttu-id="23763-1753">ほとんどのユーザーが属性の適用に精通しています。</span><span class="sxs-lookup"><span data-stu-id="23763-1753">Most users are familiar with applying attributes.</span></span>

<span data-ttu-id="23763-1754">メタデータ型をインターフェイスとして宣言すると、柔軟性の高いレイヤーがさらに追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1754">Declaring a metadata type as an interface adds another layer of flexibility:</span></span>

* <span data-ttu-id="23763-1755">インターフェイスが構成可能です。</span><span class="sxs-lookup"><span data-stu-id="23763-1755">Interfaces are composable.</span></span>
* <span data-ttu-id="23763-1756">開発者は、複数のポリシーを結合する独自の型を宣言できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1756">Developers can declare their own types that combine multiple policies.</span></span>

<span data-ttu-id="23763-1757">次の例に示すように、メタデータを**オーバーライドできるようにします**。</span><span class="sxs-lookup"><span data-stu-id="23763-1757">**DO** make it possible to override metadata, as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

<span data-ttu-id="23763-1758">これらのガイドラインに従う最善の方法は、**マーカーのメタデータ**を定義しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="23763-1758">The best way to follow these guidelines is to avoid defining **marker metadata**:</span></span>

* <span data-ttu-id="23763-1759">メタデータ型が存在するかどうかを確認するだけで終わらせません。</span><span class="sxs-lookup"><span data-stu-id="23763-1759">Don't just look for the presence of a metadata type.</span></span>
* <span data-ttu-id="23763-1760">メタデータのプロパティを定義し、プロパティを確認します。</span><span class="sxs-lookup"><span data-stu-id="23763-1760">Define a property on the metadata and check the property.</span></span>

<span data-ttu-id="23763-1761">メタデータ コレクションが順序付けされ、優先順位によるオーバーライドがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-1761">The metadata collection is ordered and supports overriding by priority.</span></span> <span data-ttu-id="23763-1762">コントローラーの場合、アクション メソッドのメタデータが最も限定的です。</span><span class="sxs-lookup"><span data-stu-id="23763-1762">In the case of controllers, metadata on the action method is most specific.</span></span>

<span data-ttu-id="23763-1763">ルーティングを使用する場合もしない場合もミドルウェアが**役立つようにします**。</span><span class="sxs-lookup"><span data-stu-id="23763-1763">**DO** make middleware useful with and without routing.</span></span>

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

<span data-ttu-id="23763-1764">このガイドラインの例として、`UseAuthorization` ミドルウェアを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="23763-1764">As an example of this guideline, consider the `UseAuthorization` middleware.</span></span> <span data-ttu-id="23763-1765">この承認ミドルウェアを使用すると、フォールバック ポリシーを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1765">The authorization middleware allows you to pass in a fallback policy.</span></span> <!-- shown where?  (shown here) --> <span data-ttu-id="23763-1766">フォールバック ポリシーは、指定されている場合、次の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1766">The fallback policy, if specified, applies to both:</span></span>

* <span data-ttu-id="23763-1767">指定されたポリシーのないエンドポイント。</span><span class="sxs-lookup"><span data-stu-id="23763-1767">Endpoints without a specified policy.</span></span>
* <span data-ttu-id="23763-1768">エンドポイントに一致しない要求。</span><span class="sxs-lookup"><span data-stu-id="23763-1768">Requests that don't match an endpoint.</span></span>

<span data-ttu-id="23763-1769">これにより、承認ミドルウェアはルーティングのコンテキストの外部で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="23763-1769">This makes the authorization middleware useful outside of the context of routing.</span></span> <span data-ttu-id="23763-1770">承認ミドルウェアは、従来のミドルウェア プログラミングに使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1770">The authorization middleware can be used for traditional middleware programming.</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="23763-1771">ルーティングでは、要求 URI をエンドポイントにマッピングし、受信要求をそれらのエンドポイントに配布します。</span><span class="sxs-lookup"><span data-stu-id="23763-1771">Routing is responsible for mapping request URIs to endpoints and dispatching incoming requests to those endpoints.</span></span> <span data-ttu-id="23763-1772">ルートはアプリに定義され、アプリの起動時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1772">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="23763-1773">ルートは、要求に含まれている URL から値を任意で抽出できます。その値を要求処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1773">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="23763-1774">ルーティングでは、アプリからのルート情報を利用し、エンドポイントにマッピングする URL を生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="23763-1774">Using route information from the app, routing is also able to generate URLs that map to endpoints.</span></span>

<span data-ttu-id="23763-1775">ASP.NET Core 2.2 で最新のルーティング シナリオを使用するには、`Startup.ConfigureServices` で[互換性バージョン](xref:mvc/compatibility-version)を MVC サービス登録に指定します。</span><span class="sxs-lookup"><span data-stu-id="23763-1775">To use the latest routing scenarios in ASP.NET Core 2.2, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="23763-1776"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> オプションでは、ルーティングで内部的にエンドポイント ベースのロジックを使用するか、ASP.NET Core 2.1 以前の <xref:Microsoft.AspNetCore.Routing.IRouter> ベースのロジックを使用する必要があるかどうかを決定します。</span><span class="sxs-lookup"><span data-stu-id="23763-1776">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> option determines if routing should internally use endpoint-based logic or the <xref:Microsoft.AspNetCore.Routing.IRouter>-based logic of ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="23763-1777">互換性バージョンが 2.2 以降に設定されている場合、既定値は `true` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-1777">When the compatibility version is set to 2.2 or later, the default value is `true`.</span></span> <span data-ttu-id="23763-1778">以前のルーティング ロジックを使用する場合は、値を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="23763-1778">Set the value to `false` to use the prior routing logic:</span></span>

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="23763-1779"><xref:Microsoft.AspNetCore.Routing.IRouter> ベースのルーティングの詳細については、[このトピックの ASP.NET Core 2.1 バージョン](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1779">For more information on <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, see the [ASP.NET Core 2.1 version of this topic](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="23763-1780">本文では、ASP.NET Core ルーティングについて詳しく取り上げます。</span><span class="sxs-lookup"><span data-stu-id="23763-1780">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="23763-1781">ASP.NET Core MVC ルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1781">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="23763-1782">Razor Pages のルーティング規則については、「<xref:razor-pages/razor-pages-conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1782">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="23763-1783">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="23763-1783">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="23763-1784">ルーティングの基本</span><span class="sxs-lookup"><span data-stu-id="23763-1784">Routing basics</span></span>

<span data-ttu-id="23763-1785">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1785">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="23763-1786">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="23763-1786">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="23763-1787">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="23763-1787">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="23763-1788">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="23763-1788">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="23763-1789">開発者は一般的に、特殊な状況でのアプリのトラフィックが多い領域に対しては、[属性ルーティング](xref:mvc/controllers/routing#attribute-routing)または専用規則ルートを使用して簡易ルートをさらに追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-1789">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span> <span data-ttu-id="23763-1790">特殊な状況の例として、ブログや eコマース エンドポイントがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-1790">Specialized situations examples include, blog and ecommerce endpoints.</span></span>

<span data-ttu-id="23763-1791">Web API では、属性ルーティングを使用して、HTTP 動詞で操作を表現するリソースのセットとしてアプリの機能をモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1791">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="23763-1792">つまり、同じ論理リソース上の多くの操作 (たとえば GET や POST) で、同じ URL が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1792">This means that many operations, for example, GET, and POST, on the same logical resource use the same URL.</span></span> <span data-ttu-id="23763-1793">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1793">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="23763-1794"> Pages アプリでは、既定の規則ルーティングを使用して、アプリの *Pages* フォルダーの名前付きリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1794"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="23763-1795">Razor Pages のルーティング動作をカスタマイズできる追加の規則を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1795">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="23763-1796">詳細については、次のトピックを参照してください。 <xref:razor-pages/index> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="23763-1796">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="23763-1797">URL 生成サポートを使用すると、アプリを相互にリンクする URL をハード コーディングすることなくアプリを開発できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1797">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="23763-1798">このサポートにより、基本的なルーティング構成を使用して作業を開始し、アプリのリソース レイアウトが決まった後でルートを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1798">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="23763-1799">ルーティングでは*エンドポイント* (`Endpoint`) を使用して、アプリの論理エンドポイントを表します。</span><span class="sxs-lookup"><span data-stu-id="23763-1799">Routing uses *endpoints* (`Endpoint`) to represent logical endpoints in an app.</span></span>

<span data-ttu-id="23763-1800">エンドポイントでは、要求と、任意のメタデータのコレクションを処理するデリゲートが定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1800">An endpoint defines a delegate to process requests and a collection of arbitrary metadata.</span></span> <span data-ttu-id="23763-1801">メタデータは、各エンドポイントにアタッチされている構成とポリシーに基づいて横断的な関心事を実装するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1801">The metadata is used implement cross-cutting concerns based on policies and configuration attached to each endpoint.</span></span>

<span data-ttu-id="23763-1802">ルーティング システムには次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1802">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="23763-1803">ルート テンプレート構文は、トークン化されたルート パラメーターでルートを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1803">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="23763-1804">規則スタイルおよび属性スタイルのエンドポイント構成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1804">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="23763-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> は、URL パラメーターに特定のエンドポイント制約で有効な値が含まれているかどうかを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1805"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="23763-1806">MVC/Razor Pages などのアプリ モデルでは、ルーティング シナリオの予測可能な実装を持つ、すべてのエンドポイントを登録します。</span><span class="sxs-lookup"><span data-stu-id="23763-1806">App models, such as MVC/Razor Pages, register all of their endpoints, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="23763-1807">ルーティングの実装では、必要に応じて、ミドルウェア パイプラインでのルーティングに関する決定を行います。</span><span class="sxs-lookup"><span data-stu-id="23763-1807">The routing implementation makes routing decisions wherever desired in the middleware pipeline.</span></span>
* <span data-ttu-id="23763-1808">ルーティング ミドルウェアの後に表示されるミドルウェアでは、特定の要求 URI に関するルーティング ミドルウェアのエンドポイント決定の結果を検査できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1808">Middleware that appears after a Routing Middleware can inspect the result of the Routing Middleware's endpoint decision for a given request URI.</span></span>
* <span data-ttu-id="23763-1809">ミドルウェア パイプラインの任意の場所でアプリのすべてのエンドポイントを列挙することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1809">It's possible to enumerate all of the endpoints in the app anywhere in the middleware pipeline.</span></span>
* <span data-ttu-id="23763-1810">アプリではルーティングを使用し、エンドポイント情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="23763-1810">An app can use routing to generate URLs (for example, for redirection or links) based on endpoint information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="23763-1811">URL の生成は、任意の拡張機能をサポートする、アドレスに基づきます。</span><span class="sxs-lookup"><span data-stu-id="23763-1811">URL generation is based on addresses, which support arbitrary extensibility:</span></span>

  * <span data-ttu-id="23763-1812">リンク ジェネレーター API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) は、URI を生成するために[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) を使用して任意の場所で解決できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1812">The Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) can be resolved anywhere using [dependency injection (DI)](xref:fundamentals/dependency-injection) to generate URLs.</span></span>
  * <span data-ttu-id="23763-1813">リンク ジェネレーター API を DI で使用できない場合、<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> で URL を構築するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1813">Where the Link Generator API isn't available via DI, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>

> [!NOTE]
> <span data-ttu-id="23763-1814">ASP.NET Core 2.2 のエンドポイント ルーティングのリリースでは、エンドポイント リンクは、MVC/Razor Pages アクションおよびページに制限されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1814">With the release of endpoint routing in ASP.NET Core 2.2, endpoint linking is limited to MVC/Razor Pages actions and pages.</span></span> <span data-ttu-id="23763-1815">エンドポイント リンク機能の拡張は、今後のリリースで予定されています。</span><span class="sxs-lookup"><span data-stu-id="23763-1815">The expansions of endpoint-linking capabilities is planned for future releases.</span></span>

<span data-ttu-id="23763-1816">ルーティングは <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> クラスにより[ミドルウェア](xref:fundamentals/middleware/index) パイプラインに接続されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1816">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="23763-1817">[ASP.NET Core MVC](xref:mvc/overview) では、ミドルウェア パイプラインにその構成の一部としてルーティングが追加され、MVC および Razor Pages アプリでのルーティングが処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1817">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="23763-1818">スタンドアロン コンポーネントとしてルーティングを利用する方法については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-1818">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="23763-1819">URL 一致</span><span class="sxs-lookup"><span data-stu-id="23763-1819">URL matching</span></span>

<span data-ttu-id="23763-1820">URL 一致というプロセスでは、ルーティングによって、受信要求が*エンドポイント*に配布されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1820">URL matching is the process by which routing dispatches an incoming request to an *endpoint*.</span></span> <span data-ttu-id="23763-1821">このプロセスは URL パスのデータに基づきますが、要求内のあらゆるデータを考慮することもできます。</span><span class="sxs-lookup"><span data-stu-id="23763-1821">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="23763-1822">アプリの規模を大きくしたり、より複雑にしたりするとき、要求を個別のハンドラーに配布する機能が鍵となります。</span><span class="sxs-lookup"><span data-stu-id="23763-1822">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="23763-1823">エンドポイント ルーティングのルーティング システムでは、配布に関するすべての決定が行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1823">The routing system in endpoint routing is responsible for all dispatching decisions.</span></span> <span data-ttu-id="23763-1824">ミドルウェアでは選択されたエンドポイントに基づいてポリシーが適用されるため、セキュリティ ポリシーの適用や配布に影響する可能性のある決定は、ルーティング システム内で行うことが重要です。</span><span class="sxs-lookup"><span data-stu-id="23763-1824">Since the middleware applies policies based on the selected endpoint, it's important that any decision that can affect dispatching or the application of security policies is made inside the routing system.</span></span>

<span data-ttu-id="23763-1825">エンドポイントのデリゲートが実行されると、それまでに実行された要求処理に基づいて、[RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) のプロパティが適切な値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1825">When the endpoint delegate is executed, the properties of [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) are set to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="23763-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) は、ルートから生成された*ルート値*のディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-1826">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="23763-1827">この値は通常、URL のトークン化で決定されます。この値を利用してユーザー入力を受け取ったり、アプリ内で決定をさらに配布したりできます。</span><span class="sxs-lookup"><span data-stu-id="23763-1827">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="23763-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) は、一致したルートに関連する追加データのプロパティ バッグです。</span><span class="sxs-lookup"><span data-stu-id="23763-1828">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="23763-1829">一致したルートに基づいてアプリで決定を行えるように、<xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> が提供されます。これは状態データと各ルートの関連付けをサポートするためのものです。</span><span class="sxs-lookup"><span data-stu-id="23763-1829"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="23763-1830">この値は開発者が定義するものです。ルーティングの動作に影響を与えることは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="23763-1830">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="23763-1831">また、[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) に一時退避される値はどのような型でも構いません。一方、[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) の場合は、文字列間で変換できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-1831">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="23763-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) は、過去に要求に一致したルートの一覧です。</span><span class="sxs-lookup"><span data-stu-id="23763-1832">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="23763-1833">ルートはルートの中に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1833">Routes can be nested inside of one another.</span></span> <span data-ttu-id="23763-1834"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> プロパティは、結果的に一致をもたらしたルートの論理ツリーを通るパスを表します。</span><span class="sxs-lookup"><span data-stu-id="23763-1834">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="23763-1835">一般的に、<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最初の項目はルート コレクションであり、これは URL 生成に使用するものです。</span><span class="sxs-lookup"><span data-stu-id="23763-1835">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="23763-1836"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最後の項目は、一致したルート ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="23763-1836">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a><span data-ttu-id="23763-1837">LinkGenerator による URL の生成</span><span class="sxs-lookup"><span data-stu-id="23763-1837">URL generation with LinkGenerator</span></span>

<span data-ttu-id="23763-1838">URL 生成は、ルーティングにおいて、一連のルート値に基づいて URL パスを作成するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="23763-1838">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="23763-1839">これにより、エンドポイントとそれにアクセスする URL を論理的に分離できます。</span><span class="sxs-lookup"><span data-stu-id="23763-1839">This allows for a logical separation between your endpoints and the URLs that access them.</span></span>

<span data-ttu-id="23763-1840">エンドポイント ルーティングには、リンク ジェネレーター API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23763-1840">Endpoint routing includes the Link Generator API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>).</span></span> <span data-ttu-id="23763-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> は、[DI](xref:fundamentals/dependency-injection) から取得できるシングルトン サービスです。</span><span class="sxs-lookup"><span data-stu-id="23763-1841"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is a singleton service that can be retrieved from [DI](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="23763-1842">API は、実行中の要求のコンテキスト外で使用することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-1842">The API can be used outside of the context of an executing request.</span></span> <span data-ttu-id="23763-1843">MVC の <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> と、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)、HTML ヘルパー、[アクション結果](xref:mvc/controllers/actions)など、<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> に依存するシナリオではリンク ジェネレーターを使用して、リンク生成機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-1843">MVC's <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> and scenarios that rely on <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, and [Action Results](xref:mvc/controllers/actions), use the link generator to provide link generating capabilities.</span></span>

<span data-ttu-id="23763-1844">リンク ジェネレーターは、*アドレス* と*アドレス スキーム* の概念に基づいています。</span><span class="sxs-lookup"><span data-stu-id="23763-1844">The link generator is backed by the concept of an *address* and *address schemes*.</span></span> <span data-ttu-id="23763-1845">アドレス スキームは、リンク生成で考慮すべきエンドポイントを決定する方法です。</span><span class="sxs-lookup"><span data-stu-id="23763-1845">An address scheme is a way of determining the endpoints that should be considered for link generation.</span></span> <span data-ttu-id="23763-1846">たとえば、MVC/Razor Pages からの、多くのユーザーに馴染みのあるルート名やルート値シナリオは、アドレス スキームとして実装されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1846">For example, the route name and route values scenarios many users are familiar with from MVC/Razor Pages are implemented as an address scheme.</span></span>

<span data-ttu-id="23763-1847">リンク ジェネレーターでは、次の拡張メソッドを使用して、MVC/Razor Pages アクションおよびページにリンクできます。</span><span class="sxs-lookup"><span data-stu-id="23763-1847">The link generator can link to MVC/Razor Pages actions and pages via the following extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

<span data-ttu-id="23763-1848">これらのメソッドのオーバーロードでは、`HttpContext` を含む引数が受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="23763-1848">An overload of these methods accepts arguments that include the `HttpContext`.</span></span> <span data-ttu-id="23763-1849">これらのメソッドは `Url.Action` および `Url.Page` と機能的には同等ですが、柔軟性とオプションがさらに提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1849">These methods are functionally equivalent to `Url.Action` and `Url.Page` but offer additional flexibility and options.</span></span>

<span data-ttu-id="23763-1850">`GetPath*` メソッドは、絶対パスを含む URI を生成するという点で `Url.Action` および `Url.Page` に最も似ています。</span><span class="sxs-lookup"><span data-stu-id="23763-1850">The `GetPath*` methods are most similar to `Url.Action` and `Url.Page` in that they generate a URI containing an absolute path.</span></span> <span data-ttu-id="23763-1851">`GetUri*` メソッドでは常に、スキームとホストを含む絶対 URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1851">The `GetUri*` methods always generate an absolute URI containing a scheme and host.</span></span> <span data-ttu-id="23763-1852">`HttpContext` を受け入れるメソッドでは、実行中の要求のコンテキストで URI が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1852">The methods that accept an `HttpContext` generate a URI in the context of the executing request.</span></span> <span data-ttu-id="23763-1853">実行中の要求からのアンビエント ルート値、URL ベース パス、スキーム、およびホストは、オーバーライドされない限り使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1853">The ambient route values, URL base path, scheme, and host from the executing request are used unless overridden.</span></span>

<span data-ttu-id="23763-1854"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> はアドレスと共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1854"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> is called with an address.</span></span> <span data-ttu-id="23763-1855">URI の生成は、次の 2 つの手順で行われます。</span><span class="sxs-lookup"><span data-stu-id="23763-1855">Generating a URI occurs in two steps:</span></span>

1. <span data-ttu-id="23763-1856">アドレスは、そのアドレスと一致するエンドポイントのリストにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23763-1856">An address is bound to a list of endpoints that match the address.</span></span>
1. <span data-ttu-id="23763-1857">各エンドポイントの `RoutePattern` は、指定された値と一致するルート パターンが見つかるまで評価されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1857">Each endpoint's `RoutePattern` is evaluated until a route pattern that matches the supplied values is found.</span></span> <span data-ttu-id="23763-1858">結果の出力は、リンク ジェネレーターに指定された他の URI 部分と結合され、返されます。</span><span class="sxs-lookup"><span data-stu-id="23763-1858">The resulting output is combined with the other URI parts supplied to the link generator and returned.</span></span>

<span data-ttu-id="23763-1859"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> によって提供されるメソッドでは、すべての種類のアドレスの標準的なリンク生成機能がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-1859">The methods provided by <xref:Microsoft.AspNetCore.Routing.LinkGenerator> support standard link generation capabilities for any type of address.</span></span> <span data-ttu-id="23763-1860">リンク ジェネレーターを使用する最も便利な方法は、特定のアドレスの種類の操作を実行する拡張メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="23763-1860">The most convenient way to use the link generator is through extension methods that perform operations for a specific address type.</span></span>

| <span data-ttu-id="23763-1861">拡張メソッド</span><span class="sxs-lookup"><span data-stu-id="23763-1861">Extension Method</span></span>   | <span data-ttu-id="23763-1862">説明</span><span class="sxs-lookup"><span data-stu-id="23763-1862">Description</span></span>                                                         |
| ---
<span data-ttu-id="23763-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1863">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1864">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1864">'Blazor'</span></span>
- <span data-ttu-id="23763-1865">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1865">'Identity'</span></span>
- <span data-ttu-id="23763-1866">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1866">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1867">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1867">'Razor'</span></span>
- <span data-ttu-id="23763-1868">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1868">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1869">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1870">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1870">'Blazor'</span></span>
- <span data-ttu-id="23763-1871">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1871">'Identity'</span></span>
- <span data-ttu-id="23763-1872">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1872">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1873">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1873">'Razor'</span></span>
- <span data-ttu-id="23763-1874">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1874">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1875">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1876">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1876">'Blazor'</span></span>
- <span data-ttu-id="23763-1877">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1877">'Identity'</span></span>
- <span data-ttu-id="23763-1878">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1878">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1879">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1879">'Razor'</span></span>
- <span data-ttu-id="23763-1880">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1880">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1881">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1882">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1882">'Blazor'</span></span>
- <span data-ttu-id="23763-1883">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1883">'Identity'</span></span>
- <span data-ttu-id="23763-1884">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1884">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1885">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1885">'Razor'</span></span>
- <span data-ttu-id="23763-1886">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1886">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1887">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1888">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1888">'Blazor'</span></span>
- <span data-ttu-id="23763-1889">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1889">'Identity'</span></span>
- <span data-ttu-id="23763-1890">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1890">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1891">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1891">'Razor'</span></span>
- <span data-ttu-id="23763-1892">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1892">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1893">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1894">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1894">'Blazor'</span></span>
- <span data-ttu-id="23763-1895">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1895">'Identity'</span></span>
- <span data-ttu-id="23763-1896">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1896">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1897">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1897">'Razor'</span></span>
- <span data-ttu-id="23763-1898">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1898">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1899">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1900">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1900">'Blazor'</span></span>
- <span data-ttu-id="23763-1901">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1901">'Identity'</span></span>
- <span data-ttu-id="23763-1902">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1902">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1903">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1903">'Razor'</span></span>
- <span data-ttu-id="23763-1904">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1904">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1905">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1906">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1906">'Blazor'</span></span>
- <span data-ttu-id="23763-1907">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1907">'Identity'</span></span>
- <span data-ttu-id="23763-1908">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1908">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1909">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1909">'Razor'</span></span>
- <span data-ttu-id="23763-1910">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1910">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1911">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1912">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1912">'Blazor'</span></span>
- <span data-ttu-id="23763-1913">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1913">'Identity'</span></span>
- <span data-ttu-id="23763-1914">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1914">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1915">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1915">'Razor'</span></span>
- <span data-ttu-id="23763-1916">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1916">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1917">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1918">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1918">'Blazor'</span></span>
- <span data-ttu-id="23763-1919">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1919">'Identity'</span></span>
- <span data-ttu-id="23763-1920">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1920">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1921">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1921">'Razor'</span></span>
- <span data-ttu-id="23763-1922">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1922">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1923">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1924">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1924">'Blazor'</span></span>
- <span data-ttu-id="23763-1925">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1925">'Identity'</span></span>
- <span data-ttu-id="23763-1926">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1926">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1927">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1927">'Razor'</span></span>
- <span data-ttu-id="23763-1928">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1928">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1929">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1930">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1930">'Blazor'</span></span>
- <span data-ttu-id="23763-1931">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1931">'Identity'</span></span>
- <span data-ttu-id="23763-1932">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1932">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1933">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1933">'Razor'</span></span>
- <span data-ttu-id="23763-1934">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1934">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1935">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1936">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1936">'Blazor'</span></span>
- <span data-ttu-id="23763-1937">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1937">'Identity'</span></span>
- <span data-ttu-id="23763-1938">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1938">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1939">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1939">'Razor'</span></span>
- <span data-ttu-id="23763-1940">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1940">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1941">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1942">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1942">'Blazor'</span></span>
- <span data-ttu-id="23763-1943">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1943">'Identity'</span></span>
- <span data-ttu-id="23763-1944">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1944">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1945">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1945">'Razor'</span></span>
- <span data-ttu-id="23763-1946">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1946">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1947">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1948">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1948">'Blazor'</span></span>
- <span data-ttu-id="23763-1949">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1949">'Identity'</span></span>
- <span data-ttu-id="23763-1950">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1950">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1951">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1951">'Razor'</span></span>
- <span data-ttu-id="23763-1952">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1952">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1953">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1954">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1954">'Blazor'</span></span>
- <span data-ttu-id="23763-1955">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1955">'Identity'</span></span>
- <span data-ttu-id="23763-1956">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1956">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1957">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1957">'Razor'</span></span>
- <span data-ttu-id="23763-1958">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1958">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1959">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1960">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1960">'Blazor'</span></span>
- <span data-ttu-id="23763-1961">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1961">'Identity'</span></span>
- <span data-ttu-id="23763-1962">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1962">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1963">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1963">'Razor'</span></span>
- <span data-ttu-id="23763-1964">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1964">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1965">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1966">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1966">'Blazor'</span></span>
- <span data-ttu-id="23763-1967">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1967">'Identity'</span></span>
- <span data-ttu-id="23763-1968">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1968">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1969">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1969">'Razor'</span></span>
- <span data-ttu-id="23763-1970">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1970">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1971">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1972">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1972">'Blazor'</span></span>
- <span data-ttu-id="23763-1973">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1973">'Identity'</span></span>
- <span data-ttu-id="23763-1974">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1974">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1975">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1975">'Razor'</span></span>
- <span data-ttu-id="23763-1976">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1976">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1977">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1978">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1978">'Blazor'</span></span>
- <span data-ttu-id="23763-1979">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1979">'Identity'</span></span>
- <span data-ttu-id="23763-1980">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1980">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1981">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1981">'Razor'</span></span>
- <span data-ttu-id="23763-1982">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1982">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1983">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1984">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1984">'Blazor'</span></span>
- <span data-ttu-id="23763-1985">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1985">'Identity'</span></span>
- <span data-ttu-id="23763-1986">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1986">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1987">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1987">'Razor'</span></span>
- <span data-ttu-id="23763-1988">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1988">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1989">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1990">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1990">'Blazor'</span></span>
- <span data-ttu-id="23763-1991">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1991">'Identity'</span></span>
- <span data-ttu-id="23763-1992">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1992">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1993">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1993">'Razor'</span></span>
- <span data-ttu-id="23763-1994">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-1994">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-1995">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-1996">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-1996">'Blazor'</span></span>
- <span data-ttu-id="23763-1997">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-1997">'Identity'</span></span>
- <span data-ttu-id="23763-1998">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-1998">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-1999">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-1999">'Razor'</span></span>
- <span data-ttu-id="23763-2000">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2000">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2001">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2002">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2002">'Blazor'</span></span>
- <span data-ttu-id="23763-2003">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2003">'Identity'</span></span>
- <span data-ttu-id="23763-2004">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2004">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2005">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2005">'Razor'</span></span>
- <span data-ttu-id="23763-2006">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2006">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2007">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2008">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2008">'Blazor'</span></span>
- <span data-ttu-id="23763-2009">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2009">'Identity'</span></span>
- <span data-ttu-id="23763-2010">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2010">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2011">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2011">'Razor'</span></span>
- <span data-ttu-id="23763-2012">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2012">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2013">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2014">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2014">'Blazor'</span></span>
- <span data-ttu-id="23763-2015">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2015">'Identity'</span></span>
- <span data-ttu-id="23763-2016">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2016">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2017">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2017">'Razor'</span></span>
- <span data-ttu-id="23763-2018">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2018">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2019">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2020">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2020">'Blazor'</span></span>
- <span data-ttu-id="23763-2021">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2021">'Identity'</span></span>
- <span data-ttu-id="23763-2022">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2022">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2023">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2023">'Razor'</span></span>
- <span data-ttu-id="23763-2024">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2024">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2025">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2026">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2026">'Blazor'</span></span>
- <span data-ttu-id="23763-2027">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2027">'Identity'</span></span>
- <span data-ttu-id="23763-2028">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2028">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2029">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2029">'Razor'</span></span>
- <span data-ttu-id="23763-2030">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2030">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2031">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2032">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2032">'Blazor'</span></span>
- <span data-ttu-id="23763-2033">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2033">'Identity'</span></span>
- <span data-ttu-id="23763-2034">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2034">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2035">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2035">'Razor'</span></span>
- <span data-ttu-id="23763-2036">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2036">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2037">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2038">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2038">'Blazor'</span></span>
- <span data-ttu-id="23763-2039">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2039">'Identity'</span></span>
- <span data-ttu-id="23763-2040">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2040">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2041">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2041">'Razor'</span></span>
- <span data-ttu-id="23763-2042">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2042">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2043">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2044">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2044">'Blazor'</span></span>
- <span data-ttu-id="23763-2045">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2045">'Identity'</span></span>
- <span data-ttu-id="23763-2046">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2046">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2047">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2047">'Razor'</span></span>
- <span data-ttu-id="23763-2048">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2048">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2049">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2050">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2050">'Blazor'</span></span>
- <span data-ttu-id="23763-2051">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2051">'Identity'</span></span>
- <span data-ttu-id="23763-2052">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2052">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2053">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2053">'Razor'</span></span>
- <span data-ttu-id="23763-2054">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2054">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2055">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2056">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2056">'Blazor'</span></span>
- <span data-ttu-id="23763-2057">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2057">'Identity'</span></span>
- <span data-ttu-id="23763-2058">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2058">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2059">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2059">'Razor'</span></span>
- <span data-ttu-id="23763-2060">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2060">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2061">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2062">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2062">'Blazor'</span></span>
- <span data-ttu-id="23763-2063">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2063">'Identity'</span></span>
- <span data-ttu-id="23763-2064">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2064">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2065">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2065">'Razor'</span></span>
- <span data-ttu-id="23763-2066">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2066">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2067">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2068">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2068">'Blazor'</span></span>
- <span data-ttu-id="23763-2069">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2069">'Identity'</span></span>
- <span data-ttu-id="23763-2070">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2070">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2071">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2071">'Razor'</span></span>
- <span data-ttu-id="23763-2072">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2072">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2073">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2074">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2074">'Blazor'</span></span>
- <span data-ttu-id="23763-2075">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2075">'Identity'</span></span>
- <span data-ttu-id="23763-2076">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2076">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2077">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2077">'Razor'</span></span>
- <span data-ttu-id="23763-2078">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2078">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2079">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2080">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2080">'Blazor'</span></span>
- <span data-ttu-id="23763-2081">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2081">'Identity'</span></span>
- <span data-ttu-id="23763-2082">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2082">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2083">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2083">'Razor'</span></span>
- <span data-ttu-id="23763-2084">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2084">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2085">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2086">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2086">'Blazor'</span></span>
- <span data-ttu-id="23763-2087">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2087">'Identity'</span></span>
- <span data-ttu-id="23763-2088">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2088">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2089">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2089">'Razor'</span></span>
- <span data-ttu-id="23763-2090">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2090">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 指定された値に基づき、絶対パスを含む URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2091">---------------------------------- | | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generates a URI with an absolute path based on the provided values.</span></span> <span data-ttu-id="23763-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 指定された値に基づき、絶対 URI を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2092">| | <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generates an absolute URI based on the provided values.</span></span>             |

> [!WARNING]
> <span data-ttu-id="23763-2093"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> メソッド呼び出しによる次の影響に注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2093">Pay attention to the following implications of calling <xref:Microsoft.AspNetCore.Routing.LinkGenerator> methods:</span></span>
>
> * <span data-ttu-id="23763-2094">受信要求の `Host` ヘッダーが確認されないアプリ構成では、`GetUri*` 拡張メソッドは注意して使用してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2094">Use `GetUri*` extension methods with caution in an app configuration that doesn't validate the `Host` header of incoming requests.</span></span> <span data-ttu-id="23763-2095">受信要求の `Host` ヘッダーが確認されていない場合、信頼されていない要求入力を、ビュー/ページの URI でクライアントに送り返すことができます。</span><span class="sxs-lookup"><span data-stu-id="23763-2095">If the `Host` header of incoming requests isn't validated, untrusted request input can be sent back to the client in URIs in a view/page.</span></span> <span data-ttu-id="23763-2096">すべての運用アプリで、`Host` ヘッダーを既知の有効な値と照らし合わせて確認するようにサーバーを構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23763-2096">We recommend that all production apps configure their server to validate the `Host` header against known valid values.</span></span>
>
> * <span data-ttu-id="23763-2097">ミドルウェアで `Map` または `MapWhen` と組み合わせて、<xref:Microsoft.AspNetCore.Routing.LinkGenerator> を使用する場合は注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2097">Use <xref:Microsoft.AspNetCore.Routing.LinkGenerator> with caution in middleware in combination with `Map` or `MapWhen`.</span></span> <span data-ttu-id="23763-2098">`Map*` では、実行中の要求の基本パスが変更され、リンク生成の出力に影響します。</span><span class="sxs-lookup"><span data-stu-id="23763-2098">`Map*` changes the base path of the executing request, which affects the output of link generation.</span></span> <span data-ttu-id="23763-2099">すべての <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API で基本パスを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-2099">All of the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> APIs allow specifying a base path.</span></span> <span data-ttu-id="23763-2100">リンク生成への `Map*` の影響を元に戻すための空の基本パスを必ず指定してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2100">Always specify an empty base path to undo `Map*`'s affect on link generation.</span></span>

## <a name="differences-from-earlier-versions-of-routing"></a><span data-ttu-id="23763-2101">以前のバージョンのルーティングとの相違点</span><span class="sxs-lookup"><span data-stu-id="23763-2101">Differences from earlier versions of routing</span></span>

<span data-ttu-id="23763-2102">ASP.NET Core 2.2 以降でのエンドポイント ルーティングと、ASP.NET Core での以前のバージョンのルーティングには、次のようないくつかの違いがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2102">A few differences exist between endpoint routing in ASP.NET Core 2.2 or later and earlier versions of routing in ASP.NET Core:</span></span>

* <span data-ttu-id="23763-2103">エンドポイント ルーティング システムでは、<xref:Microsoft.AspNetCore.Routing.Route> からの継承を含む、<xref:Microsoft.AspNetCore.Routing.IRouter> ベースの拡張機能がサポートされません。</span><span class="sxs-lookup"><span data-stu-id="23763-2103">The endpoint routing system doesn't support <xref:Microsoft.AspNetCore.Routing.IRouter>-based extensibility, including inheriting from <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>

* <span data-ttu-id="23763-2104">エンドポイント ルーティングでは [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) がサポートされません。</span><span class="sxs-lookup"><span data-stu-id="23763-2104">Endpoint routing doesn't support [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim).</span></span> <span data-ttu-id="23763-2105">互換性 shim を引き続き使用するには、2.1 の[互換性バージョン](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-2105">Use the 2.1 [compatibility version](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) to continue using the compatibility shim.</span></span>

* <span data-ttu-id="23763-2106">エンドポイント ルーティングでは、規則ルートを使用する場合に生成される URI の大文字と小文字の指定の動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="23763-2106">Endpoint Routing has different behavior for the casing of generated URIs when using conventional routes.</span></span>

  <span data-ttu-id="23763-2107">次の既定のルート テンプレートについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-2107">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="23763-2108">次のルートを使用して、アクションへのリンクを生成するとします。</span><span class="sxs-lookup"><span data-stu-id="23763-2108">Suppose you generate a link to an action using the following route:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  <span data-ttu-id="23763-2109"><xref:Microsoft.AspNetCore.Routing.IRouter> ベースのルーティングでは、このコードで `/blog/ReadPost/17` の URI が生成され、指定されたルート値の大文字と小文字の指定が優先されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2109">With <xref:Microsoft.AspNetCore.Routing.IRouter>-based routing, this code generates a URI of `/blog/ReadPost/17`, which respects the casing of the provided route value.</span></span> <span data-ttu-id="23763-2110">ASP.NET Core 2.2 以降のエンドポイント ルーティングでは、`/Blog/ReadPost/17` ("Blog" の先頭は大文字) が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2110">Endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` ("Blog" is capitalized).</span></span> <span data-ttu-id="23763-2111">エンドポイント ルーティングでは `IOutboundParameterTransformer` インターフェイスが提供されます。それを使用して、この動作をグローバルにカスタマイズしたり、URL のマッピングで異なる規則を適用したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-2111">Endpoint routing provides the `IOutboundParameterTransformer` interface that can be used to customize this behavior globally or to apply different conventions for mapping URLs.</span></span>

  <span data-ttu-id="23763-2112">詳細については、「[パラメーター トランスフォーマー参照](#parameter-transformer-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2112">For more information, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

* <span data-ttu-id="23763-2113">存在しないコントローラー/アクションまたはページへのリンクを試みる場合、規則ルートがある MVC/Razor Pages によって使用されるリンク生成の動作は異なります。</span><span class="sxs-lookup"><span data-stu-id="23763-2113">Link Generation used by MVC/Razor Pages with conventional routes behaves differently when attempting to link to an controller/action or page that doesn't exist.</span></span>

  <span data-ttu-id="23763-2114">次の既定のルート テンプレートについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-2114">Consider the following default route template:</span></span>

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  <span data-ttu-id="23763-2115">次のように既定のテンプレートを使用して、アクションへのリンクを生成するとします。</span><span class="sxs-lookup"><span data-stu-id="23763-2115">Suppose you generate a link to an action using the default template with the following:</span></span>

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  <span data-ttu-id="23763-2116">`IRouter` ベースのルーティングでは、`BlogController` が存在しない場合や `ReadPost` アクション メソッドがない場合でも、結果は常に `/Blog/ReadPost/17` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-2116">With `IRouter`-based routing, the result is always `/Blog/ReadPost/17`, even if the `BlogController` doesn't exist or doesn't have a `ReadPost` action method.</span></span> <span data-ttu-id="23763-2117">予想どおり、アクション メソッドが存在する場合は、ASP.NET Core 2.2 以降のエンドポイント ルーティングで `/Blog/ReadPost/17` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2117">As expected, endpoint routing in ASP.NET Core 2.2 or later produces `/Blog/ReadPost/17` if the action method exists.</span></span> <span data-ttu-id="23763-2118">*しかし、アクションが存在しない場合は、エンドポイント ルーティングで空の文字列が生成されます。*</span><span class="sxs-lookup"><span data-stu-id="23763-2118">*However, endpoint routing produces an empty string if the action doesn't exist.*</span></span> <span data-ttu-id="23763-2119">概念的には、エンドポイント ルーティングでは、アクションが存在しない場合はエンドポイントが存在するとは見なされません。</span><span class="sxs-lookup"><span data-stu-id="23763-2119">Conceptually, endpoint routing doesn't assume that the endpoint exists if the action doesn't exist.</span></span>

* <span data-ttu-id="23763-2120">エンドポイント ルーティングで使用する場合は、リンク生成の*アンビエント値の無効化アルゴリズム* の動作が異なります。</span><span class="sxs-lookup"><span data-stu-id="23763-2120">The link generation *ambient value invalidation algorithm* behaves differently when used with endpoint routing.</span></span>

  <span data-ttu-id="23763-2121">*アンビエント値の無効化* は、現在実行中の要求からのルート値 (アンビエント値) のうち、どちらがリンク生成操作で使用できのかを判断するアルゴリズムです。</span><span class="sxs-lookup"><span data-stu-id="23763-2121">*Ambient value invalidation* is the algorithm that decides which route values from the currently executing request (the ambient values) can be used in link generation operations.</span></span> <span data-ttu-id="23763-2122">規則ルーティングでは、異なるアクションへのリンク時に余分なルート値が常に無効化されました。</span><span class="sxs-lookup"><span data-stu-id="23763-2122">Conventional routing always invalidated extra route values when linking to a different action.</span></span> <span data-ttu-id="23763-2123">属性ルーティングは、ASP.NET Core 2.2 のリリースより前ではこのように動作しませんでした。</span><span class="sxs-lookup"><span data-stu-id="23763-2123">Attribute routing didn't have this behavior prior to the release of ASP.NET Core 2.2.</span></span> <span data-ttu-id="23763-2124">以前のバージョンの ASP.NET Core では、同じルート パラメーター名を使用する別のアクションへのリンクでリンク生成エラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="23763-2124">In earlier versions of ASP.NET Core, links to another action that use the same route parameter names resulted in link generation errors.</span></span> <span data-ttu-id="23763-2125">ASP.NET Core 2.2 以降では、両方の形式のルーティングで、別のアクションへのリンク時に値が無効化されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2125">In ASP.NET Core 2.2 or later, both forms of routing invalidate values when linking to another action.</span></span>

  <span data-ttu-id="23763-2126">ASP.NET Core 2.1 以前での次の例について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-2126">Consider the following example in ASP.NET Core 2.1 or earlier.</span></span> <span data-ttu-id="23763-2127">別のアクション (または別のページ) にリンクする場合、好ましくない方法でルート値が再利用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2127">When linking to another action (or another page), route values can be reused in undesirable ways.</span></span>

  <span data-ttu-id="23763-2128">*/Pages/Store/Product.cshtml* の場合:</span><span class="sxs-lookup"><span data-stu-id="23763-2128">In */Pages/Store/Product.cshtml*:</span></span>

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  <span data-ttu-id="23763-2129">*/Pages/Login.cshtml* の場合:</span><span class="sxs-lookup"><span data-stu-id="23763-2129">In */Pages/Login.cshtml*:</span></span>

  ```cshtml
  @page "{id?}"
  ```

  <span data-ttu-id="23763-2130">ASP.NET Core 2.1 以前で URI が `/Store/Product/18` である場合、`@Url.Page("/Login")` によって Store/Info ページに生成されるリンクは `/Login/18` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-2130">If the URI is `/Store/Product/18` in ASP.NET Core 2.1 or earlier, the link generated on the Store/Info page by `@Url.Page("/Login")` is `/Login/18`.</span></span> <span data-ttu-id="23763-2131">リンク先が完全にアプリの異なる部分であっても、18 という `id` の値が再利用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2131">The `id` value of 18 is reused, even though the link destination is different part of the app entirely.</span></span> <span data-ttu-id="23763-2132">`/Login` ページのコンテキスト内の `id` ルート値は、商品 ID 値ではなく、ユーザー ID 値であると考えられます。</span><span class="sxs-lookup"><span data-stu-id="23763-2132">The `id` route value in the context of the `/Login` page is probably a user ID value, not a store product ID value.</span></span>

  <span data-ttu-id="23763-2133">ASP.NET Core 2.2 以降でのエンドポイント ルーティングでは、結果は `/Login` となります。</span><span class="sxs-lookup"><span data-stu-id="23763-2133">In endpoint routing with ASP.NET Core 2.2 or later, the result is `/Login`.</span></span> <span data-ttu-id="23763-2134">リンク先が異なるアクションやページである場合、アンビエント値は再利用されません。</span><span class="sxs-lookup"><span data-stu-id="23763-2134">Ambient values aren't reused when the linked destination is a different action or page.</span></span>

* <span data-ttu-id="23763-2135">ラウンド トリップ ルート パラメーターの構文: 二重アスタリスク (`**`) キャッチオール パラメーター構文を使用する場合、スラッシュはエンコードされません。</span><span class="sxs-lookup"><span data-stu-id="23763-2135">Round-tripping route parameter syntax: Forward slashes aren't encoded when using a double-asterisk (`**`) catch-all parameter syntax.</span></span>

  <span data-ttu-id="23763-2136">リンクの生成中に、ルーティング システムでは、スラッシュを除く二重アスタリスク (`**`) キャッチオール パラメーター (`{**myparametername}` など) でキャプチャされた値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="23763-2136">During link generation, the routing system encodes the value captured in a double-asterisk (`**`) catch-all parameter (for example, `{**myparametername}`) except the forward slashes.</span></span> <span data-ttu-id="23763-2137">二重アスタリスク キャッチオールは、ASP.NET Core 2.2 以降の `IRouter` ベース ルーティングでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="23763-2137">The double-asterisk catch-all is supported with `IRouter`-based routing in ASP.NET Core 2.2 or later.</span></span>

  <span data-ttu-id="23763-2138">以前のバージョンの ASP.NET Core での単一のアスタリスク キャッチオール パラメーター構文 (`{*myparametername}`) は引き続きサポートされ、スラッシュはエンコードされます。</span><span class="sxs-lookup"><span data-stu-id="23763-2138">The single asterisk catch-all parameter syntax in prior versions of ASP.NET Core (`{*myparametername}`) remains supported, and forward slashes are encoded.</span></span>

  | <span data-ttu-id="23763-2139">ルート</span><span class="sxs-lookup"><span data-stu-id="23763-2139">Route</span></span>              | <span data-ttu-id="23763-2140">以下で生成されるリンク</span><span class="sxs-lookup"><span data-stu-id="23763-2140">Link generated with</span></span><br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ---
<span data-ttu-id="23763-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2141">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2142">'Blazor'</span></span>
- <span data-ttu-id="23763-2143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2143">'Identity'</span></span>
- <span data-ttu-id="23763-2144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2144">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2145">'Razor'</span></span>
- <span data-ttu-id="23763-2146">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2148">'Blazor'</span></span>
- <span data-ttu-id="23763-2149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2149">'Identity'</span></span>
- <span data-ttu-id="23763-2150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2150">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2151">'Razor'</span></span>
- <span data-ttu-id="23763-2152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2154">'Blazor'</span></span>
- <span data-ttu-id="23763-2155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2155">'Identity'</span></span>
- <span data-ttu-id="23763-2156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2156">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2157">'Razor'</span></span>
- <span data-ttu-id="23763-2158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2160">'Blazor'</span></span>
- <span data-ttu-id="23763-2161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2161">'Identity'</span></span>
- <span data-ttu-id="23763-2162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2162">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2163">'Razor'</span></span>
- <span data-ttu-id="23763-2164">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2166">'Blazor'</span></span>
- <span data-ttu-id="23763-2167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2167">'Identity'</span></span>
- <span data-ttu-id="23763-2168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2168">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2169">'Razor'</span></span>
- <span data-ttu-id="23763-2170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2172">'Blazor'</span></span>
- <span data-ttu-id="23763-2173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2173">'Identity'</span></span>
- <span data-ttu-id="23763-2174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2174">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2175">'Razor'</span></span>
- <span data-ttu-id="23763-2176">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2178">'Blazor'</span></span>
- <span data-ttu-id="23763-2179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2179">'Identity'</span></span>
- <span data-ttu-id="23763-2180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2180">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2181">'Razor'</span></span>
- <span data-ttu-id="23763-2182">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2182">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2183">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2184">'Blazor'</span></span>
- <span data-ttu-id="23763-2185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2185">'Identity'</span></span>
- <span data-ttu-id="23763-2186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2186">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2187">'Razor'</span></span>
- <span data-ttu-id="23763-2188">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2190">'Blazor'</span></span>
- <span data-ttu-id="23763-2191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2191">'Identity'</span></span>
- <span data-ttu-id="23763-2192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2192">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2193">'Razor'</span></span>
- <span data-ttu-id="23763-2194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2196">'Blazor'</span></span>
- <span data-ttu-id="23763-2197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2197">'Identity'</span></span>
- <span data-ttu-id="23763-2198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2198">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2199">'Razor'</span></span>
- <span data-ttu-id="23763-2200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2202">'Blazor'</span></span>
- <span data-ttu-id="23763-2203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2203">'Identity'</span></span>
- <span data-ttu-id="23763-2204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2204">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2205">'Razor'</span></span>
- <span data-ttu-id="23763-2206">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2208">'Blazor'</span></span>
- <span data-ttu-id="23763-2209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2209">'Identity'</span></span>
- <span data-ttu-id="23763-2210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2210">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2211">'Razor'</span></span>
- <span data-ttu-id="23763-2212">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2214">'Blazor'</span></span>
- <span data-ttu-id="23763-2215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2215">'Identity'</span></span>
- <span data-ttu-id="23763-2216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2216">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2217">'Razor'</span></span>
- <span data-ttu-id="23763-2218">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2220">'Blazor'</span></span>
- <span data-ttu-id="23763-2221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2221">'Identity'</span></span>
- <span data-ttu-id="23763-2222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2222">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2223">'Razor'</span></span>
- <span data-ttu-id="23763-2224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2226">'Blazor'</span></span>
- <span data-ttu-id="23763-2227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2227">'Identity'</span></span>
- <span data-ttu-id="23763-2228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2228">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2229">'Razor'</span></span>
- <span data-ttu-id="23763-2230">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2232">'Blazor'</span></span>
- <span data-ttu-id="23763-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2233">'Identity'</span></span>
- <span data-ttu-id="23763-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2235">'Razor'</span></span>
- <span data-ttu-id="23763-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2238">'Blazor'</span></span>
- <span data-ttu-id="23763-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2239">'Identity'</span></span>
- <span data-ttu-id="23763-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2241">'Razor'</span></span>
- <span data-ttu-id="23763-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2244">'Blazor'</span></span>
- <span data-ttu-id="23763-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2245">'Identity'</span></span>
- <span data-ttu-id="23763-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2247">'Razor'</span></span>
- <span data-ttu-id="23763-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2250">'Blazor'</span></span>
- <span data-ttu-id="23763-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2251">'Identity'</span></span>
- <span data-ttu-id="23763-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2253">'Razor'</span></span>
- <span data-ttu-id="23763-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2256">'Blazor'</span></span>
- <span data-ttu-id="23763-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2257">'Identity'</span></span>
- <span data-ttu-id="23763-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2259">'Razor'</span></span>
- <span data-ttu-id="23763-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2262">'Blazor'</span></span>
- <span data-ttu-id="23763-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2263">'Identity'</span></span>
- <span data-ttu-id="23763-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2265">'Razor'</span></span>
- <span data-ttu-id="23763-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2268">'Blazor'</span></span>
- <span data-ttu-id="23763-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2269">'Identity'</span></span>
- <span data-ttu-id="23763-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2271">'Razor'</span></span>
- <span data-ttu-id="23763-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2274">'Blazor'</span></span>
- <span data-ttu-id="23763-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2275">'Identity'</span></span>
- <span data-ttu-id="23763-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2277">'Razor'</span></span>
- <span data-ttu-id="23763-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2280">'Blazor'</span></span>
- <span data-ttu-id="23763-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2281">'Identity'</span></span>
- <span data-ttu-id="23763-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2283">'Razor'</span></span>
- <span data-ttu-id="23763-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2286">'Blazor'</span></span>
- <span data-ttu-id="23763-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2287">'Identity'</span></span>
- <span data-ttu-id="23763-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2289">'Razor'</span></span>
- <span data-ttu-id="23763-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2292">'Blazor'</span></span>
- <span data-ttu-id="23763-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2293">'Identity'</span></span>
- <span data-ttu-id="23763-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2295">'Razor'</span></span>
- <span data-ttu-id="23763-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2298">'Blazor'</span></span>
- <span data-ttu-id="23763-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2299">'Identity'</span></span>
- <span data-ttu-id="23763-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2301">'Razor'</span></span>
- <span data-ttu-id="23763-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2304">'Blazor'</span></span>
- <span data-ttu-id="23763-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2305">'Identity'</span></span>
- <span data-ttu-id="23763-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2307">'Razor'</span></span>
- <span data-ttu-id="23763-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2310">'Blazor'</span></span>
- <span data-ttu-id="23763-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2311">'Identity'</span></span>
- <span data-ttu-id="23763-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2313">'Razor'</span></span>
- <span data-ttu-id="23763-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2316">'Blazor'</span></span>
- <span data-ttu-id="23763-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2317">'Identity'</span></span>
- <span data-ttu-id="23763-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2319">'Razor'</span></span>
- <span data-ttu-id="23763-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2322">'Blazor'</span></span>
- <span data-ttu-id="23763-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2323">'Identity'</span></span>
- <span data-ttu-id="23763-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2325">'Razor'</span></span>
- <span data-ttu-id="23763-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2328">'Blazor'</span></span>
- <span data-ttu-id="23763-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2329">'Identity'</span></span>
- <span data-ttu-id="23763-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2331">'Razor'</span></span>
- <span data-ttu-id="23763-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2334">'Blazor'</span></span>
- <span data-ttu-id="23763-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2335">'Identity'</span></span>
- <span data-ttu-id="23763-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2337">'Razor'</span></span>
- <span data-ttu-id="23763-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2340">'Blazor'</span></span>
- <span data-ttu-id="23763-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2341">'Identity'</span></span>
- <span data-ttu-id="23763-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2343">'Razor'</span></span>
- <span data-ttu-id="23763-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2346">'Blazor'</span></span>
- <span data-ttu-id="23763-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2347">'Identity'</span></span>
- <span data-ttu-id="23763-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2349">'Razor'</span></span>
- <span data-ttu-id="23763-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2352">'Blazor'</span></span>
- <span data-ttu-id="23763-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2353">'Identity'</span></span>
- <span data-ttu-id="23763-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2355">'Razor'</span></span>
- <span data-ttu-id="23763-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2358">'Blazor'</span></span>
- <span data-ttu-id="23763-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2359">'Identity'</span></span>
- <span data-ttu-id="23763-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2361">'Razor'</span></span>
- <span data-ttu-id="23763-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2364">'Blazor'</span></span>
- <span data-ttu-id="23763-2365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2365">'Identity'</span></span>
- <span data-ttu-id="23763-2366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2366">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2367">'Razor'</span></span>
- <span data-ttu-id="23763-2368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2370">'Blazor'</span></span>
- <span data-ttu-id="23763-2371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2371">'Identity'</span></span>
- <span data-ttu-id="23763-2372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2372">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2373">'Razor'</span></span>
- <span data-ttu-id="23763-2374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2374">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts` (スラッシュはエンコードされます)             |   | `/search/{**page}` | `/search/admin/products`                                              |</span><span class="sxs-lookup"><span data-stu-id="23763-2375">----------------------------------- |   | `/search/{*page}`  | `/search/admin%2Fproducts` (the forward slash is encoded)             |   | `/search/{**page}` | `/search/admin/products`                                              |</span></span>

### <a name="middleware-example"></a><span data-ttu-id="23763-2376">ミドルウェアの例</span><span class="sxs-lookup"><span data-stu-id="23763-2376">Middleware example</span></span>

<span data-ttu-id="23763-2377">次の例では、ミドルウェアで <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API を使用して、商品をリストするアクション メソッドへのリンクを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2377">In the following example, a middleware uses the <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API to create link to an action method that lists store products.</span></span> <span data-ttu-id="23763-2378">リンク ジェネレーターは、クラスに挿入し、`GenerateLink` を呼び出すことで、アプリのどのクラスでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-2378">Using the link generator by injecting it into a class and calling `GenerateLink` is available to any class in an app.</span></span>

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a><span data-ttu-id="23763-2379">ルートを作成する</span><span class="sxs-lookup"><span data-stu-id="23763-2379">Create routes</span></span>

<span data-ttu-id="23763-2380">ほとんどのアプリは、<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> を呼び出すか、<xref:Microsoft.AspNetCore.Routing.IRouteBuilder> で定義されている同様の拡張メソッドの 1 つを呼び出してルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2380">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="23763-2381">いずれの <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 拡張メソッドでも、<xref:Microsoft.AspNetCore.Routing.Route> のインスタンスが作成され、それがルート コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2381">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="23763-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> では、ルート ハンドラー パラメーターは受け入れられません。</span><span class="sxs-lookup"><span data-stu-id="23763-2382"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="23763-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> は、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> によって処理されるルートを追加するだけです。</span><span class="sxs-lookup"><span data-stu-id="23763-2383"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="23763-2384">MVC でのルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2384">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="23763-2385">次のコード例は、一般的な ASP.NET Core MVC ルート定義によって使用される <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼び出しの例です。</span><span class="sxs-lookup"><span data-stu-id="23763-2385">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="23763-2386">このテンプレートでは URL パスが照合され、ルート値が抽出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2386">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="23763-2387">たとえば、`/Products/Details/17` というパスでは、`{ controller = Products, action = Details, id = 17 }` というルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2387">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="23763-2388">ルート値は、URL パスをセグメントに分割し、各セグメントと、ルート テンプレートの*ルート パラメーター* 名を照合することで決定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2388">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="23763-2389">ルート パラメーターが指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2389">Route parameters are named.</span></span> <span data-ttu-id="23763-2390">パラメーターは、中かっこ `{ ... }` でパラメーター名を囲むことで定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2390">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="23763-2391">上のテンプレートでは URL パス `/` の照合も行い、`{ controller = Home, action = Index }` という値を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2391">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="23763-2392">これは、ルート パラメーターの `{controller}` と `{action}` に既定値が与えられ、`id` ルート パラメーターが任意であるためです。</span><span class="sxs-lookup"><span data-stu-id="23763-2392">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="23763-2393">ルート パラメーター名の後の等号 (`=`) とそれに続く値により、パラメーターの既定値が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2393">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="23763-2394">ルート パラメーター名の後の疑問符 (`?`) により、オプションのパラメーターが定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2394">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="23763-2395">既定値のルート パラメーターはルートが一致するとルート値を*常に*生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2395">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="23763-2396">省略可能なパラメーターでは、対応する URL パス セグメントがない場合、ルート値は生成されません。</span><span class="sxs-lookup"><span data-stu-id="23763-2396">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="23763-2397">ルート テンプレートのシナリオと構文の詳しい説明については、「[ルート テンプレート参照](#route-template-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2397">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="23763-2398">次の例では、ルート パラメーター定義 `{id:int}` により、`id` ルート パラメーターの[ルート制約](#route-constraint-reference)が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2398">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="23763-2399">このテンプレートは `/Products/Details/Apples` ではなく、`/Products/Details/17` のような URL パスを照合します。</span><span class="sxs-lookup"><span data-stu-id="23763-2399">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="23763-2400">ルート制約は <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を実装し、ルート値を調べて正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="23763-2400">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="23763-2401">この例では、ルート値 `id` は整数に変換できなければなりません。</span><span class="sxs-lookup"><span data-stu-id="23763-2401">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="23763-2402">フレームワークによって提供されるルート制約については、「[ルート制約参照](#route-constraint-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2402">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="23763-2403"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> の追加オーバーロードは、`constraints`、`dataTokens`、`defaults` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="23763-2403">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="23763-2404">このようなパラメーターは一般的に、匿名型のオブジェクトを渡し、匿名型のプロパティ名がルート パラメーター名と一致するというような使われ方をします。</span><span class="sxs-lookup"><span data-stu-id="23763-2404">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="23763-2405">次の <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 例では、同等のルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-2405">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="23763-2406">制約と既定値を定義するインライン構文は単純なルートの場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="23763-2406">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="23763-2407">しかし、インライン構文でサポートされていない、データ トークンなどのシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2407">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="23763-2408">次の例では、その他のいくつかのシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-2408">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="23763-2409">上記のテンプレートでは `/Blog/All-About-Routing/Introduction` のような URL パスを照合し、値 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="23763-2409">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="23763-2410">`controller` と `action` の既定のルート値は、テンプレートに対応するルート パラメーターがなくても、ルートにより生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2410">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="23763-2411">既定値はルート テンプレートで指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-2411">Default values can be specified in the route template.</span></span> <span data-ttu-id="23763-2412">`article` ルート パラメーターは、ルート パラメーター名の前に二重アスタリスク (`**`) があるときに、*キャッチオール* として定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2412">The `article` route parameter is defined as a *catch-all* by the appearance of an double asterisk (`**`) before the route parameter name.</span></span> <span data-ttu-id="23763-2413">キャッチオール ルート パラメーターは、URL パスの残りの部分をキャプチャします。空の文字列も照合できます。</span><span class="sxs-lookup"><span data-stu-id="23763-2413">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="23763-2414">次の例では、ルート制約とデータ トークンを追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-2414">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="23763-2415">上記のテンプレートでは `/en-US/Products/5` のような URL パスを照合し、値 `{ controller = Products, action = Details, id = 5 }` とデータ トークン `{ locale = en-US }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="23763-2415">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![ローカル変数ウィンドウ トークン](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="23763-2417">ルート クラス URL の生成</span><span class="sxs-lookup"><span data-stu-id="23763-2417">Route class URL generation</span></span>

<span data-ttu-id="23763-2418"><xref:Microsoft.AspNetCore.Routing.Route> クラスは、一連のルート値をそのルート テンプレートと組み合わせる方法でも URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-2418">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="23763-2419">論理的には、URL パスの照合とは逆のプロセスになります。</span><span class="sxs-lookup"><span data-stu-id="23763-2419">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="23763-2420">URL 生成を理解する方法として、どのような URL を生成したいのかを考えてください。そして、ルート テンプレートがその URL にどのように照合されるのかを考えてください。</span><span class="sxs-lookup"><span data-stu-id="23763-2420">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="23763-2421">どのような値が生成されるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="23763-2421">What values would be produced?</span></span> <span data-ttu-id="23763-2422"><xref:Microsoft.AspNetCore.Routing.Route> クラスにおける URL 生成のしくみと大体同じになります。</span><span class="sxs-lookup"><span data-stu-id="23763-2422">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="23763-2423">次の例では、一般的な ASP.NET Core MVC の既定のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-2423">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="23763-2424">ルート値が `{ controller = Products, action = List }` の場合、URL `/Products/List` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2424">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="23763-2425">ルート値が対応ルート パラメーターの代替となり、URL パスが作られます。</span><span class="sxs-lookup"><span data-stu-id="23763-2425">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="23763-2426">`id` は任意のルート パラメーターであるため、URL は `id` の値なしで正常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2426">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="23763-2427">ルート値が `{ controller = Home, action = Index }` の場合、URL `/` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2427">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="23763-2428">指定されたルート値は既定値と一致し、その既定値に対応するセグメントを省略しても問題は発生しません。</span><span class="sxs-lookup"><span data-stu-id="23763-2428">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="23763-2429">生成された両方の URL では、次のルート定義 (`/Home/Index` と `/`) でラウンドトリップされ、URL の生成に使用された同じルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2429">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="23763-2430">アプリで ASP.NET Core MVC が使用される場合、ルーティングを直接呼び出す代わりに、<xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> を使用して URL を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2430">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="23763-2431">URL 生成の詳細については、「[URL 生成参照](#url-generation-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2431">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="23763-2432">ルーティング ミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="23763-2432">Use Routing Middleware</span></span>

<span data-ttu-id="23763-2433">アプリのプロジェクト ファイルの [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照します。</span><span class="sxs-lookup"><span data-stu-id="23763-2433">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="23763-2434">`Startup.ConfigureServices` でサービス コンテナーにルーティングを追加した例:</span><span class="sxs-lookup"><span data-stu-id="23763-2434">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="23763-2435">ルートは `Startup.Configure` メソッドで設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2435">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="23763-2436">サンプル アプリでは次の API を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-2436">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="23763-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>:HTTP GET 要求のみを照合します。</span><span class="sxs-lookup"><span data-stu-id="23763-2437"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="23763-2438">下の表は、応答と所与の URI をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-2438">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="23763-2439">URI</span><span class="sxs-lookup"><span data-stu-id="23763-2439">URI</span></span>                    | <span data-ttu-id="23763-2440">応答</span><span class="sxs-lookup"><span data-stu-id="23763-2440">Response</span></span>                                          |
| ---
<span data-ttu-id="23763-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2442">'Blazor'</span></span>
- <span data-ttu-id="23763-2443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2443">'Identity'</span></span>
- <span data-ttu-id="23763-2444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2444">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2445">'Razor'</span></span>
- <span data-ttu-id="23763-2446">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2448">'Blazor'</span></span>
- <span data-ttu-id="23763-2449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2449">'Identity'</span></span>
- <span data-ttu-id="23763-2450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2450">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2451">'Razor'</span></span>
- <span data-ttu-id="23763-2452">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2454">'Blazor'</span></span>
- <span data-ttu-id="23763-2455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2455">'Identity'</span></span>
- <span data-ttu-id="23763-2456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2456">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2457">'Razor'</span></span>
- <span data-ttu-id="23763-2458">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2460">'Blazor'</span></span>
- <span data-ttu-id="23763-2461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2461">'Identity'</span></span>
- <span data-ttu-id="23763-2462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2462">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2463">'Razor'</span></span>
- <span data-ttu-id="23763-2464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2466">'Blazor'</span></span>
- <span data-ttu-id="23763-2467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2467">'Identity'</span></span>
- <span data-ttu-id="23763-2468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2468">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2469">'Razor'</span></span>
- <span data-ttu-id="23763-2470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2472">'Blazor'</span></span>
- <span data-ttu-id="23763-2473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2473">'Identity'</span></span>
- <span data-ttu-id="23763-2474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2474">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2475">'Razor'</span></span>
- <span data-ttu-id="23763-2476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2478">'Blazor'</span></span>
- <span data-ttu-id="23763-2479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2479">'Identity'</span></span>
- <span data-ttu-id="23763-2480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2480">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2481">'Razor'</span></span>
- <span data-ttu-id="23763-2482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2484">'Blazor'</span></span>
- <span data-ttu-id="23763-2485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2485">'Identity'</span></span>
- <span data-ttu-id="23763-2486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2486">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2487">'Razor'</span></span>
- <span data-ttu-id="23763-2488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2490">'Blazor'</span></span>
- <span data-ttu-id="23763-2491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2491">'Identity'</span></span>
- <span data-ttu-id="23763-2492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2492">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2493">'Razor'</span></span>
- <span data-ttu-id="23763-2494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2494">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2495">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2496">'Blazor'</span></span>
- <span data-ttu-id="23763-2497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2497">'Identity'</span></span>
- <span data-ttu-id="23763-2498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2498">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2499">'Razor'</span></span>
- <span data-ttu-id="23763-2500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2502">'Blazor'</span></span>
- <span data-ttu-id="23763-2503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2503">'Identity'</span></span>
- <span data-ttu-id="23763-2504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2504">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2505">'Razor'</span></span>
- <span data-ttu-id="23763-2506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2508">'Blazor'</span></span>
- <span data-ttu-id="23763-2509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2509">'Identity'</span></span>
- <span data-ttu-id="23763-2510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2510">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2511">'Razor'</span></span>
- <span data-ttu-id="23763-2512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2514">'Blazor'</span></span>
- <span data-ttu-id="23763-2515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2515">'Identity'</span></span>
- <span data-ttu-id="23763-2516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2516">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2517">'Razor'</span></span>
- <span data-ttu-id="23763-2518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2520">'Blazor'</span></span>
- <span data-ttu-id="23763-2521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2521">'Identity'</span></span>
- <span data-ttu-id="23763-2522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2522">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2523">'Razor'</span></span>
- <span data-ttu-id="23763-2524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2526">'Blazor'</span></span>
- <span data-ttu-id="23763-2527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2527">'Identity'</span></span>
- <span data-ttu-id="23763-2528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2528">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2529">'Razor'</span></span>
- <span data-ttu-id="23763-2530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2532">'Blazor'</span></span>
- <span data-ttu-id="23763-2533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2533">'Identity'</span></span>
- <span data-ttu-id="23763-2534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2534">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2535">'Razor'</span></span>
- <span data-ttu-id="23763-2536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2538">'Blazor'</span></span>
- <span data-ttu-id="23763-2539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2539">'Identity'</span></span>
- <span data-ttu-id="23763-2540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2540">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2541">'Razor'</span></span>
- <span data-ttu-id="23763-2542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2544">'Blazor'</span></span>
- <span data-ttu-id="23763-2545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2545">'Identity'</span></span>
- <span data-ttu-id="23763-2546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2546">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2547">'Razor'</span></span>
- <span data-ttu-id="23763-2548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2550">'Blazor'</span></span>
- <span data-ttu-id="23763-2551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2551">'Identity'</span></span>
- <span data-ttu-id="23763-2552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2552">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2553">'Razor'</span></span>
- <span data-ttu-id="23763-2554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2556">'Blazor'</span></span>
- <span data-ttu-id="23763-2557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2557">'Identity'</span></span>
- <span data-ttu-id="23763-2558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2558">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2559">'Razor'</span></span>
- <span data-ttu-id="23763-2560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2562">'Blazor'</span></span>
- <span data-ttu-id="23763-2563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2563">'Identity'</span></span>
- <span data-ttu-id="23763-2564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2564">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2565">'Razor'</span></span>
- <span data-ttu-id="23763-2566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2568">'Blazor'</span></span>
- <span data-ttu-id="23763-2569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2569">'Identity'</span></span>
- <span data-ttu-id="23763-2570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2570">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2571">'Razor'</span></span>
- <span data-ttu-id="23763-2572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2574">'Blazor'</span></span>
- <span data-ttu-id="23763-2575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2575">'Identity'</span></span>
- <span data-ttu-id="23763-2576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2576">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2577">'Razor'</span></span>
- <span data-ttu-id="23763-2578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2580">'Blazor'</span></span>
- <span data-ttu-id="23763-2581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2581">'Identity'</span></span>
- <span data-ttu-id="23763-2582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2582">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2583">'Razor'</span></span>
- <span data-ttu-id="23763-2584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2586">'Blazor'</span></span>
- <span data-ttu-id="23763-2587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2587">'Identity'</span></span>
- <span data-ttu-id="23763-2588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2588">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2589">'Razor'</span></span>
- <span data-ttu-id="23763-2590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2592">'Blazor'</span></span>
- <span data-ttu-id="23763-2593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2593">'Identity'</span></span>
- <span data-ttu-id="23763-2594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2594">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2595">'Razor'</span></span>
- <span data-ttu-id="23763-2596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2598">'Blazor'</span></span>
- <span data-ttu-id="23763-2599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2599">'Identity'</span></span>
- <span data-ttu-id="23763-2600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2600">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2601">'Razor'</span></span>
- <span data-ttu-id="23763-2602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2604">'Blazor'</span></span>
- <span data-ttu-id="23763-2605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2605">'Identity'</span></span>
- <span data-ttu-id="23763-2606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2606">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2607">'Razor'</span></span>
- <span data-ttu-id="23763-2608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2610">'Blazor'</span></span>
- <span data-ttu-id="23763-2611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2611">'Identity'</span></span>
- <span data-ttu-id="23763-2612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2612">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2613">'Razor'</span></span>
- <span data-ttu-id="23763-2614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2616">'Blazor'</span></span>
- <span data-ttu-id="23763-2617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2617">'Identity'</span></span>
- <span data-ttu-id="23763-2618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2618">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2619">'Razor'</span></span>
- <span data-ttu-id="23763-2620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2622">'Blazor'</span></span>
- <span data-ttu-id="23763-2623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2623">'Identity'</span></span>
- <span data-ttu-id="23763-2624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2624">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2625">'Razor'</span></span>
- <span data-ttu-id="23763-2626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2626">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2627">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-2627">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="23763-2628">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-2628">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="23763-2629">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-2629">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="23763-2630">Route values: [operation, track], [id, -3] | | `/package/track/`      | 要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-2630">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="23763-2631">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="23763-2631">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="23763-2632">| | `POST /hello/Joe`      | 要求はフォール スルーし、HTTP GET のみに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-2632">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="23763-2633">| | `GET /hello/Joe/Smith` | 要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-2633">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="23763-2634">このフレームワークでは、ルートを作成するために拡張メソッド セット (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2634">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="23763-2635">`Map[Verb]` メソッドは制約を利用し、メソッド名の HTTP Verb にルートを制限します。</span><span class="sxs-lookup"><span data-stu-id="23763-2635">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="23763-2636">たとえば、 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> や <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2636">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="23763-2637">ルート テンプレート参照</span><span class="sxs-lookup"><span data-stu-id="23763-2637">Route template reference</span></span>

<span data-ttu-id="23763-2638">中括弧 (`{ ... }`) 内のトークンは*ルート パラメーター*を定義します。ルートが一致した場合、これがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23763-2638">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="23763-2639">1 つのルート セグメントで複数のルート パラメーターを定義できますが、リテラル値で区切る必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2639">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="23763-2640">たとえば、`{controller=Home}{action=Index}` は有効なルートになりません。`{controller}` と `{action}` の間にリテラル値がないためです。</span><span class="sxs-lookup"><span data-stu-id="23763-2640">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="23763-2641">このようなルート パラメーターには名前を付ける必要があります。付加的な属性を指定することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2641">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="23763-2642">ルート パラメーター以外のリテラル テキスト (`{id}` など) とパス区切り `/` は URL のテキストに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2642">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="23763-2643">テキスト照合は復号された URL パスを基盤とし、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2643">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="23763-2644">リテラル ルート パラメーターの区切り記号 (`{` または `}`) を照合するには、文字を繰り返して区切り記号をエスケープします (`{{` または `}}`)。</span><span class="sxs-lookup"><span data-stu-id="23763-2644">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="23763-2645">任意のファイル拡張子が付いたファイル名のキャプチャを試行する URL パターンには、追加の考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="23763-2645">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="23763-2646">たとえば、テンプレート `files/{filename}.{ext?}` について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-2646">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="23763-2647">`filename` と `ext` の両方の値が存在するときに、両方の値が入力されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2647">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="23763-2648">URL に `filename` の値だけが存在する場合、末尾のピリオド (`.`) は任意であるため、このルートは一致となります。</span><span class="sxs-lookup"><span data-stu-id="23763-2648">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="23763-2649">次の URL はこのルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-2649">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="23763-2650">ルート パラメーターのプレフィックスとしてアスタリスク (`*`) または二重アスタリスク (`**`) を使用し、URI の残りの部分にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="23763-2650">You can use an asterisk (`*`) or double asterisk (`**`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="23763-2651">これらは、*キャッチオール* パラメーターと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="23763-2651">These are called a *catch-all* parameters.</span></span> <span data-ttu-id="23763-2652">たとえば、`blog/{**slug}` は、`/blog` で始まり、その後に (`slug` ルート値に割り当てられる) 任意の値が続くあらゆる URI に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-2652">For example, `blog/{**slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="23763-2653">キャッチオール パラメーターは空の文字列に一致することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2653">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="23763-2654">キャッチオール パラメーターでは、パス区切り (`/`) 文字を含め、URL の生成にルートが使用されるときに適切な文字がエスケープされます。</span><span class="sxs-lookup"><span data-stu-id="23763-2654">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="23763-2655">たとえば、ルート値が `{ path = "my/path" }` のルート `foo/{*path}` では、`foo/my%2Fpath` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2655">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="23763-2656">エスケープされたスラッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2656">Note the escaped forward slash.</span></span> <span data-ttu-id="23763-2657">パス区切り文字をラウンドトリップさせるには、`**` ルート パラメーター プレフィックスを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-2657">To round-trip path separator characters, use the `**` route parameter prefix.</span></span> <span data-ttu-id="23763-2658">`{ path = "my/path" }` のルート `foo/{**path}` では、`foo/my/path` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2658">The route `foo/{**path}` with `{ path = "my/path" }` generates `foo/my/path`.</span></span>

<span data-ttu-id="23763-2659">ルート パラメーターには、*既定値* が含まれることがあります。パラメーター名の後に既定値を指定し、等号 (`=`) で区切ることで指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2659">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="23763-2660">たとえば、`{controller=Home}` では、`controller` の既定値として `Home` が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2660">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="23763-2661">パラメーターの URL に値がない場合、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2661">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="23763-2662">ルート パラメーターは、`id?` のように、パラメーター名の終わりに疑問符 (`?`) を追加することでオプションとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-2662">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="23763-2663">オプション値と既定ルート パラメーターの違いは、既定値を含むルート パラメーターは常に値を生成するのに対し、オプションのパラメーターには、要求 URL によって値が指定されたときにのみ値が与えられることにあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2663">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="23763-2664">ルート パラメーターには、URL からバインドされるルート値に一致しなければならないという制約が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2664">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="23763-2665">コロン (`:`) と制約名をルート パラメーター名の後に追加すると、ルート パラメーターの*インライン制約*が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2665">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="23763-2666">その制約で引数が要求される場合、制約名の後にかっこ (`(...)`) で囲まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-2666">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="23763-2667">別のコロン (`:`) と制約名を追加することで、複数のインライン制約を指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-2667">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="23763-2668">制約名と引数が <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> サービスに渡され、URL 処理で使用する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2668">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="23763-2669">たとえば、ルート テンプレート `blog/{article:minlength(10)}` によって、制約 `minlength` と引数 `10` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2669">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="23763-2670">ルート制約の詳細とこのフレームワークによって指定される制約のリストについては、「[ルート制約参照](#route-constraint-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2670">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="23763-2671">ルート パラメーターには、リンクを生成し、ページおよびアクションを URL と一致させるときにパラメーターの値を変換する、パラメーター トランスフォーマーが含まれている場合もあります。</span><span class="sxs-lookup"><span data-stu-id="23763-2671">Route parameters may also have parameter transformers, which transform a parameter's value when generating links and matching actions and pages to URLs.</span></span> <span data-ttu-id="23763-2672">制約と同様に、パラメーター トランスフォーマーをルート パラメーターにインラインで追加することができます。その場合、ルート パラメーター名の後にコロン (`:`) とトランスフォーマー名を追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-2672">Like constraints, parameter transformers can be added inline to a route parameter by adding a colon (`:`) and transformer name after the route parameter name.</span></span> <span data-ttu-id="23763-2673">たとえば、ルート テンプレート `blog/{article:slugify}` では、`slugify` トランスフォーマーが指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-2673">For example, the route template `blog/{article:slugify}` specifies a `slugify` transformer.</span></span> <span data-ttu-id="23763-2674">パラメーター トランスフォーマーの詳細については、「[パラメーター トランスフォーマー参照](#parameter-transformer-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-2674">For more information on parameter transformers, see the [Parameter transformer reference](#parameter-transformer-reference) section.</span></span>

<span data-ttu-id="23763-2675">次の表は、ルート テンプレートの例とその動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-2675">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="23763-2676">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="23763-2676">Route Template</span></span>                           | <span data-ttu-id="23763-2677">一致する URI の例</span><span class="sxs-lookup"><span data-stu-id="23763-2677">Example Matching URI</span></span>    | <span data-ttu-id="23763-2678">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="23763-2678">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="23763-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2680">'Blazor'</span></span>
- <span data-ttu-id="23763-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2681">'Identity'</span></span>
- <span data-ttu-id="23763-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2683">'Razor'</span></span>
- <span data-ttu-id="23763-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2686">'Blazor'</span></span>
- <span data-ttu-id="23763-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2687">'Identity'</span></span>
- <span data-ttu-id="23763-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2689">'Razor'</span></span>
- <span data-ttu-id="23763-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2692">'Blazor'</span></span>
- <span data-ttu-id="23763-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2693">'Identity'</span></span>
- <span data-ttu-id="23763-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2695">'Razor'</span></span>
- <span data-ttu-id="23763-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2698">'Blazor'</span></span>
- <span data-ttu-id="23763-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2699">'Identity'</span></span>
- <span data-ttu-id="23763-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2701">'Razor'</span></span>
- <span data-ttu-id="23763-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2704">'Blazor'</span></span>
- <span data-ttu-id="23763-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2705">'Identity'</span></span>
- <span data-ttu-id="23763-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2707">'Razor'</span></span>
- <span data-ttu-id="23763-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2710">'Blazor'</span></span>
- <span data-ttu-id="23763-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2711">'Identity'</span></span>
- <span data-ttu-id="23763-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2713">'Razor'</span></span>
- <span data-ttu-id="23763-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2716">'Blazor'</span></span>
- <span data-ttu-id="23763-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2717">'Identity'</span></span>
- <span data-ttu-id="23763-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2719">'Razor'</span></span>
- <span data-ttu-id="23763-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2722">'Blazor'</span></span>
- <span data-ttu-id="23763-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2723">'Identity'</span></span>
- <span data-ttu-id="23763-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2725">'Razor'</span></span>
- <span data-ttu-id="23763-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2728">'Blazor'</span></span>
- <span data-ttu-id="23763-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2729">'Identity'</span></span>
- <span data-ttu-id="23763-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2731">'Razor'</span></span>
- <span data-ttu-id="23763-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2734">'Blazor'</span></span>
- <span data-ttu-id="23763-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2735">'Identity'</span></span>
- <span data-ttu-id="23763-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2737">'Razor'</span></span>
- <span data-ttu-id="23763-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2740">'Blazor'</span></span>
- <span data-ttu-id="23763-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2741">'Identity'</span></span>
- <span data-ttu-id="23763-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2743">'Razor'</span></span>
- <span data-ttu-id="23763-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2746">'Blazor'</span></span>
- <span data-ttu-id="23763-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2747">'Identity'</span></span>
- <span data-ttu-id="23763-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2749">'Razor'</span></span>
- <span data-ttu-id="23763-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2752">'Blazor'</span></span>
- <span data-ttu-id="23763-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2753">'Identity'</span></span>
- <span data-ttu-id="23763-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2755">'Razor'</span></span>
- <span data-ttu-id="23763-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2758">'Blazor'</span></span>
- <span data-ttu-id="23763-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2759">'Identity'</span></span>
- <span data-ttu-id="23763-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2761">'Razor'</span></span>
- <span data-ttu-id="23763-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2764">'Blazor'</span></span>
- <span data-ttu-id="23763-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2765">'Identity'</span></span>
- <span data-ttu-id="23763-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2767">'Razor'</span></span>
- <span data-ttu-id="23763-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2770">'Blazor'</span></span>
- <span data-ttu-id="23763-2771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2771">'Identity'</span></span>
- <span data-ttu-id="23763-2772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2772">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2773">'Razor'</span></span>
- <span data-ttu-id="23763-2774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2776">'Blazor'</span></span>
- <span data-ttu-id="23763-2777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2777">'Identity'</span></span>
- <span data-ttu-id="23763-2778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2778">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2779">'Razor'</span></span>
- <span data-ttu-id="23763-2780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2780">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2781">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2782">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2782">'Blazor'</span></span>
- <span data-ttu-id="23763-2783">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2783">'Identity'</span></span>
- <span data-ttu-id="23763-2784">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2784">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2785">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2785">'Razor'</span></span>
- <span data-ttu-id="23763-2786">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2786">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2787">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2788">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2788">'Blazor'</span></span>
- <span data-ttu-id="23763-2789">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2789">'Identity'</span></span>
- <span data-ttu-id="23763-2790">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2790">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2791">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2791">'Razor'</span></span>
- <span data-ttu-id="23763-2792">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2792">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2793">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2794">'Blazor'</span></span>
- <span data-ttu-id="23763-2795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2795">'Identity'</span></span>
- <span data-ttu-id="23763-2796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2796">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2797">'Razor'</span></span>
- <span data-ttu-id="23763-2798">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2799">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2800">'Blazor'</span></span>
- <span data-ttu-id="23763-2801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2801">'Identity'</span></span>
- <span data-ttu-id="23763-2802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2802">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2803">'Razor'</span></span>
- <span data-ttu-id="23763-2804">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2805">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2806">'Blazor'</span></span>
- <span data-ttu-id="23763-2807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2807">'Identity'</span></span>
- <span data-ttu-id="23763-2808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2808">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2809">'Razor'</span></span>
- <span data-ttu-id="23763-2810">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2811">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2812">'Blazor'</span></span>
- <span data-ttu-id="23763-2813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2813">'Identity'</span></span>
- <span data-ttu-id="23763-2814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2814">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2815">'Razor'</span></span>
- <span data-ttu-id="23763-2816">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2816">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2817">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2818">'Blazor'</span></span>
- <span data-ttu-id="23763-2819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2819">'Identity'</span></span>
- <span data-ttu-id="23763-2820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2820">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2821">'Razor'</span></span>
- <span data-ttu-id="23763-2822">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2823">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2824">'Blazor'</span></span>
- <span data-ttu-id="23763-2825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2825">'Identity'</span></span>
- <span data-ttu-id="23763-2826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2826">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2827">'Razor'</span></span>
- <span data-ttu-id="23763-2828">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2830">'Blazor'</span></span>
- <span data-ttu-id="23763-2831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2831">'Identity'</span></span>
- <span data-ttu-id="23763-2832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2832">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2833">'Razor'</span></span>
- <span data-ttu-id="23763-2834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2836">'Blazor'</span></span>
- <span data-ttu-id="23763-2837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2837">'Identity'</span></span>
- <span data-ttu-id="23763-2838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2838">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2839">'Razor'</span></span>
- <span data-ttu-id="23763-2840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2840">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2841">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2842">'Blazor'</span></span>
- <span data-ttu-id="23763-2843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2843">'Identity'</span></span>
- <span data-ttu-id="23763-2844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2844">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2845">'Razor'</span></span>
- <span data-ttu-id="23763-2846">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2848">'Blazor'</span></span>
- <span data-ttu-id="23763-2849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2849">'Identity'</span></span>
- <span data-ttu-id="23763-2850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2850">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2851">'Razor'</span></span>
- <span data-ttu-id="23763-2852">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2854">'Blazor'</span></span>
- <span data-ttu-id="23763-2855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2855">'Identity'</span></span>
- <span data-ttu-id="23763-2856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2856">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2857">'Razor'</span></span>
- <span data-ttu-id="23763-2858">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2860">'Blazor'</span></span>
- <span data-ttu-id="23763-2861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2861">'Identity'</span></span>
- <span data-ttu-id="23763-2862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2862">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2863">'Razor'</span></span>
- <span data-ttu-id="23763-2864">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2866">'Blazor'</span></span>
- <span data-ttu-id="23763-2867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2867">'Identity'</span></span>
- <span data-ttu-id="23763-2868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2868">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2869">'Razor'</span></span>
- <span data-ttu-id="23763-2870">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2872">'Blazor'</span></span>
- <span data-ttu-id="23763-2873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2873">'Identity'</span></span>
- <span data-ttu-id="23763-2874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2874">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2875">'Razor'</span></span>
- <span data-ttu-id="23763-2876">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2878">'Blazor'</span></span>
- <span data-ttu-id="23763-2879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2879">'Identity'</span></span>
- <span data-ttu-id="23763-2880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2880">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2881">'Razor'</span></span>
- <span data-ttu-id="23763-2882">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2884">'Blazor'</span></span>
- <span data-ttu-id="23763-2885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2885">'Identity'</span></span>
- <span data-ttu-id="23763-2886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2886">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2887">'Razor'</span></span>
- <span data-ttu-id="23763-2888">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2890">'Blazor'</span></span>
- <span data-ttu-id="23763-2891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2891">'Identity'</span></span>
- <span data-ttu-id="23763-2892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2892">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2893">'Razor'</span></span>
- <span data-ttu-id="23763-2894">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2896">'Blazor'</span></span>
- <span data-ttu-id="23763-2897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2897">'Identity'</span></span>
- <span data-ttu-id="23763-2898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2898">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2899">'Razor'</span></span>
- <span data-ttu-id="23763-2900">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2902">'Blazor'</span></span>
- <span data-ttu-id="23763-2903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2903">'Identity'</span></span>
- <span data-ttu-id="23763-2904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2904">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2905">'Razor'</span></span>
- <span data-ttu-id="23763-2906">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2906">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2907">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2908">'Blazor'</span></span>
- <span data-ttu-id="23763-2909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2909">'Identity'</span></span>
- <span data-ttu-id="23763-2910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2910">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2911">'Razor'</span></span>
- <span data-ttu-id="23763-2912">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2914">'Blazor'</span></span>
- <span data-ttu-id="23763-2915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2915">'Identity'</span></span>
- <span data-ttu-id="23763-2916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2916">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2917">'Razor'</span></span>
- <span data-ttu-id="23763-2918">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2920">'Blazor'</span></span>
- <span data-ttu-id="23763-2921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2921">'Identity'</span></span>
- <span data-ttu-id="23763-2922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2922">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2923">'Razor'</span></span>
- <span data-ttu-id="23763-2924">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2926">'Blazor'</span></span>
- <span data-ttu-id="23763-2927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2927">'Identity'</span></span>
- <span data-ttu-id="23763-2928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2928">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2929">'Razor'</span></span>
- <span data-ttu-id="23763-2930">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2932">'Blazor'</span></span>
- <span data-ttu-id="23763-2933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2933">'Identity'</span></span>
- <span data-ttu-id="23763-2934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2934">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2935">'Razor'</span></span>
- <span data-ttu-id="23763-2936">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2938">'Blazor'</span></span>
- <span data-ttu-id="23763-2939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2939">'Identity'</span></span>
- <span data-ttu-id="23763-2940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2940">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2941">'Razor'</span></span>
- <span data-ttu-id="23763-2942">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2944">'Blazor'</span></span>
- <span data-ttu-id="23763-2945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2945">'Identity'</span></span>
- <span data-ttu-id="23763-2946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2946">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2947">'Razor'</span></span>
- <span data-ttu-id="23763-2948">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2950">'Blazor'</span></span>
- <span data-ttu-id="23763-2951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2951">'Identity'</span></span>
- <span data-ttu-id="23763-2952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2952">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2953">'Razor'</span></span>
- <span data-ttu-id="23763-2954">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2956">'Blazor'</span></span>
- <span data-ttu-id="23763-2957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2957">'Identity'</span></span>
- <span data-ttu-id="23763-2958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2958">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2959">'Razor'</span></span>
- <span data-ttu-id="23763-2960">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2962">'Blazor'</span></span>
- <span data-ttu-id="23763-2963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2963">'Identity'</span></span>
- <span data-ttu-id="23763-2964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2964">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2965">'Razor'</span></span>
- <span data-ttu-id="23763-2966">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2968">'Blazor'</span></span>
- <span data-ttu-id="23763-2969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2969">'Identity'</span></span>
- <span data-ttu-id="23763-2970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2970">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2971">'Razor'</span></span>
- <span data-ttu-id="23763-2972">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2974">'Blazor'</span></span>
- <span data-ttu-id="23763-2975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2975">'Identity'</span></span>
- <span data-ttu-id="23763-2976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2976">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2977">'Razor'</span></span>
- <span data-ttu-id="23763-2978">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2980">'Blazor'</span></span>
- <span data-ttu-id="23763-2981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2981">'Identity'</span></span>
- <span data-ttu-id="23763-2982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2982">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2983">'Razor'</span></span>
- <span data-ttu-id="23763-2984">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2986">'Blazor'</span></span>
- <span data-ttu-id="23763-2987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2987">'Identity'</span></span>
- <span data-ttu-id="23763-2988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2988">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2989">'Razor'</span></span>
- <span data-ttu-id="23763-2990">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2992">'Blazor'</span></span>
- <span data-ttu-id="23763-2993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2993">'Identity'</span></span>
- <span data-ttu-id="23763-2994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-2994">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-2995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-2995">'Razor'</span></span>
- <span data-ttu-id="23763-2996">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-2996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-2997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-2998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-2998">'Blazor'</span></span>
- <span data-ttu-id="23763-2999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-2999">'Identity'</span></span>
- <span data-ttu-id="23763-3000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3000">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3001">'Razor'</span></span>
- <span data-ttu-id="23763-3002">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3004">'Blazor'</span></span>
- <span data-ttu-id="23763-3005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3005">'Identity'</span></span>
- <span data-ttu-id="23763-3006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3006">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3007">'Razor'</span></span>
- <span data-ttu-id="23763-3008">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3008">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3009">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3010">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3010">'Blazor'</span></span>
- <span data-ttu-id="23763-3011">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3011">'Identity'</span></span>
- <span data-ttu-id="23763-3012">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3012">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3013">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3013">'Razor'</span></span>
- <span data-ttu-id="23763-3014">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3014">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3015">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3016">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3016">'Blazor'</span></span>
- <span data-ttu-id="23763-3017">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3017">'Identity'</span></span>
- <span data-ttu-id="23763-3018">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3018">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3019">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3019">'Razor'</span></span>
- <span data-ttu-id="23763-3020">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3020">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3021">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3022">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3022">'Blazor'</span></span>
- <span data-ttu-id="23763-3023">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3023">'Identity'</span></span>
- <span data-ttu-id="23763-3024">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3024">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3025">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3025">'Razor'</span></span>
- <span data-ttu-id="23763-3026">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3026">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3027">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3028">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3028">'Blazor'</span></span>
- <span data-ttu-id="23763-3029">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3029">'Identity'</span></span>
- <span data-ttu-id="23763-3030">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3030">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3031">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3031">'Razor'</span></span>
- <span data-ttu-id="23763-3032">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3032">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3033">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3034">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3034">'Blazor'</span></span>
- <span data-ttu-id="23763-3035">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3035">'Identity'</span></span>
- <span data-ttu-id="23763-3036">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3036">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3037">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3037">'Razor'</span></span>
- <span data-ttu-id="23763-3038">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3038">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3039">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3040">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3040">'Blazor'</span></span>
- <span data-ttu-id="23763-3041">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3041">'Identity'</span></span>
- <span data-ttu-id="23763-3042">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3042">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3043">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3043">'Razor'</span></span>
- <span data-ttu-id="23763-3044">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3044">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3045">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3046">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3046">'Blazor'</span></span>
- <span data-ttu-id="23763-3047">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3047">'Identity'</span></span>
- <span data-ttu-id="23763-3048">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3048">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3049">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3049">'Razor'</span></span>
- <span data-ttu-id="23763-3050">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3050">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3051">------------------------------------- | | `hello`                                  | `/hello`                | 単一パス `/hello` にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3051">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="23763-3052">| | `{Page=Home}`                            | `/`                     | 一致し、`Page` が `Home` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3052">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="23763-3053">| | `{Page=Home}`                            | `/Contact`              | 一致し、`Page` が `Contact` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3053">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="23763-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` コントローラーと `List` アクションにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-3054">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="23763-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` コントローラーと `Details` アクションにマッピングされます (`id` は 123 に設定されます)。</span><span class="sxs-lookup"><span data-stu-id="23763-3055">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="23763-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` コントローラーと `Index` メソッドにマッピングされます (`id` は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="23763-3056">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="23763-3057">一般的に、テンプレートの利用が最も簡単なルーティングの手法となります。</span><span class="sxs-lookup"><span data-stu-id="23763-3057">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="23763-3058">ルート テンプレート以外では、制約と既定値も指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3058">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="23763-3059">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3059">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="23763-3060">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="23763-3060">Reserved routing names</span></span>

<span data-ttu-id="23763-3061">次のキーワードは予約名であり、ルート名やパラメーターとして使用できません。</span><span class="sxs-lookup"><span data-stu-id="23763-3061">The following keywords are reserved names and can't be used as route names or parameters:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a><span data-ttu-id="23763-3062">ルート制約参照</span><span class="sxs-lookup"><span data-stu-id="23763-3062">Route constraint reference</span></span>

<span data-ttu-id="23763-3063">ルート制約は、受信 URL と一致し、URL パスがルート値にトークン化されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3063">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="23763-3064">ルート制約では、通常、ルート テンプレート経由で関連付けられるルート値を調べ、値が許容できるかどうかをはい/いいえで決定します。</span><span class="sxs-lookup"><span data-stu-id="23763-3064">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="23763-3065">一部のルート制約では、ルート値以外のデータを使用し、要求をルーティングできるかどうかが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3065">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="23763-3066">たとえば、<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> はその HTTP Verb に基づいて要求を承認または却下します。</span><span class="sxs-lookup"><span data-stu-id="23763-3066">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="23763-3067">制約は、要求のルーティングとリンクの生成で使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3067">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="23763-3068">**入力の検証**には制約を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="23763-3068">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="23763-3069">**入力の検証**に制約が使用されると、無効な入力の結果、*400 - Bad Request* と適切なエラー メッセージではなく、*404 - Not Found* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3069">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="23763-3070">ルート制約は、特定のルートに対する入力の妥当性を検証するためではなく、似たようなルートの**違いを明らかにする**ために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3070">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="23763-3071">次の表は、ルート制約の例とそれに求められる動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-3071">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="23763-3072">制約</span><span class="sxs-lookup"><span data-stu-id="23763-3072">constraint</span></span> | <span data-ttu-id="23763-3073">例</span><span class="sxs-lookup"><span data-stu-id="23763-3073">Example</span></span> | <span data-ttu-id="23763-3074">一致の例</span><span class="sxs-lookup"><span data-stu-id="23763-3074">Example Matches</span></span> | <span data-ttu-id="23763-3075">メモ</span><span class="sxs-lookup"><span data-stu-id="23763-3075">Notes</span></span> |
| ---
<span data-ttu-id="23763-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3076">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3077">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3077">'Blazor'</span></span>
- <span data-ttu-id="23763-3078">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3078">'Identity'</span></span>
- <span data-ttu-id="23763-3079">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3079">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3080">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3080">'Razor'</span></span>
- <span data-ttu-id="23763-3081">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3081">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3083">'Blazor'</span></span>
- <span data-ttu-id="23763-3084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3084">'Identity'</span></span>
- <span data-ttu-id="23763-3085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3085">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3086">'Razor'</span></span>
- <span data-ttu-id="23763-3087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3089">'Blazor'</span></span>
- <span data-ttu-id="23763-3090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3090">'Identity'</span></span>
- <span data-ttu-id="23763-3091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3091">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3092">'Razor'</span></span>
- <span data-ttu-id="23763-3093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3093">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3094">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3095">'Blazor'</span></span>
- <span data-ttu-id="23763-3096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3096">'Identity'</span></span>
- <span data-ttu-id="23763-3097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3097">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3098">'Razor'</span></span>
- <span data-ttu-id="23763-3099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3099">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3100">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3101">'Blazor'</span></span>
- <span data-ttu-id="23763-3102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3102">'Identity'</span></span>
- <span data-ttu-id="23763-3103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3103">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3104">'Razor'</span></span>
- <span data-ttu-id="23763-3105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3107">'Blazor'</span></span>
- <span data-ttu-id="23763-3108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3108">'Identity'</span></span>
- <span data-ttu-id="23763-3109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3109">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3110">'Razor'</span></span>
- <span data-ttu-id="23763-3111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3113">'Blazor'</span></span>
- <span data-ttu-id="23763-3114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3114">'Identity'</span></span>
- <span data-ttu-id="23763-3115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3115">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3116">'Razor'</span></span>
- <span data-ttu-id="23763-3117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3119">'Blazor'</span></span>
- <span data-ttu-id="23763-3120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3120">'Identity'</span></span>
- <span data-ttu-id="23763-3121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3121">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3122">'Razor'</span></span>
- <span data-ttu-id="23763-3123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3125">'Blazor'</span></span>
- <span data-ttu-id="23763-3126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3126">'Identity'</span></span>
- <span data-ttu-id="23763-3127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3127">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3128">'Razor'</span></span>
- <span data-ttu-id="23763-3129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3129">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`、`-123456789` | 任意の整数に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3130">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer.</span></span> <span data-ttu-id="23763-3131">| | `bool` | `{active:bool}` | `true`、`FALSE` | 次に一致します: `true` または `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | URL 生成中、非パラメーター値が提示されるように強制するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3131">| | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false. Case-insensitive. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture. See  preceding warning.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture. See  preceding warning.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture. See  preceding warning.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture. See  preceding warning.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | String has maximum of 8 characters. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and has maximum of 16 characters. |
| `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18. |
| `max(value)` | `{age:max(120)}` | `91` | Integer value maximum of 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 and maximum of 120. |
| `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters `a`-`z`.  Case-insensitive. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression. See tips about defining a regular expression. |
| `required` | `{name:required}` | `Rick\` | Used to enforce that a non-parameter value is present during URL generation.</span></span> |

<span data-ttu-id="23763-3132">コロンで区切られた複数の制約を単一のパラメーターに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-3132">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="23763-3133">たとえば、次の制約では、パラメーターが 1 以上の整数値に制限されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3133">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="23763-3134">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3134">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="23763-3135">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="23763-3135">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="23763-3136">フレームワークから提供されるルート制約がルート値に格納されている値を変更することはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-3136">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="23763-3137">URL から解析されたルート値はすべて文字列として格納されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3137">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="23763-3138">たとえば、`float` 制約はルート値を浮動小数に変換しますが、変換された値は、浮動小数に変換できることを検証するためにだけ利用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3138">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="23763-3139">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-3139">Regular expressions</span></span>

<span data-ttu-id="23763-3140">ASP.NET Core フレームワークでは、正規表現コンストラクターに `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3140">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="23763-3141">これらのメンバーの詳細については、「<xref:System.Text.RegularExpressions.RegexOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3141">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="23763-3142">正規表現では、ルーティングや C# 言語で使用されるものに似た区切り記号とトークンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3142">Regular expressions use delimiters and tokens similar to those used by routing and the C# language.</span></span> <span data-ttu-id="23763-3143">正規表現トークンはエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3143">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="23763-3144">ルーティングで正規表現 `^\d{3}-\d{2}-\d{4}$` を使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="23763-3144">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing:</span></span>

* <span data-ttu-id="23763-3145">表現の場合、文字列内に指定された単一の円記号 `\` 文字を、ソース コード内で二重円記号 `\\` 文字とする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3145">The expression must have the single backslash `\` characters provided in the string as double backslash `\\` characters in the source code.</span></span>
* <span data-ttu-id="23763-3146">正規表現では、文字列エスケープ文字 `\` をエスケープするためには `\\` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3146">The regular expression must us `\\` in order to escape the `\` string escape character.</span></span>
* <span data-ttu-id="23763-3147">正規表現では、[逐語的文字列リテラル](/dotnet/csharp/language-reference/keywords/string)を使用する場合、`\\` を必要としません。</span><span class="sxs-lookup"><span data-stu-id="23763-3147">The regular expression doesn't require `\\` when using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string).</span></span>

<span data-ttu-id="23763-3148">ルーティング パラメーター区切り記号文字である `{`、`}`、`[`、`]` をエスケープするには、`{{`、`}`、`[[`、`]]` のように表現の文字を二重にします。</span><span class="sxs-lookup"><span data-stu-id="23763-3148">To escape routing parameter delimiter characters `{`, `}`, `[`, `]`, double the characters in the expression `{{`, `}`, `[[`, `]]`.</span></span> <span data-ttu-id="23763-3149">次の表に、正規表現とエスケープ適用後のものを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-3149">The following table shows a regular expression and the escaped version:</span></span>

| <span data-ttu-id="23763-3150">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-3150">Regular Expression</span></span>    | <span data-ttu-id="23763-3151">エスケープ適用後の正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-3151">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="23763-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3152">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3153">'Blazor'</span></span>
- <span data-ttu-id="23763-3154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3154">'Identity'</span></span>
- <span data-ttu-id="23763-3155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3155">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3156">'Razor'</span></span>
- <span data-ttu-id="23763-3157">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3158">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3159">'Blazor'</span></span>
- <span data-ttu-id="23763-3160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3160">'Identity'</span></span>
- <span data-ttu-id="23763-3161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3161">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3162">'Razor'</span></span>
- <span data-ttu-id="23763-3163">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3165">'Blazor'</span></span>
- <span data-ttu-id="23763-3166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3166">'Identity'</span></span>
- <span data-ttu-id="23763-3167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3167">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3168">'Razor'</span></span>
- <span data-ttu-id="23763-3169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3171">'Blazor'</span></span>
- <span data-ttu-id="23763-3172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3172">'Identity'</span></span>
- <span data-ttu-id="23763-3173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3173">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3174">'Razor'</span></span>
- <span data-ttu-id="23763-3175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3177">'Blazor'</span></span>
- <span data-ttu-id="23763-3178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3178">'Identity'</span></span>
- <span data-ttu-id="23763-3179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3179">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3180">'Razor'</span></span>
- <span data-ttu-id="23763-3181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3183">'Blazor'</span></span>
- <span data-ttu-id="23763-3184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3184">'Identity'</span></span>
- <span data-ttu-id="23763-3185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3185">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3186">'Razor'</span></span>
- <span data-ttu-id="23763-3187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3188">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3189">'Blazor'</span></span>
- <span data-ttu-id="23763-3190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3190">'Identity'</span></span>
- <span data-ttu-id="23763-3191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3191">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3192">'Razor'</span></span>
- <span data-ttu-id="23763-3193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3195">'Blazor'</span></span>
- <span data-ttu-id="23763-3196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3196">'Identity'</span></span>
- <span data-ttu-id="23763-3197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3197">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3198">'Razor'</span></span>
- <span data-ttu-id="23763-3199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3199">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3200">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3201">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3201">'Blazor'</span></span>
- <span data-ttu-id="23763-3202">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3202">'Identity'</span></span>
- <span data-ttu-id="23763-3203">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3203">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3204">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3204">'Razor'</span></span>
- <span data-ttu-id="23763-3205">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3205">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3206">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3207">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3207">'Blazor'</span></span>
- <span data-ttu-id="23763-3208">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3208">'Identity'</span></span>
- <span data-ttu-id="23763-3209">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3209">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3210">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3210">'Razor'</span></span>
- <span data-ttu-id="23763-3211">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3211">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3212">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3213">'Blazor'</span></span>
- <span data-ttu-id="23763-3214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3214">'Identity'</span></span>
- <span data-ttu-id="23763-3215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3215">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3216">'Razor'</span></span>
- <span data-ttu-id="23763-3217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3218">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3219">'Blazor'</span></span>
- <span data-ttu-id="23763-3220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3220">'Identity'</span></span>
- <span data-ttu-id="23763-3221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3221">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3222">'Razor'</span></span>
- <span data-ttu-id="23763-3223">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3224">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3225">'Blazor'</span></span>
- <span data-ttu-id="23763-3226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3226">'Identity'</span></span>
- <span data-ttu-id="23763-3227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3227">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3228">'Razor'</span></span>
- <span data-ttu-id="23763-3229">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3230">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3231">'Blazor'</span></span>
- <span data-ttu-id="23763-3232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3232">'Identity'</span></span>
- <span data-ttu-id="23763-3233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3233">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3234">'Razor'</span></span>
- <span data-ttu-id="23763-3235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3236">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3237">'Blazor'</span></span>
- <span data-ttu-id="23763-3238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3238">'Identity'</span></span>
- <span data-ttu-id="23763-3239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3239">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3240">'Razor'</span></span>
- <span data-ttu-id="23763-3241">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3243">'Blazor'</span></span>
- <span data-ttu-id="23763-3244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3244">'Identity'</span></span>
- <span data-ttu-id="23763-3245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3245">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3246">'Razor'</span></span>
- <span data-ttu-id="23763-3247">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3249">'Blazor'</span></span>
- <span data-ttu-id="23763-3250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3250">'Identity'</span></span>
- <span data-ttu-id="23763-3251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3251">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3252">'Razor'</span></span>
- <span data-ttu-id="23763-3253">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3255">'Blazor'</span></span>
- <span data-ttu-id="23763-3256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3256">'Identity'</span></span>
- <span data-ttu-id="23763-3257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3257">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3258">'Razor'</span></span>
- <span data-ttu-id="23763-3259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3261">'Blazor'</span></span>
- <span data-ttu-id="23763-3262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3262">'Identity'</span></span>
- <span data-ttu-id="23763-3263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3263">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3264">'Razor'</span></span>
- <span data-ttu-id="23763-3265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3267">'Blazor'</span></span>
- <span data-ttu-id="23763-3268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3268">'Identity'</span></span>
- <span data-ttu-id="23763-3269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3269">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3270">'Razor'</span></span>
- <span data-ttu-id="23763-3271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3273">'Blazor'</span></span>
- <span data-ttu-id="23763-3274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3274">'Identity'</span></span>
- <span data-ttu-id="23763-3275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3275">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3276">'Razor'</span></span>
- <span data-ttu-id="23763-3277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3277">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="23763-3278">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="23763-3279">ルーティングで使用される正規表現は、多くの場合、キャレット文字 `^` で始まり、文字列の開始位置と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3279">Regular expressions used in routing often start with the caret `^` character and match starting position of the string.</span></span> <span data-ttu-id="23763-3280">表現は、多くの場合、ドル記号 `$` で終わり、文字列の末尾と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3280">The expressions often end with the dollar sign `$` character and match end of the string.</span></span> <span data-ttu-id="23763-3281">`^` 文字と `$` 文字により、正規表現はルート パラメーター値全体に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3281">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="23763-3282">`^` 文字と `$` 文字がなければ、正規表現は、文字列内のあらゆる部分文字列に一致します。それは多くの場合、意図に反することです。</span><span class="sxs-lookup"><span data-stu-id="23763-3282">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="23763-3283">下の表では例を示し、それらが一致する、または一致しない理由について説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-3283">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="23763-3284">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-3284">Expression</span></span>   | <span data-ttu-id="23763-3285">String</span><span class="sxs-lookup"><span data-stu-id="23763-3285">String</span></span>    | <span data-ttu-id="23763-3286">一致したもの</span><span class="sxs-lookup"><span data-stu-id="23763-3286">Match</span></span> | <span data-ttu-id="23763-3287">コメント</span><span class="sxs-lookup"><span data-stu-id="23763-3287">Comment</span></span>               |
| ---
<span data-ttu-id="23763-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3289">'Blazor'</span></span>
- <span data-ttu-id="23763-3290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3290">'Identity'</span></span>
- <span data-ttu-id="23763-3291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3291">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3292">'Razor'</span></span>
- <span data-ttu-id="23763-3293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3295">'Blazor'</span></span>
- <span data-ttu-id="23763-3296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3296">'Identity'</span></span>
- <span data-ttu-id="23763-3297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3297">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3298">'Razor'</span></span>
- <span data-ttu-id="23763-3299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3301">'Blazor'</span></span>
- <span data-ttu-id="23763-3302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3302">'Identity'</span></span>
- <span data-ttu-id="23763-3303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3303">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3304">'Razor'</span></span>
- <span data-ttu-id="23763-3305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3307">'Blazor'</span></span>
- <span data-ttu-id="23763-3308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3308">'Identity'</span></span>
- <span data-ttu-id="23763-3309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3309">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3310">'Razor'</span></span>
- <span data-ttu-id="23763-3311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3311">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3312">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3313">'Blazor'</span></span>
- <span data-ttu-id="23763-3314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3314">'Identity'</span></span>
- <span data-ttu-id="23763-3315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3315">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3316">'Razor'</span></span>
- <span data-ttu-id="23763-3317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3319">'Blazor'</span></span>
- <span data-ttu-id="23763-3320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3320">'Identity'</span></span>
- <span data-ttu-id="23763-3321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3321">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3322">'Razor'</span></span>
- <span data-ttu-id="23763-3323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3323">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3324">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3325">'Blazor'</span></span>
- <span data-ttu-id="23763-3326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3326">'Identity'</span></span>
- <span data-ttu-id="23763-3327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3327">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3328">'Razor'</span></span>
- <span data-ttu-id="23763-3329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3331">'Blazor'</span></span>
- <span data-ttu-id="23763-3332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3332">'Identity'</span></span>
- <span data-ttu-id="23763-3333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3333">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3334">'Razor'</span></span>
- <span data-ttu-id="23763-3335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3337">'Blazor'</span></span>
- <span data-ttu-id="23763-3338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3338">'Identity'</span></span>
- <span data-ttu-id="23763-3339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3339">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3340">'Razor'</span></span>
- <span data-ttu-id="23763-3341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3343">'Blazor'</span></span>
- <span data-ttu-id="23763-3344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3344">'Identity'</span></span>
- <span data-ttu-id="23763-3345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3345">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3346">'Razor'</span></span>
- <span data-ttu-id="23763-3347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3349">'Blazor'</span></span>
- <span data-ttu-id="23763-3350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3350">'Identity'</span></span>
- <span data-ttu-id="23763-3351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3351">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3352">'Razor'</span></span>
- <span data-ttu-id="23763-3353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3355">'Blazor'</span></span>
- <span data-ttu-id="23763-3356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3356">'Identity'</span></span>
- <span data-ttu-id="23763-3357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3357">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3358">'Razor'</span></span>
- <span data-ttu-id="23763-3359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3361">'Blazor'</span></span>
- <span data-ttu-id="23763-3362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3362">'Identity'</span></span>
- <span data-ttu-id="23763-3363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3363">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3364">'Razor'</span></span>
- <span data-ttu-id="23763-3365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3367">'Blazor'</span></span>
- <span data-ttu-id="23763-3368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3368">'Identity'</span></span>
- <span data-ttu-id="23763-3369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3369">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3370">'Razor'</span></span>
- <span data-ttu-id="23763-3371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3371">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3372">---------- | | `[a-z]{2}`   | hello     | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | 123abc456 | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | mz        | はい   | 式が一致します    | | `[a-z]{2}`   | MZ        | はい   | 大文字と小文字は区別されません    | | `^[a-z]{2}$` | hello     | いいえ    | 上の `^` と `$` を参照してください | | `^[a-z]{2}$` | 123abc456 | いいえ    | 上の `^` と `$` を参照してください |</span><span class="sxs-lookup"><span data-stu-id="23763-3372">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="23763-3373">正規表現構文の詳細については、[.NET Framework 正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3373">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="23763-3374">既知の入力可能値の集まりにパラメーターを制限するには、正規表現を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-3374">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="23763-3375">たとえば、`{action:regex(^(list|get|create)$)}` の場合、`action` ルート値は `list`、`get`、`create` とのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3375">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="23763-3376">制約ディクショナリに渡された場合、文字列 `^(list|get|create)$` で同じものになります。</span><span class="sxs-lookup"><span data-stu-id="23763-3376">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="23763-3377">(テンプレート内でインラインではなく) 制約ディクショナリに渡された制約が既知の制約に一致しない場合も、正規表現として扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-3377">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="23763-3378">カスタム ルート制約</span><span class="sxs-lookup"><span data-stu-id="23763-3378">Custom route constraints</span></span>

<span data-ttu-id="23763-3379">組み込みのルート制約だけでなく、<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスを実装してカスタム ルート制約を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-3379">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="23763-3380"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスには、1 つのメソッド `Match` が含まれています。これは、制約が満たされている場合は `true` を、それ以外の場合は `false` を返します。</span><span class="sxs-lookup"><span data-stu-id="23763-3380">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="23763-3381">カスタムの <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を使うには、アプリのサービス コンテナー内にあるアプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> に、ルート制約の種類を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3381">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="23763-3382"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、ルート制約キーを、その制約を検証する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> の実装にマッピングするディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-3382">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="23763-3383">アプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、`Startup.ConfigureServices` で、[services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼び出しの一部として、または `services.Configure<RouteOptions>` を使って <xref:Microsoft.AspNetCore.Routing.RouteOptions> を直接構成することで、更新できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3383">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="23763-3384">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-3384">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="23763-3385">これで、制約の種類を登録するときに指定した名前を使って、通常の方法でルートに制約を適用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3385">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="23763-3386">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-3386">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a><span data-ttu-id="23763-3387">パラメーター トランスフォーマー参照</span><span class="sxs-lookup"><span data-stu-id="23763-3387">Parameter transformer reference</span></span>

<span data-ttu-id="23763-3388">パラメーター トランスフォーマー:</span><span class="sxs-lookup"><span data-stu-id="23763-3388">Parameter transformers:</span></span>

* <span data-ttu-id="23763-3389"><xref:Microsoft.AspNetCore.Routing.Route> のリンクの生成時に実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3389">Execute when generating a link for a <xref:Microsoft.AspNetCore.Routing.Route>.</span></span>
* <span data-ttu-id="23763-3390">`Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`を実装します。</span><span class="sxs-lookup"><span data-stu-id="23763-3390">Implement `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.</span></span>
* <span data-ttu-id="23763-3391"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3391">Are configured using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.</span></span>
* <span data-ttu-id="23763-3392">パラメーターのルート値を取得し、それを新しい文字列値に変換します。</span><span class="sxs-lookup"><span data-stu-id="23763-3392">Take the parameter's route value and transform it to a new string value.</span></span>
* <span data-ttu-id="23763-3393">変換された値は生成されたリンクで使用されるようになります。</span><span class="sxs-lookup"><span data-stu-id="23763-3393">Result in using the transformed value in the generated link.</span></span>

<span data-ttu-id="23763-3394">たとえば、`Url.Action(new { article = "MyTestArticle" })` のルート パターン `blog\{article:slugify}` のカスタム `slugify` パラメーター トランスフォーマーでは、`blog\my-test-article` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3394">For example, a custom `slugify` parameter transformer in route pattern `blog\{article:slugify}` with `Url.Action(new { article = "MyTestArticle" })` generates `blog\my-test-article`.</span></span>

<span data-ttu-id="23763-3395">ルート パターンでパラメーター トランスフォーマーを使用するには、まず `Startup.ConfigureServices` で <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> を使用してこれを構成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3395">To use a parameter transformer in a route pattern, configure it first using <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

<span data-ttu-id="23763-3396">パラメーター トランスフォーマーは、エンドポイントが解決される URI を変換するためにフレームワークで使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3396">Parameter transformers are used by the framework to transform the URI where an endpoint resolves.</span></span> <span data-ttu-id="23763-3397">たとえば、ASP.NET Core MVC ではパラメーター トランスフォーマーを使用して、`area`、`controller`、`action`、`page` を照合するために使用されるルート値を変換します。</span><span class="sxs-lookup"><span data-stu-id="23763-3397">For example, ASP.NET Core MVC uses parameter transformers to transform the route value used to match an `area`, `controller`, `action`, and `page`.</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

<span data-ttu-id="23763-3398">上記のルートでは、アクション `SubscriptionManagementController.GetAll` は URI `/subscription-management/get-all` と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-3398">With the preceding route, the action `SubscriptionManagementController.GetAll` is matched with the URI `/subscription-management/get-all`.</span></span> <span data-ttu-id="23763-3399">パラメーター トランスフォーマーでは、リンクを生成するために使用されるルート値は変更されません。</span><span class="sxs-lookup"><span data-stu-id="23763-3399">A parameter transformer doesn't change the route values used to generate a link.</span></span> <span data-ttu-id="23763-3400">たとえば、`Url.Action("GetAll", "SubscriptionManagement")` では `/subscription-management/get-all` が出力されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3400">For example, `Url.Action("GetAll", "SubscriptionManagement")` outputs `/subscription-management/get-all`.</span></span>

<span data-ttu-id="23763-3401">ASP.NET Core では、生成されたルートと共にパラメーター トランスフォーマーを使用するための API 規則が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3401">ASP.NET Core provides API conventions for using a parameter transformers with generated routes:</span></span>

* <span data-ttu-id="23763-3402">ASP.NET Core MVC には、`Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 規則が備わっています。</span><span class="sxs-lookup"><span data-stu-id="23763-3402">ASP.NET Core MVC has the `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API convention.</span></span> <span data-ttu-id="23763-3403">この規則では、指定されたパラメーター トランスフォーマーがアプリ内のすべての属性ルートに適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3403">This convention applies a specified parameter transformer to all attribute routes in the app.</span></span> <span data-ttu-id="23763-3404">パラメーター トランスフォーマーでは、置き換えられる属性ルート トークンが変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3404">The parameter transformer transforms attribute route tokens as they are replaced.</span></span> <span data-ttu-id="23763-3405">詳細については、「[パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-3405">For more information, see [Use a parameter transformer to customize token replacement](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).</span></span>
* Razor<span data-ttu-id="23763-3406"> Pages には、`Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 規則があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3406"> Pages has the `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API convention.</span></span> <span data-ttu-id="23763-3407">この規則では、指定されたパラメーター トランスフォーマーが、自動で検出されたすべての Razor Pages に適用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3407">This convention applies a specified parameter transformer to all automatically discovered Razor Pages.</span></span> <span data-ttu-id="23763-3408">パラメーター トランスフォーマーでは、Razor Pages ルートのフォルダーとファイル名のセグメントが変換されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3408">The parameter transformer transforms the folder and file name segments of Razor Pages routes.</span></span> <span data-ttu-id="23763-3409">詳細については、[パラメーター トランスフォーマーを使用したページ ルートのカスタマイズ](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-3409">For more information, see [Use a parameter transformer to customize page routes](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).</span></span>

## <a name="url-generation-reference"></a><span data-ttu-id="23763-3410">URL 生成参照</span><span class="sxs-lookup"><span data-stu-id="23763-3410">URL generation reference</span></span>

<span data-ttu-id="23763-3411">次の例では、ルートのリンクを生成する方法を確認できます。ルート値のディクショナリと <xref:Microsoft.AspNetCore.Routing.RouteCollection> が指定されています。</span><span class="sxs-lookup"><span data-stu-id="23763-3411">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="23763-3412">上のサンプルの終わりで生成された <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> は `/package/create/123` です。</span><span class="sxs-lookup"><span data-stu-id="23763-3412">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="23763-3413">ディクショナリにより、"Track Package Route" テンプレート `package/{operation}/{id}` のルート値 `operation` と `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3413">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="23763-3414">詳細については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションのサンプル コードまたは[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3414">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="23763-3415"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> コンストラクターの 2 番目のパラメーターは*アンビエント値*の集合です。</span><span class="sxs-lookup"><span data-stu-id="23763-3415">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="23763-3416">アンビエント値は、開発者が要求コンテキスト内で指定する必要がある値の数が制限されるため、使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="23763-3416">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="23763-3417">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-3417">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="23763-3418">ASP.NET Core MVC アプリの `HomeController` の `About` アクションでは、コントローラー ルート値を指定し、`Index` アクションにリンクする必要はありません。`Home` のアンビエント値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3418">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="23763-3419">パラメーターに一致しないアンビエント値は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3419">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="23763-3420">アンビエント値は、明示的に指定された値でアンビエント値がオーバーライドされる場合にも無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3420">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="23763-3421">URL では左から右に照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3421">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="23763-3422">明示的に指定されているが、ルートのセグメントと一致しない値は、クエリ文字列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3422">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="23763-3423">次の表は、ルート テンプレート `{controller}/{action}/{id?}` の使用時の結果をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-3423">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="23763-3424">アンビエント値</span><span class="sxs-lookup"><span data-stu-id="23763-3424">Ambient Values</span></span>                     | <span data-ttu-id="23763-3425">明示的な値</span><span class="sxs-lookup"><span data-stu-id="23763-3425">Explicit Values</span></span>                        | <span data-ttu-id="23763-3426">結果</span><span class="sxs-lookup"><span data-stu-id="23763-3426">Result</span></span>                  |
| ---
<span data-ttu-id="23763-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3428">'Blazor'</span></span>
- <span data-ttu-id="23763-3429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3429">'Identity'</span></span>
- <span data-ttu-id="23763-3430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3430">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3431">'Razor'</span></span>
- <span data-ttu-id="23763-3432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3434">'Blazor'</span></span>
- <span data-ttu-id="23763-3435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3435">'Identity'</span></span>
- <span data-ttu-id="23763-3436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3436">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3437">'Razor'</span></span>
- <span data-ttu-id="23763-3438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3440">'Blazor'</span></span>
- <span data-ttu-id="23763-3441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3441">'Identity'</span></span>
- <span data-ttu-id="23763-3442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3442">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3443">'Razor'</span></span>
- <span data-ttu-id="23763-3444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3446">'Blazor'</span></span>
- <span data-ttu-id="23763-3447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3447">'Identity'</span></span>
- <span data-ttu-id="23763-3448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3448">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3449">'Razor'</span></span>
- <span data-ttu-id="23763-3450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3452">'Blazor'</span></span>
- <span data-ttu-id="23763-3453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3453">'Identity'</span></span>
- <span data-ttu-id="23763-3454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3454">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3455">'Razor'</span></span>
- <span data-ttu-id="23763-3456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3458">'Blazor'</span></span>
- <span data-ttu-id="23763-3459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3459">'Identity'</span></span>
- <span data-ttu-id="23763-3460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3460">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3461">'Razor'</span></span>
- <span data-ttu-id="23763-3462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3464">'Blazor'</span></span>
- <span data-ttu-id="23763-3465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3465">'Identity'</span></span>
- <span data-ttu-id="23763-3466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3466">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3467">'Razor'</span></span>
- <span data-ttu-id="23763-3468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3470">'Blazor'</span></span>
- <span data-ttu-id="23763-3471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3471">'Identity'</span></span>
- <span data-ttu-id="23763-3472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3472">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3473">'Razor'</span></span>
- <span data-ttu-id="23763-3474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3476">'Blazor'</span></span>
- <span data-ttu-id="23763-3477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3477">'Identity'</span></span>
- <span data-ttu-id="23763-3478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3478">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3479">'Razor'</span></span>
- <span data-ttu-id="23763-3480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3482">'Blazor'</span></span>
- <span data-ttu-id="23763-3483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3483">'Identity'</span></span>
- <span data-ttu-id="23763-3484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3484">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3485">'Razor'</span></span>
- <span data-ttu-id="23763-3486">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3488">'Blazor'</span></span>
- <span data-ttu-id="23763-3489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3489">'Identity'</span></span>
- <span data-ttu-id="23763-3490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3490">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3491">'Razor'</span></span>
- <span data-ttu-id="23763-3492">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3492">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3493">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3494">'Blazor'</span></span>
- <span data-ttu-id="23763-3495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3495">'Identity'</span></span>
- <span data-ttu-id="23763-3496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3496">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3497">'Razor'</span></span>
- <span data-ttu-id="23763-3498">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3498">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3499">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3500">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3500">'Blazor'</span></span>
- <span data-ttu-id="23763-3501">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3501">'Identity'</span></span>
- <span data-ttu-id="23763-3502">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3502">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3503">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3503">'Razor'</span></span>
- <span data-ttu-id="23763-3504">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3504">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3505">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3506">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3506">'Blazor'</span></span>
- <span data-ttu-id="23763-3507">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3507">'Identity'</span></span>
- <span data-ttu-id="23763-3508">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3508">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3509">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3509">'Razor'</span></span>
- <span data-ttu-id="23763-3510">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3510">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3511">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3512">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3512">'Blazor'</span></span>
- <span data-ttu-id="23763-3513">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3513">'Identity'</span></span>
- <span data-ttu-id="23763-3514">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3514">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3515">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3515">'Razor'</span></span>
- <span data-ttu-id="23763-3516">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3516">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3517">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3518">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3518">'Blazor'</span></span>
- <span data-ttu-id="23763-3519">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3519">'Identity'</span></span>
- <span data-ttu-id="23763-3520">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3520">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3521">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3521">'Razor'</span></span>
- <span data-ttu-id="23763-3522">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3522">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3523">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3524">'Blazor'</span></span>
- <span data-ttu-id="23763-3525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3525">'Identity'</span></span>
- <span data-ttu-id="23763-3526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3526">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3527">'Razor'</span></span>
- <span data-ttu-id="23763-3528">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3529">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3530">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3530">'Blazor'</span></span>
- <span data-ttu-id="23763-3531">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3531">'Identity'</span></span>
- <span data-ttu-id="23763-3532">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3532">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3533">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3533">'Razor'</span></span>
- <span data-ttu-id="23763-3534">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3534">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3535">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3536">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3536">'Blazor'</span></span>
- <span data-ttu-id="23763-3537">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3537">'Identity'</span></span>
- <span data-ttu-id="23763-3538">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3538">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3539">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3539">'Razor'</span></span>
- <span data-ttu-id="23763-3540">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3540">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3542">'Blazor'</span></span>
- <span data-ttu-id="23763-3543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3543">'Identity'</span></span>
- <span data-ttu-id="23763-3544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3544">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3545">'Razor'</span></span>
- <span data-ttu-id="23763-3546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3548">'Blazor'</span></span>
- <span data-ttu-id="23763-3549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3549">'Identity'</span></span>
- <span data-ttu-id="23763-3550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3550">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3551">'Razor'</span></span>
- <span data-ttu-id="23763-3552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3554">'Blazor'</span></span>
- <span data-ttu-id="23763-3555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3555">'Identity'</span></span>
- <span data-ttu-id="23763-3556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3556">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3557">'Razor'</span></span>
- <span data-ttu-id="23763-3558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3560">'Blazor'</span></span>
- <span data-ttu-id="23763-3561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3561">'Identity'</span></span>
- <span data-ttu-id="23763-3562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3562">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3563">'Razor'</span></span>
- <span data-ttu-id="23763-3564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3566">'Blazor'</span></span>
- <span data-ttu-id="23763-3567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3567">'Identity'</span></span>
- <span data-ttu-id="23763-3568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3568">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3569">'Razor'</span></span>
- <span data-ttu-id="23763-3570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3572">'Blazor'</span></span>
- <span data-ttu-id="23763-3573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3573">'Identity'</span></span>
- <span data-ttu-id="23763-3574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3574">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3575">'Razor'</span></span>
- <span data-ttu-id="23763-3576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3578">'Blazor'</span></span>
- <span data-ttu-id="23763-3579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3579">'Identity'</span></span>
- <span data-ttu-id="23763-3580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3580">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3581">'Razor'</span></span>
- <span data-ttu-id="23763-3582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3584">'Blazor'</span></span>
- <span data-ttu-id="23763-3585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3585">'Identity'</span></span>
- <span data-ttu-id="23763-3586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3586">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3587">'Razor'</span></span>
- <span data-ttu-id="23763-3588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3590">'Blazor'</span></span>
- <span data-ttu-id="23763-3591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3591">'Identity'</span></span>
- <span data-ttu-id="23763-3592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3592">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3593">'Razor'</span></span>
- <span data-ttu-id="23763-3594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3594">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3595">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3596">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3596">'Blazor'</span></span>
- <span data-ttu-id="23763-3597">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3597">'Identity'</span></span>
- <span data-ttu-id="23763-3598">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3598">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3599">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3599">'Razor'</span></span>
- <span data-ttu-id="23763-3600">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3600">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3602">'Blazor'</span></span>
- <span data-ttu-id="23763-3603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3603">'Identity'</span></span>
- <span data-ttu-id="23763-3604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3604">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3605">'Razor'</span></span>
- <span data-ttu-id="23763-3606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3608">'Blazor'</span></span>
- <span data-ttu-id="23763-3609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3609">'Identity'</span></span>
- <span data-ttu-id="23763-3610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3610">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3611">'Razor'</span></span>
- <span data-ttu-id="23763-3612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3614">'Blazor'</span></span>
- <span data-ttu-id="23763-3615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3615">'Identity'</span></span>
- <span data-ttu-id="23763-3616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3616">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3617">'Razor'</span></span>
- <span data-ttu-id="23763-3618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3618">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3619">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3620">'Blazor'</span></span>
- <span data-ttu-id="23763-3621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3621">'Identity'</span></span>
- <span data-ttu-id="23763-3622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3622">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3623">'Razor'</span></span>
- <span data-ttu-id="23763-3624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3626">'Blazor'</span></span>
- <span data-ttu-id="23763-3627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3627">'Identity'</span></span>
- <span data-ttu-id="23763-3628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3628">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3629">'Razor'</span></span>
- <span data-ttu-id="23763-3630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3632">'Blazor'</span></span>
- <span data-ttu-id="23763-3633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3633">'Identity'</span></span>
- <span data-ttu-id="23763-3634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3634">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3635">'Razor'</span></span>
- <span data-ttu-id="23763-3636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3638">'Blazor'</span></span>
- <span data-ttu-id="23763-3639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3639">'Identity'</span></span>
- <span data-ttu-id="23763-3640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3640">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3641">'Razor'</span></span>
- <span data-ttu-id="23763-3642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3644">'Blazor'</span></span>
- <span data-ttu-id="23763-3645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3645">'Identity'</span></span>
- <span data-ttu-id="23763-3646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3646">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3647">'Razor'</span></span>
- <span data-ttu-id="23763-3648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3650">'Blazor'</span></span>
- <span data-ttu-id="23763-3651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3651">'Identity'</span></span>
- <span data-ttu-id="23763-3652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3652">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3653">'Razor'</span></span>
- <span data-ttu-id="23763-3654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3656">'Blazor'</span></span>
- <span data-ttu-id="23763-3657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3657">'Identity'</span></span>
- <span data-ttu-id="23763-3658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3658">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3659">'Razor'</span></span>
- <span data-ttu-id="23763-3660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3662">'Blazor'</span></span>
- <span data-ttu-id="23763-3663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3663">'Identity'</span></span>
- <span data-ttu-id="23763-3664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3664">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3665">'Razor'</span></span>
- <span data-ttu-id="23763-3666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3668">'Blazor'</span></span>
- <span data-ttu-id="23763-3669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3669">'Identity'</span></span>
- <span data-ttu-id="23763-3670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3670">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3671">'Razor'</span></span>
- <span data-ttu-id="23763-3672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3672">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3673">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order"、action = "About" | `/Order/About`          |
| controller = "Home"、color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About"、color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="23763-3673">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="23763-3674">パラメーターに対応しない既定値がルートにあり、その値が明示的に指定される場合、それは既定値に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3674">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="23763-3675">リンク生成では、`controller` と `action` について一致する値が指定されるときにのみ、このルートのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3675">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="23763-3676">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="23763-3676">Complex segments</span></span>

<span data-ttu-id="23763-3677">複雑なセグメント (例: `[Route("/x{token}y")]`) は、リテラルを右から左に最短一致の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3677">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="23763-3678">複雑なセグメントの一致方法に関する詳しい説明については、[こちらのコード](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-3678">See [this code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="23763-3679">この[コード サンプル](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)は ASP.NET Core では使われていませんが、複雑なセグメントに関する優れた説明が提供されています。</span><span class="sxs-lookup"><span data-stu-id="23763-3679">The [code sample](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="23763-3680">ルーティングでは要求 URI をルート ハンドラーにマッピングし、受信要求を配布します。</span><span class="sxs-lookup"><span data-stu-id="23763-3680">Routing is responsible for mapping request URIs to route handlers and dispatching an incoming requests.</span></span> <span data-ttu-id="23763-3681">ルートはアプリに定義され、アプリの起動時に構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3681">Routes are defined in the app and configured when the app starts.</span></span> <span data-ttu-id="23763-3682">ルートは、要求に含まれている URL から値を任意で抽出できます。その値を要求処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3682">A route can optionally extract values from the URL contained in the request, and these values can then be used for request processing.</span></span> <span data-ttu-id="23763-3683">ルーティングでは、アプリからの構成済みルートを使用して、ルート ハンドラーにマッピングする URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3683">Using configured routes from the app, routing is able to generate URLs that map to route handlers.</span></span>

<span data-ttu-id="23763-3684">ASP.NET Core 2.1 で最新のルーティング シナリオを使用するには、`Startup.ConfigureServices` で[互換性バージョン](xref:mvc/compatibility-version)を MVC サービス登録に指定します。</span><span class="sxs-lookup"><span data-stu-id="23763-3684">To use the latest routing scenarios in ASP.NET Core 2.1, specify the [compatibility version](xref:mvc/compatibility-version) to the MVC services registration in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> <span data-ttu-id="23763-3685">本文では、ASP.NET Core ルーティングについて詳しく取り上げます。</span><span class="sxs-lookup"><span data-stu-id="23763-3685">This document covers low-level ASP.NET Core routing.</span></span> <span data-ttu-id="23763-3686">ASP.NET Core MVC ルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3686">For information on ASP.NET Core MVC routing, see <xref:mvc/controllers/routing>.</span></span> <span data-ttu-id="23763-3687">Razor Pages のルーティング規則については、「<xref:razor-pages/razor-pages-conventions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3687">For information on routing conventions in Razor Pages, see <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="23763-3688">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="23763-3688">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="routing-basics"></a><span data-ttu-id="23763-3689">ルーティングの基本</span><span class="sxs-lookup"><span data-stu-id="23763-3689">Routing basics</span></span>

<span data-ttu-id="23763-3690">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3690">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="23763-3691">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="23763-3691">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="23763-3692">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="23763-3692">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="23763-3693">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="23763-3693">Is a useful starting point for UI-based apps.</span></span>

<span data-ttu-id="23763-3694">開発者は一般的に、特殊な状況でのアプリのトラフィックが多い領域 (ブログや E コマース エンドポイントなど) に対しては、[属性ルーティング](xref:mvc/controllers/routing#attribute-routing)または専用規則ルートを使用して簡易ルートをさらに追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-3694">Developers commonly add additional terse routes to high-traffic areas of an app in specialized situations (for example, blog and ecommerce endpoints) using [attribute routing](xref:mvc/controllers/routing#attribute-routing) or dedicated conventional routes.</span></span>

<span data-ttu-id="23763-3695">Web API では、属性ルーティングを使用して、HTTP 動詞で操作を表現するリソースのセットとしてアプリの機能をモデル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3695">Web APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="23763-3696">つまり、同じ論理リソース上の多くの操作 (たとえば GET や POST) で、同じ URL を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-3696">This means that many operations (for example, GET, POST) on the same logical resource will use the same URL.</span></span> <span data-ttu-id="23763-3697">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3697">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

Razor<span data-ttu-id="23763-3698"> Pages アプリでは、既定の規則ルーティングを使用して、アプリの *Pages* フォルダーの名前付きリソースを提供します。</span><span class="sxs-lookup"><span data-stu-id="23763-3698"> Pages apps use default conventional routing to serve named resources in the *Pages* folder of an app.</span></span> <span data-ttu-id="23763-3699">Razor Pages のルーティング動作をカスタマイズできる追加の規則を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3699">Additional conventions are available that allow you to customize Razor Pages routing behavior.</span></span> <span data-ttu-id="23763-3700">詳細については、次のトピックを参照してください。 <xref:razor-pages/index> および <xref:razor-pages/razor-pages-conventions></span><span class="sxs-lookup"><span data-stu-id="23763-3700">For more information, see <xref:razor-pages/index> and <xref:razor-pages/razor-pages-conventions>.</span></span>

<span data-ttu-id="23763-3701">URL 生成サポートを使用すると、アプリを相互にリンクする URL をハード コーディングすることなくアプリを開発できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3701">URL generation support allows the app to be developed without hard-coding URLs to link the app together.</span></span> <span data-ttu-id="23763-3702">このサポートにより、基本的なルーティング構成を使用して作業を開始し、アプリのリソース レイアウトが決まった後でルートを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-3702">This support allows for starting with a basic routing configuration and modifying the routes after the app's resource layout is determined.</span></span>

<span data-ttu-id="23763-3703">ルーティングでは <xref:Microsoft.AspNetCore.Routing.IRouter> のルートの実装が次の目的で利用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3703">Routing uses routes implementations of <xref:Microsoft.AspNetCore.Routing.IRouter> to:</span></span>

* <span data-ttu-id="23763-3704">受信要求を*ルート ハンドラー*にマッピングする。</span><span class="sxs-lookup"><span data-stu-id="23763-3704">Map incoming requests to *route handlers*.</span></span>
* <span data-ttu-id="23763-3705">応答で使用される URL を生成する。</span><span class="sxs-lookup"><span data-stu-id="23763-3705">Generate the URLs used in responses.</span></span>

<span data-ttu-id="23763-3706">既定では、アプリにはルート コレクションが 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="23763-3706">By default, an app has a single collection of routes.</span></span> <span data-ttu-id="23763-3707">要求が到着すると、コレクション内のルートは、コレクションに存在する順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3707">When a request arrives, the routes in the collection are processed in the order that they exist in the collection.</span></span> <span data-ttu-id="23763-3708">フレームワークでは、コレクション内の各ルートに対して <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> メソッドを呼び出し、受信要求 URL とコレクション内のルートとの照合を試みます。</span><span class="sxs-lookup"><span data-stu-id="23763-3708">The framework attempts to match an incoming request URL to a route in the collection by calling the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in the collection.</span></span> <span data-ttu-id="23763-3709">応答ではルーティングを使用し、ルート情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="23763-3709">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>

<span data-ttu-id="23763-3710">ルーティング システムには次の特性があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3710">The routing system has the following characteristics:</span></span>

* <span data-ttu-id="23763-3711">ルート テンプレート構文は、トークン化されたルート パラメーターでルートを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3711">Route template syntax is used to define routes with tokenized route parameters.</span></span>
* <span data-ttu-id="23763-3712">規則スタイルおよび属性スタイルのエンドポイント構成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3712">Conventional-style and attribute-style endpoint configuration is permitted.</span></span>
* <span data-ttu-id="23763-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> は、URL パラメーターに特定のエンドポイント制約で有効な値が含まれているかどうかを確認するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3713"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> is used to determine whether a URL parameter contains a valid value for a given endpoint constraint.</span></span>
* <span data-ttu-id="23763-3714">MVC/Razor Pages などのアプリ モデルでは、ルーティング シナリオの予測可能な実装を持つ、すべてのルートを登録します。</span><span class="sxs-lookup"><span data-stu-id="23763-3714">App models, such as MVC/Razor Pages, register all of their routes, which have a predictable implementation of routing scenarios.</span></span>
* <span data-ttu-id="23763-3715">応答ではルーティングを使用し、ルート情報に基づいて URL (リダイレクトやリンクなど) を生成できます。そのため、URL をハードコーディングする必要がなく、保守管理が容易になります。</span><span class="sxs-lookup"><span data-stu-id="23763-3715">A response can use routing to generate URLs (for example, for redirection or links) based on route information and thus avoid hard-coded URLs, which helps maintainability.</span></span>
* <span data-ttu-id="23763-3716">URL の生成は、任意の拡張機能をサポートする、ルートに基づきます。</span><span class="sxs-lookup"><span data-stu-id="23763-3716">URL generation is based on routes, which support arbitrary extensibility.</span></span> <span data-ttu-id="23763-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> では、URL を構築するためのメソッドが提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3717"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offers methods to build URLs.</span></span>
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
<span data-ttu-id="23763-3718">ルーティングは <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> クラスにより[ミドルウェア](xref:fundamentals/middleware/index) パイプラインに接続されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3718">Routing is connected to the [middleware](xref:fundamentals/middleware/index) pipeline by the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> class.</span></span> <span data-ttu-id="23763-3719">[ASP.NET Core MVC](xref:mvc/overview) では、ミドルウェア パイプラインにその構成の一部としてルーティングが追加され、MVC および Razor Pages アプリでのルーティングが処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3719">[ASP.NET Core MVC](xref:mvc/overview) adds routing to the middleware pipeline as part of its configuration and handles routing in MVC and Razor Pages apps.</span></span> <span data-ttu-id="23763-3720">スタンドアロン コンポーネントとしてルーティングを利用する方法については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3720">To learn how to use routing as a standalone component, see the [Use Routing Middleware](#use-routing-middleware) section.</span></span>

### <a name="url-matching"></a><span data-ttu-id="23763-3721">URL 一致</span><span class="sxs-lookup"><span data-stu-id="23763-3721">URL matching</span></span>

<span data-ttu-id="23763-3722">URL 一致というプロセスでは、ルーティングによって、受信要求が*ハンドラー*に配布されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3722">URL matching is the process by which routing dispatches an incoming request to a *handler*.</span></span> <span data-ttu-id="23763-3723">このプロセスは URL パスのデータに基づきますが、要求内のあらゆるデータを考慮することもできます。</span><span class="sxs-lookup"><span data-stu-id="23763-3723">This process is based on data in the URL path but can be extended to consider any data in the request.</span></span> <span data-ttu-id="23763-3724">アプリの規模を大きくしたり、より複雑にしたりするとき、要求を個別のハンドラーに配布する機能が鍵となります。</span><span class="sxs-lookup"><span data-stu-id="23763-3724">The ability to dispatch requests to separate handlers is key to scaling the size and complexity of an app.</span></span>

<span data-ttu-id="23763-3725">受信要求は <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> に入ります。これが各ルートで順に <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="23763-3725">Incoming requests enter the <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, which calls the <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> method on each route in sequence.</span></span> <span data-ttu-id="23763-3726"><xref:Microsoft.AspNetCore.Routing.IRouter> インスタンスでは、[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) を非 null の <xref:Microsoft.AspNetCore.Http.RequestDelegate> に設定することで、要求を*処理する*かどうかを選択します。</span><span class="sxs-lookup"><span data-stu-id="23763-3726">The <xref:Microsoft.AspNetCore.Routing.IRouter> instance chooses whether to *handle* the request by setting the [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) to a non-null <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="23763-3727">要求に適したハンドラーがルートに見つかると、ルート処理が停止します。ハンドラーが呼び出され、要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="23763-3727">If a route sets a handler for the request, route processing stops, and the handler is invoked to process the request.</span></span> <span data-ttu-id="23763-3728">要求を処理するためのルート ハンドラーが見つからない場合、ミドルウェアによって、要求が要求パイプラインの次のミドルウェアに渡されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3728">If no route handler is found to process the request, the middleware hands the request off to the next middleware in the request pipeline.</span></span>

<span data-ttu-id="23763-3729"><xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> に最初に入力されるのが、現在の要求に関連付けられている [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) です。</span><span class="sxs-lookup"><span data-stu-id="23763-3729">The primary input to <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is the [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associated with the current request.</span></span> <span data-ttu-id="23763-3730">[RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) と [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) は、ルートが一致した後に設定される出力です。</span><span class="sxs-lookup"><span data-stu-id="23763-3730">The [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) and [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) are outputs set after a route is matched.</span></span>

<span data-ttu-id="23763-3731">また、<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> を呼び出す一致が見つかると、それまでに実行された要求処理に基づいて、[RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) のプロパティが適切な値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3731">A match that calls <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> also sets the properties of the [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) to appropriate values based on the request processing performed thus far.</span></span>

<span data-ttu-id="23763-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) は、ルートから生成された*ルート値*のディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-3732">[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) is a dictionary of *route values* produced from the route.</span></span> <span data-ttu-id="23763-3733">この値は通常、URL のトークン化で決定されます。この値を利用してユーザー入力を受け取ったり、アプリ内で決定をさらに配布したりできます。</span><span class="sxs-lookup"><span data-stu-id="23763-3733">These values are usually determined by tokenizing the URL and can be used to accept user input or to make further dispatching decisions inside the app.</span></span>

<span data-ttu-id="23763-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) は、一致したルートに関連する追加データのプロパティ バッグです。</span><span class="sxs-lookup"><span data-stu-id="23763-3734">[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) is a property bag of additional data related to the matched route.</span></span> <span data-ttu-id="23763-3735">一致したルートに基づいてアプリで決定を行えるように、<xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> が提供されます。これは状態データと各ルートの関連付けをサポートするためのものです。</span><span class="sxs-lookup"><span data-stu-id="23763-3735"><xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> are provided to support associating state data with each route so that the app can make decisions based on which route matched.</span></span> <span data-ttu-id="23763-3736">この値は開発者が定義するものです。ルーティングの動作に影響を与えることは**ありません**。</span><span class="sxs-lookup"><span data-stu-id="23763-3736">These values are developer-defined and do **not** affect the behavior of routing in any way.</span></span> <span data-ttu-id="23763-3737">また、[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) に一時退避される値はどのような型でも構いません。一方、[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) の場合は、文字列間で変換できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3737">Additionally, values stashed in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) can be of any type, in contrast to [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), which must be convertible to and from strings.</span></span>

<span data-ttu-id="23763-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) は、過去に要求に一致したルートの一覧です。</span><span class="sxs-lookup"><span data-stu-id="23763-3738">[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) is a list of the routes that took part in successfully matching the request.</span></span> <span data-ttu-id="23763-3739">ルートはルートの中に入れ子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23763-3739">Routes can be nested inside of one another.</span></span> <span data-ttu-id="23763-3740"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> プロパティは、結果的に一致をもたらしたルートの論理ツリーを通るパスを表します。</span><span class="sxs-lookup"><span data-stu-id="23763-3740">The <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> property reflects the path through the logical tree of routes that resulted in a match.</span></span> <span data-ttu-id="23763-3741">一般的に、<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最初の項目はルート コレクションであり、これは URL 生成に使用するものです。</span><span class="sxs-lookup"><span data-stu-id="23763-3741">Generally, the first item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route collection and should be used for URL generation.</span></span> <span data-ttu-id="23763-3742"><xref:Microsoft.AspNetCore.Routing.RouteData.Routers> の最後の項目は、一致したルート ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="23763-3742">The last item in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> is the route handler that matched.</span></span>

<a name="lg"></a>

### <a name="url-generation"></a><span data-ttu-id="23763-3743">URL 生成</span><span class="sxs-lookup"><span data-stu-id="23763-3743">URL generation</span></span>

<span data-ttu-id="23763-3744">URL 生成は、ルーティングにおいて、一連のルート値に基づいて URL パスを作成するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="23763-3744">URL generation is the process by which routing can create a URL path based on a set of route values.</span></span> <span data-ttu-id="23763-3745">これにより、ルート ハンドラーとそれにアクセスする URL を論理的に分離できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3745">This allows for a logical separation between route handlers and the URLs that access them.</span></span>

<span data-ttu-id="23763-3746">URL 生成は同様の繰り返しプロセスに従いますが、最初にユーザーまたはフレームワーク コードでルート コレクションの <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="23763-3746">URL generation follows a similar iterative process, but it starts with user or framework code calling into the <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method of the route collection.</span></span> <span data-ttu-id="23763-3747">非 null の <xref:Microsoft.AspNetCore.Routing.VirtualPathData> が返されるまで、各*ルート* で順番に <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3747">Each *route* has its <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> method called in sequence until a non-null <xref:Microsoft.AspNetCore.Routing.VirtualPathData> is returned.</span></span>

<span data-ttu-id="23763-3748"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の第一入力:</span><span class="sxs-lookup"><span data-stu-id="23763-3748">The primary inputs to <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> are:</span></span>

* [<span data-ttu-id="23763-3749">VirtualPathContext.HttpContext</span><span class="sxs-lookup"><span data-stu-id="23763-3749">VirtualPathContext.HttpContext</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [<span data-ttu-id="23763-3750">VirtualPathContext.Values</span><span class="sxs-lookup"><span data-stu-id="23763-3750">VirtualPathContext.Values</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [<span data-ttu-id="23763-3751">VirtualPathContext.AmbientValues</span><span class="sxs-lookup"><span data-stu-id="23763-3751">VirtualPathContext.AmbientValues</span></span>](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

<span data-ttu-id="23763-3752">ルートでは <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> と <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> によって指定されるルート値を主に使用し、URL を生成できるかどうかと、どの値を含めるかを判断します。</span><span class="sxs-lookup"><span data-stu-id="23763-3752">Routes primarily use the route values provided by <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> and <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> to decide whether it's possible to generate a URL and what values to include.</span></span> <span data-ttu-id="23763-3753"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> は、現在の要求を照合することで生成された一連のルート値です。</span><span class="sxs-lookup"><span data-stu-id="23763-3753">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> are the set of route values that were produced from matching the current request.</span></span> <span data-ttu-id="23763-3754">対照的に、<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> は、現在の操作に適した URL の生成方法を指定するルート値です。</span><span class="sxs-lookup"><span data-stu-id="23763-3754">In contrast, <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> are the route values that specify how to generate the desired URL for the current operation.</span></span> <span data-ttu-id="23763-3755">ルートで現在のコンテキストに関連付けられているサービスまたは追加データを取得する必要がある場合、<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3755">The <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> is provided in case a route should obtain services or additional data associated with the current context.</span></span>

> [!TIP]
> <span data-ttu-id="23763-3756">[VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) は [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) のオーバーライド セットであると考えてください。</span><span class="sxs-lookup"><span data-stu-id="23763-3756">Think of [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) as a set of overrides for the [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*).</span></span> <span data-ttu-id="23763-3757">URL 生成では、同じルートまたはルート値を利用することでリンクの URL 生成を生成するために、現在の要求からのルート値の再利用が試行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3757">URL generation attempts to reuse route values from the current request to generate URLs for links using the same route or route values.</span></span>

<span data-ttu-id="23763-3758"><xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の出力は <xref:Microsoft.AspNetCore.Routing.VirtualPathData> です。</span><span class="sxs-lookup"><span data-stu-id="23763-3758">The output of <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is a <xref:Microsoft.AspNetCore.Routing.VirtualPathData>.</span></span> <span data-ttu-id="23763-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> は <xref:Microsoft.AspNetCore.Routing.RouteData> の並列です。</span><span class="sxs-lookup"><span data-stu-id="23763-3759"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> is a parallel of <xref:Microsoft.AspNetCore.Routing.RouteData>.</span></span> <span data-ttu-id="23763-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> には出力 URL として <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> が含まれ、ルートで設定する追加プロパティがいくつか含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-3760"><xref:Microsoft.AspNetCore.Routing.VirtualPathData> contains the <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> for the output URL and some additional properties that should be set by the route.</span></span>

<span data-ttu-id="23763-3761">[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) プロパティには、ルートによって生成された*仮想パス*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-3761">The [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) property contains the *virtual path* produced by the route.</span></span> <span data-ttu-id="23763-3762">必要に応じて、パスをさらに処理する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3762">Depending on your needs, you may need to process the path further.</span></span> <span data-ttu-id="23763-3763">生成された URL を HTML で表示するには、アプリの基礎パスを先頭に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3763">If you want to render the generated URL in HTML, prepend the base path of the app.</span></span>

<span data-ttu-id="23763-3764">[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) は URL を正常に生成したルートの参照です。</span><span class="sxs-lookup"><span data-stu-id="23763-3764">The [VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) is a reference to the route that successfully generated the URL.</span></span>

<span data-ttu-id="23763-3765">[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) プロパティは、URL を生成したルートに関連する追加データのディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-3765">The [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) properties is a dictionary of additional data related to the route that generated the URL.</span></span> <span data-ttu-id="23763-3766">これは [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) の並列です。</span><span class="sxs-lookup"><span data-stu-id="23763-3766">This is the parallel of [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).</span></span>

### <a name="create-routes"></a><span data-ttu-id="23763-3767">ルートを作成する</span><span class="sxs-lookup"><span data-stu-id="23763-3767">Create routes</span></span>

<span data-ttu-id="23763-3768">ルーティングにより、<xref:Microsoft.AspNetCore.Routing.IRouter> の標準実装として <xref:Microsoft.AspNetCore.Routing.Route> クラスが与えられます。</span><span class="sxs-lookup"><span data-stu-id="23763-3768">Routing provides the <xref:Microsoft.AspNetCore.Routing.Route> class as the standard implementation of <xref:Microsoft.AspNetCore.Routing.IRouter>.</span></span> <span data-ttu-id="23763-3769"><xref:Microsoft.AspNetCore.Routing.Route> では*ルート テンプレート* 構文を利用して、<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> の呼び出し時に URL パスに対して照合するパターンを定義します。</span><span class="sxs-lookup"><span data-stu-id="23763-3769"><xref:Microsoft.AspNetCore.Routing.Route> uses the *route template* syntax to define patterns to match against the URL path when <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> is called.</span></span> <span data-ttu-id="23763-3770"><xref:Microsoft.AspNetCore.Routing.Route> は同じルート テンプレートを利用し、<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> の呼び出し時に URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3770"><xref:Microsoft.AspNetCore.Routing.Route> uses the same route template to generate a URL when <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> is called.</span></span>

<span data-ttu-id="23763-3771">ほとんどのアプリは、<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> を呼び出すか、<xref:Microsoft.AspNetCore.Routing.IRouteBuilder> で定義されている同様の拡張メソッドの 1 つを呼び出してルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3771">Most apps create routes by calling <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> or one of the similar extension methods defined on <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>.</span></span> <span data-ttu-id="23763-3772">いずれの <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 拡張メソッドでも、<xref:Microsoft.AspNetCore.Routing.Route> のインスタンスが作成され、それがルート コレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3772">Any of the <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> extension methods create an instance of <xref:Microsoft.AspNetCore.Routing.Route> and add it to the route collection.</span></span>

<span data-ttu-id="23763-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> では、ルート ハンドラー パラメーターは受け入れられません。</span><span class="sxs-lookup"><span data-stu-id="23763-3773"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> doesn't accept a route handler parameter.</span></span> <span data-ttu-id="23763-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> は、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> によって処理されるルートを追加するだけです。</span><span class="sxs-lookup"><span data-stu-id="23763-3774"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> only adds routes that are handled by the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span> <span data-ttu-id="23763-3775">既定のハンドラーは `IRouter` であり、そのハンドラーで要求が処理されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3775">The default handler is an `IRouter`, and the handler might not handle the request.</span></span> <span data-ttu-id="23763-3776">たとえば、ASP.NET Core MVC は通常、利用できるコントローラーやアクションに一致する要求のみを処理する既定のハンドラーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3776">For example, ASP.NET Core MVC is typically configured as a default handler that only handles requests that match an available controller and action.</span></span> <span data-ttu-id="23763-3777">MVC でのルーティングの詳細については、「<xref:mvc/controllers/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3777">To learn more about routing in MVC, see <xref:mvc/controllers/routing>.</span></span>

<span data-ttu-id="23763-3778">次のコード例は、一般的な ASP.NET Core MVC ルート定義によって使用される <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 呼び出しの例です。</span><span class="sxs-lookup"><span data-stu-id="23763-3778">The following code example is an example of a <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> call used by a typical ASP.NET Core MVC route definition:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="23763-3779">このテンプレートでは URL パスが照合され、ルート値が抽出されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3779">This template matches a URL path and extracts the route values.</span></span> <span data-ttu-id="23763-3780">たとえば、`/Products/Details/17` というパスでは、`{ controller = Products, action = Details, id = 17 }` というルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3780">For example, the path `/Products/Details/17` generates the following route values: `{ controller = Products, action = Details, id = 17 }`.</span></span>

<span data-ttu-id="23763-3781">ルート値は、URL パスをセグメントに分割し、各セグメントと、ルート テンプレートの*ルート パラメーター* 名を照合することで決定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3781">Route values are determined by splitting the URL path into segments and matching each segment with the *route parameter* name in the route template.</span></span> <span data-ttu-id="23763-3782">ルート パラメーターが指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3782">Route parameters are named.</span></span> <span data-ttu-id="23763-3783">パラメーターは、中かっこ `{ ... }` でパラメーター名を囲むことで定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3783">The parameters defined by enclosing the parameter name in braces `{ ... }`.</span></span>

<span data-ttu-id="23763-3784">上のテンプレートでは URL パス `/` の照合も行い、`{ controller = Home, action = Index }` という値を生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3784">The preceding template could also match the URL path `/` and produce the values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="23763-3785">これは、ルート パラメーターの `{controller}` と `{action}` に既定値が与えられ、`id` ルート パラメーターが任意であるためです。</span><span class="sxs-lookup"><span data-stu-id="23763-3785">This occurs because the `{controller}` and `{action}` route parameters have default values and the `id` route parameter is optional.</span></span> <span data-ttu-id="23763-3786">ルート パラメーター名の後の等号 (`=`) とそれに続く値により、パラメーターの既定値が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3786">An equals sign (`=`) followed by a value after the route parameter name defines a default value for the parameter.</span></span> <span data-ttu-id="23763-3787">ルート パラメーター名の後の疑問符 (`?`) により、オプションのパラメーターが定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3787">A question mark (`?`) after the route parameter name defines an optional parameter.</span></span>

<span data-ttu-id="23763-3788">既定値のルート パラメーターはルートが一致するとルート値を*常に*生成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3788">Route parameters with a default value *always* produce a route value when the route matches.</span></span> <span data-ttu-id="23763-3789">省略可能なパラメーターでは、対応する URL パス セグメントがない場合、ルート値は生成されません。</span><span class="sxs-lookup"><span data-stu-id="23763-3789">Optional parameters don't produce a route value if there is no corresponding URL path segment.</span></span> <span data-ttu-id="23763-3790">ルート テンプレートのシナリオと構文の詳しい説明については、「[ルート テンプレート参照](#route-template-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3790">See the [Route template reference](#route-template-reference) section for a thorough description of route template scenarios and syntax.</span></span>

<span data-ttu-id="23763-3791">次の例では、ルート パラメーター定義 `{id:int}` により、`id` ルート パラメーターの[ルート制約](#route-constraint-reference)が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3791">In the following example, the route parameter definition `{id:int}` defines a [route constraint](#route-constraint-reference) for the `id` route parameter:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

<span data-ttu-id="23763-3792">このテンプレートは `/Products/Details/Apples` ではなく、`/Products/Details/17` のような URL パスを照合します。</span><span class="sxs-lookup"><span data-stu-id="23763-3792">This template matches a URL path like `/Products/Details/17` but not `/Products/Details/Apples`.</span></span> <span data-ttu-id="23763-3793">ルート制約は <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を実装し、ルート値を調べて正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="23763-3793">Route constraints implement <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> and inspect route values to verify them.</span></span> <span data-ttu-id="23763-3794">この例では、ルート値 `id` は整数に変換できなければなりません。</span><span class="sxs-lookup"><span data-stu-id="23763-3794">In this example, the route value `id` must be convertible to an integer.</span></span> <span data-ttu-id="23763-3795">フレームワークによって提供されるルート制約については、「[ルート制約参照](#route-constraint-reference)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3795">See [route-constraint-reference](#route-constraint-reference) for an explanation of route constraints provided by the framework.</span></span>

<span data-ttu-id="23763-3796"><xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> の追加オーバーロードは、`constraints`、`dataTokens`、`defaults` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="23763-3796">Additional overloads of <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> accept values for `constraints`, `dataTokens`, and `defaults`.</span></span> <span data-ttu-id="23763-3797">このようなパラメーターは一般的に、匿名型のオブジェクトを渡し、匿名型のプロパティ名がルート パラメーター名と一致するというような使われ方をします。</span><span class="sxs-lookup"><span data-stu-id="23763-3797">The typical usage of these parameters is to pass an anonymously typed object, where the property names of the anonymous type match route parameter names.</span></span>

<span data-ttu-id="23763-3798">次の <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 例では、同等のルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="23763-3798">The following <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> examples create equivalent routes:</span></span>

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> <span data-ttu-id="23763-3799">制約と既定値を定義するインライン構文は単純なルートの場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="23763-3799">The inline syntax for defining constraints and defaults can be convenient for simple routes.</span></span> <span data-ttu-id="23763-3800">しかし、インライン構文でサポートされていない、データ トークンなどのシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-3800">However, there are scenarios, such as data tokens, that aren't supported by inline syntax.</span></span>

<span data-ttu-id="23763-3801">次の例では、その他のいくつかのシナリオを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-3801">The following example demonstrates a few additional scenarios:</span></span>

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

<span data-ttu-id="23763-3802">上記のテンプレートでは `/Blog/All-About-Routing/Introduction` のような URL パスを照合し、値 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="23763-3802">The preceding template matches a URL path like `/Blog/All-About-Routing/Introduction` and extracts the values `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`.</span></span> <span data-ttu-id="23763-3803">`controller` と `action` の既定のルート値は、テンプレートに対応するルート パラメーターがなくても、ルートにより生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3803">The default route values for `controller` and `action` are produced by the route even though there are no corresponding route parameters in the template.</span></span> <span data-ttu-id="23763-3804">既定値はルート テンプレートで指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3804">Default values can be specified in the route template.</span></span> <span data-ttu-id="23763-3805">`article` ルート パラメーターは、ルート パラメーター名の前にアスタリスク (`*`) があるときに、*キャッチオール* として定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3805">The `article` route parameter is defined as a *catch-all* by the appearance of an asterisk (`*`) before the route parameter name.</span></span> <span data-ttu-id="23763-3806">キャッチオール ルート パラメーターは、URL パスの残りの部分をキャプチャします。空の文字列も照合できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3806">Catch-all route parameters capture the remainder of the URL path and can also match the empty string.</span></span>

<span data-ttu-id="23763-3807">次の例では、ルート制約とデータ トークンを追加します。</span><span class="sxs-lookup"><span data-stu-id="23763-3807">The following example adds route constraints and data tokens:</span></span>

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

<span data-ttu-id="23763-3808">上記のテンプレートでは `/en-US/Products/5` のような URL パスを照合し、値 `{ controller = Products, action = Details, id = 5 }` とデータ トークン `{ locale = en-US }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="23763-3808">The preceding template matches a URL path like `/en-US/Products/5` and extracts the values `{ controller = Products, action = Details, id = 5 }` and the data tokens `{ locale = en-US }`.</span></span>

![ローカル変数ウィンドウ トークン](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a><span data-ttu-id="23763-3810">ルート クラス URL の生成</span><span class="sxs-lookup"><span data-stu-id="23763-3810">Route class URL generation</span></span>

<span data-ttu-id="23763-3811"><xref:Microsoft.AspNetCore.Routing.Route> クラスは、一連のルート値をそのルート テンプレートと組み合わせる方法でも URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-3811">The <xref:Microsoft.AspNetCore.Routing.Route> class can also perform URL generation by combining a set of route values with its route template.</span></span> <span data-ttu-id="23763-3812">論理的には、URL パスの照合とは逆のプロセスになります。</span><span class="sxs-lookup"><span data-stu-id="23763-3812">This is logically the reverse process of matching the URL path.</span></span>

> [!TIP]
> <span data-ttu-id="23763-3813">URL 生成を理解する方法として、どのような URL を生成したいのかを考えてください。そして、ルート テンプレートがその URL にどのように照合されるのかを考えてください。</span><span class="sxs-lookup"><span data-stu-id="23763-3813">To better understand URL generation, imagine what URL you want to generate and then think about how a route template would match that URL.</span></span> <span data-ttu-id="23763-3814">どのような値が生成されるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="23763-3814">What values would be produced?</span></span> <span data-ttu-id="23763-3815"><xref:Microsoft.AspNetCore.Routing.Route> クラスにおける URL 生成のしくみと大体同じになります。</span><span class="sxs-lookup"><span data-stu-id="23763-3815">This is the rough equivalent of how URL generation works in the <xref:Microsoft.AspNetCore.Routing.Route> class.</span></span>

<span data-ttu-id="23763-3816">次の例では、一般的な ASP.NET Core MVC の既定のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-3816">The following example uses a general ASP.NET Core MVC default route:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="23763-3817">ルート値が `{ controller = Products, action = List }` の場合、URL `/Products/List` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3817">With the route values `{ controller = Products, action = List }`, the URL `/Products/List` is generated.</span></span> <span data-ttu-id="23763-3818">ルート値が対応ルート パラメーターの代替となり、URL パスが作られます。</span><span class="sxs-lookup"><span data-stu-id="23763-3818">The route values are substituted for the corresponding route parameters to form the URL path.</span></span> <span data-ttu-id="23763-3819">`id` は任意のルート パラメーターであるため、URL は `id` の値なしで正常に生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3819">Since `id` is an optional route parameter, the URL is successfully generated without a value for `id`.</span></span>

<span data-ttu-id="23763-3820">ルート値が `{ controller = Home, action = Index }` の場合、URL `/` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3820">With the route values `{ controller = Home, action = Index }`, the URL `/` is generated.</span></span> <span data-ttu-id="23763-3821">指定されたルート値は既定値と一致し、その既定値に対応するセグメントを省略しても問題は発生しません。</span><span class="sxs-lookup"><span data-stu-id="23763-3821">The provided route values match the default values, and the segments corresponding to the default values are safely omitted.</span></span>

<span data-ttu-id="23763-3822">生成された両方の URL では、次のルート定義 (`/Home/Index` と `/`) でラウンドトリップされ、URL の生成に使用された同じルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-3822">Both URLs generated round-trip with the following route definition (`/Home/Index` and `/`) produce the same route values that were used to generate the URL.</span></span>

> [!NOTE]
> <span data-ttu-id="23763-3823">アプリで ASP.NET Core MVC が使用される場合、ルーティングを直接呼び出す代わりに、<xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> を使用して URL を生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3823">An app using ASP.NET Core MVC should use <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> to generate URLs instead of calling into routing directly.</span></span>

<span data-ttu-id="23763-3824">URL 生成の詳細については、「[URL 生成参照](#url-generation-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-3824">For more information on URL generation, see the [Url generation reference](#url-generation-reference) section.</span></span>

## <a name="use-routing-middleware"></a><span data-ttu-id="23763-3825">ルーティング ミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="23763-3825">Use routing middleware</span></span>

<span data-ttu-id="23763-3826">アプリのプロジェクト ファイルの [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を参照します。</span><span class="sxs-lookup"><span data-stu-id="23763-3826">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the app's project file.</span></span>

<span data-ttu-id="23763-3827">`Startup.ConfigureServices` でサービス コンテナーにルーティングを追加した例:</span><span class="sxs-lookup"><span data-stu-id="23763-3827">Add routing to the service container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

<span data-ttu-id="23763-3828">ルートは `Startup.Configure` メソッドで設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-3828">Routes must be configured in the `Startup.Configure` method.</span></span> <span data-ttu-id="23763-3829">サンプル アプリでは次の API を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-3829">The sample app uses the following APIs:</span></span>

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <span data-ttu-id="23763-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>:HTTP GET 要求のみを照合します。</span><span class="sxs-lookup"><span data-stu-id="23763-3830"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Matches only HTTP GET requests.</span></span>
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

<span data-ttu-id="23763-3831">下の表は、応答と所与の URI をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-3831">The following table shows the responses with the given URIs.</span></span>

| <span data-ttu-id="23763-3832">URI</span><span class="sxs-lookup"><span data-stu-id="23763-3832">URI</span></span>                    | <span data-ttu-id="23763-3833">応答</span><span class="sxs-lookup"><span data-stu-id="23763-3833">Response</span></span>                                          |
| ---
<span data-ttu-id="23763-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3835">'Blazor'</span></span>
- <span data-ttu-id="23763-3836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3836">'Identity'</span></span>
- <span data-ttu-id="23763-3837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3837">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3838">'Razor'</span></span>
- <span data-ttu-id="23763-3839">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3841">'Blazor'</span></span>
- <span data-ttu-id="23763-3842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3842">'Identity'</span></span>
- <span data-ttu-id="23763-3843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3843">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3844">'Razor'</span></span>
- <span data-ttu-id="23763-3845">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3847">'Blazor'</span></span>
- <span data-ttu-id="23763-3848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3848">'Identity'</span></span>
- <span data-ttu-id="23763-3849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3849">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3850">'Razor'</span></span>
- <span data-ttu-id="23763-3851">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3853">'Blazor'</span></span>
- <span data-ttu-id="23763-3854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3854">'Identity'</span></span>
- <span data-ttu-id="23763-3855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3855">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3856">'Razor'</span></span>
- <span data-ttu-id="23763-3857">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3859">'Blazor'</span></span>
- <span data-ttu-id="23763-3860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3860">'Identity'</span></span>
- <span data-ttu-id="23763-3861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3861">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3862">'Razor'</span></span>
- <span data-ttu-id="23763-3863">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3865">'Blazor'</span></span>
- <span data-ttu-id="23763-3866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3866">'Identity'</span></span>
- <span data-ttu-id="23763-3867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3867">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3868">'Razor'</span></span>
- <span data-ttu-id="23763-3869">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3871">'Blazor'</span></span>
- <span data-ttu-id="23763-3872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3872">'Identity'</span></span>
- <span data-ttu-id="23763-3873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3873">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3874">'Razor'</span></span>
- <span data-ttu-id="23763-3875">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3877">'Blazor'</span></span>
- <span data-ttu-id="23763-3878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3878">'Identity'</span></span>
- <span data-ttu-id="23763-3879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3879">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3880">'Razor'</span></span>
- <span data-ttu-id="23763-3881">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3883">'Blazor'</span></span>
- <span data-ttu-id="23763-3884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3884">'Identity'</span></span>
- <span data-ttu-id="23763-3885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3885">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3886">'Razor'</span></span>
- <span data-ttu-id="23763-3887">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3887">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3888">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3889">'Blazor'</span></span>
- <span data-ttu-id="23763-3890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3890">'Identity'</span></span>
- <span data-ttu-id="23763-3891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3891">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3892">'Razor'</span></span>
- <span data-ttu-id="23763-3893">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3895">'Blazor'</span></span>
- <span data-ttu-id="23763-3896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3896">'Identity'</span></span>
- <span data-ttu-id="23763-3897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3897">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3898">'Razor'</span></span>
- <span data-ttu-id="23763-3899">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3901">'Blazor'</span></span>
- <span data-ttu-id="23763-3902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3902">'Identity'</span></span>
- <span data-ttu-id="23763-3903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3903">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3904">'Razor'</span></span>
- <span data-ttu-id="23763-3905">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3907">'Blazor'</span></span>
- <span data-ttu-id="23763-3908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3908">'Identity'</span></span>
- <span data-ttu-id="23763-3909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3909">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3910">'Razor'</span></span>
- <span data-ttu-id="23763-3911">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3913">'Blazor'</span></span>
- <span data-ttu-id="23763-3914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3914">'Identity'</span></span>
- <span data-ttu-id="23763-3915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3915">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3916">'Razor'</span></span>
- <span data-ttu-id="23763-3917">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3917">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3918">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3919">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3919">'Blazor'</span></span>
- <span data-ttu-id="23763-3920">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3920">'Identity'</span></span>
- <span data-ttu-id="23763-3921">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3921">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3922">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3922">'Razor'</span></span>
- <span data-ttu-id="23763-3923">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3923">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3924">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3925">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3925">'Blazor'</span></span>
- <span data-ttu-id="23763-3926">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3926">'Identity'</span></span>
- <span data-ttu-id="23763-3927">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3927">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3928">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3928">'Razor'</span></span>
- <span data-ttu-id="23763-3929">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3929">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3930">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3931">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3931">'Blazor'</span></span>
- <span data-ttu-id="23763-3932">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3932">'Identity'</span></span>
- <span data-ttu-id="23763-3933">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3933">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3934">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3934">'Razor'</span></span>
- <span data-ttu-id="23763-3935">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3935">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3936">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3937">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3937">'Blazor'</span></span>
- <span data-ttu-id="23763-3938">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3938">'Identity'</span></span>
- <span data-ttu-id="23763-3939">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3939">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3940">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3940">'Razor'</span></span>
- <span data-ttu-id="23763-3941">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3941">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3942">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3943">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3943">'Blazor'</span></span>
- <span data-ttu-id="23763-3944">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3944">'Identity'</span></span>
- <span data-ttu-id="23763-3945">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3945">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3946">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3946">'Razor'</span></span>
- <span data-ttu-id="23763-3947">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3947">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3948">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3949">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3949">'Blazor'</span></span>
- <span data-ttu-id="23763-3950">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3950">'Identity'</span></span>
- <span data-ttu-id="23763-3951">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3951">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3952">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3952">'Razor'</span></span>
- <span data-ttu-id="23763-3953">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3953">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3954">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3955">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3955">'Blazor'</span></span>
- <span data-ttu-id="23763-3956">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3956">'Identity'</span></span>
- <span data-ttu-id="23763-3957">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3957">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3958">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3958">'Razor'</span></span>
- <span data-ttu-id="23763-3959">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3959">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3960">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3961">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3961">'Blazor'</span></span>
- <span data-ttu-id="23763-3962">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3962">'Identity'</span></span>
- <span data-ttu-id="23763-3963">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3963">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3964">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3964">'Razor'</span></span>
- <span data-ttu-id="23763-3965">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3965">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3966">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3967">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3967">'Blazor'</span></span>
- <span data-ttu-id="23763-3968">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3968">'Identity'</span></span>
- <span data-ttu-id="23763-3969">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3969">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3970">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3970">'Razor'</span></span>
- <span data-ttu-id="23763-3971">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3971">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3972">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3973">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3973">'Blazor'</span></span>
- <span data-ttu-id="23763-3974">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3974">'Identity'</span></span>
- <span data-ttu-id="23763-3975">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3975">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3976">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3976">'Razor'</span></span>
- <span data-ttu-id="23763-3977">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3977">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3978">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3979">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3979">'Blazor'</span></span>
- <span data-ttu-id="23763-3980">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3980">'Identity'</span></span>
- <span data-ttu-id="23763-3981">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3981">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3982">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3982">'Razor'</span></span>
- <span data-ttu-id="23763-3983">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3983">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3984">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3985">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3985">'Blazor'</span></span>
- <span data-ttu-id="23763-3986">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3986">'Identity'</span></span>
- <span data-ttu-id="23763-3987">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3987">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3988">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3988">'Razor'</span></span>
- <span data-ttu-id="23763-3989">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3989">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3990">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3991">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3991">'Blazor'</span></span>
- <span data-ttu-id="23763-3992">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3992">'Identity'</span></span>
- <span data-ttu-id="23763-3993">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3993">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-3994">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-3994">'Razor'</span></span>
- <span data-ttu-id="23763-3995">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-3995">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-3996">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-3997">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-3997">'Blazor'</span></span>
- <span data-ttu-id="23763-3998">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-3998">'Identity'</span></span>
- <span data-ttu-id="23763-3999">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-3999">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4000">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4000">'Razor'</span></span>
- <span data-ttu-id="23763-4001">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4001">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4002">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4003">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4003">'Blazor'</span></span>
- <span data-ttu-id="23763-4004">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4004">'Identity'</span></span>
- <span data-ttu-id="23763-4005">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4005">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4006">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4006">'Razor'</span></span>
- <span data-ttu-id="23763-4007">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4007">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4008">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4009">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4009">'Blazor'</span></span>
- <span data-ttu-id="23763-4010">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4010">'Identity'</span></span>
- <span data-ttu-id="23763-4011">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4011">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4012">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4012">'Razor'</span></span>
- <span data-ttu-id="23763-4013">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4013">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4014">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4015">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4015">'Blazor'</span></span>
- <span data-ttu-id="23763-4016">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4016">'Identity'</span></span>
- <span data-ttu-id="23763-4017">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4017">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4018">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4018">'Razor'</span></span>
- <span data-ttu-id="23763-4019">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4019">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4020">------------------------- | | `/package/create/3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-4020">------------------------- | | `/package/create/3`    | Hello!</span></span> <span data-ttu-id="23763-4021">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-4021">Route values: [operation, create], [id, 3] | | `/package/track/-3`    | Hello!</span></span> <span data-ttu-id="23763-4022">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span><span class="sxs-lookup"><span data-stu-id="23763-4022">Route values: [operation, track], [id, -3] | | `/package/track/-3/`   | Hello!</span></span> <span data-ttu-id="23763-4023">Route values: [operation, track], [id, -3] | | `/package/track/`      | 要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-4023">Route values: [operation, track], [id, -3] | | `/package/track/`      | The request falls through, no match.</span></span>              <span data-ttu-id="23763-4024">| | `GET /hello/Joe`       | Hi, Joe!</span><span class="sxs-lookup"><span data-stu-id="23763-4024">| | `GET /hello/Joe`       | Hi, Joe!</span></span>                                          <span data-ttu-id="23763-4025">| | `POST /hello/Joe`      | 要求はフォール スルーし、HTTP GET のみに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4025">| | `POST /hello/Joe`      | The request falls through, matches HTTP GET only.</span></span> <span data-ttu-id="23763-4026">| | `GET /hello/Joe/Smith` | 要求はフォール スルーし、一致するものはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-4026">| | `GET /hello/Joe/Smith` | The request falls through, no match.</span></span>              |

<span data-ttu-id="23763-4027">ルートを 1 つ構成する場合、<xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> を呼び出し、`IRouter` インスタンスを渡します。</span><span class="sxs-lookup"><span data-stu-id="23763-4027">If you're configuring a single route, call <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> passing in an `IRouter` instance.</span></span> <span data-ttu-id="23763-4028"><xref:Microsoft.AspNetCore.Routing.RouteBuilder> を使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23763-4028">You won't need to use <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.</span></span>

<span data-ttu-id="23763-4029">このフレームワークでは、ルートを作成するために拡張メソッド セット (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4029">The framework provides a set of extension methods for creating routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):</span></span>

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

<span data-ttu-id="23763-4030"><xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> など、リストされているメソッドの一部では、<xref:Microsoft.AspNetCore.Http.RequestDelegate> が必要です。</span><span class="sxs-lookup"><span data-stu-id="23763-4030">Some of listed methods, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, require a <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span> <span data-ttu-id="23763-4031"><xref:Microsoft.AspNetCore.Http.RequestDelegate> は、ルートが一致したとき、*ルート ハンドラー*として使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4031">The <xref:Microsoft.AspNetCore.Http.RequestDelegate> is used as the *route handler* when the route matches.</span></span> <span data-ttu-id="23763-4032">この仲間の他のメソッドでは、ルート ハンドラーとして使用されるミドルウェア パイプラインを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4032">Other methods in this family allow configuring a middleware pipeline for use as the route handler.</span></span> <span data-ttu-id="23763-4033">`Map*` メソッドで、<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> などのハンドラーを受け入れない場合、<xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4033">If the `Map*` method doesn't accept a handler, such as <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, it uses the <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.</span></span>

<span data-ttu-id="23763-4034">`Map[Verb]` メソッドは制約を利用し、メソッド名の HTTP Verb にルートを制限します。</span><span class="sxs-lookup"><span data-stu-id="23763-4034">The `Map[Verb]` methods use constraints to limit the route to the HTTP Verb in the method name.</span></span> <span data-ttu-id="23763-4035">たとえば、 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> や <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4035">For example, see <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> and <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.</span></span>

## <a name="route-template-reference"></a><span data-ttu-id="23763-4036">ルート テンプレート参照</span><span class="sxs-lookup"><span data-stu-id="23763-4036">Route template reference</span></span>

<span data-ttu-id="23763-4037">中括弧 (`{ ... }`) 内のトークンは*ルート パラメーター*を定義します。ルートが一致した場合、これがバインドされます。</span><span class="sxs-lookup"><span data-stu-id="23763-4037">Tokens within curly braces (`{ ... }`) define *route parameters* that are bound if the route is matched.</span></span> <span data-ttu-id="23763-4038">1 つのルート セグメントで複数のルート パラメーターを定義できますが、リテラル値で区切る必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-4038">You can define more than one route parameter in a route segment, but they must be separated by a literal value.</span></span> <span data-ttu-id="23763-4039">たとえば、`{controller=Home}{action=Index}` は有効なルートになりません。`{controller}` と `{action}` の間にリテラル値がないためです。</span><span class="sxs-lookup"><span data-stu-id="23763-4039">For example, `{controller=Home}{action=Index}` isn't a valid route, since there's no literal value between `{controller}` and `{action}`.</span></span> <span data-ttu-id="23763-4040">このようなルート パラメーターには名前を付ける必要があります。付加的な属性を指定することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-4040">These route parameters must have a name and may have additional attributes specified.</span></span>

<span data-ttu-id="23763-4041">ルート パラメーター以外のリテラル テキスト (`{id}` など) とパス区切り `/` は URL のテキストに一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-4041">Literal text other than route parameters (for example, `{id}`) and the path separator `/` must match the text in the URL.</span></span> <span data-ttu-id="23763-4042">テキスト照合は復号された URL パスを基盤とし、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4042">Text matching is case-insensitive and based on the decoded representation of the URLs path.</span></span> <span data-ttu-id="23763-4043">リテラル ルート パラメーターの区切り記号 (`{` または `}`) を照合するには、文字を繰り返して区切り記号をエスケープします (`{{` または `}}`)。</span><span class="sxs-lookup"><span data-stu-id="23763-4043">To match a literal route parameter delimiter (`{` or `}`), escape the delimiter by repeating the character (`{{` or `}}`).</span></span>

<span data-ttu-id="23763-4044">任意のファイル拡張子が付いたファイル名のキャプチャを試行する URL パターンには、追加の考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="23763-4044">URL patterns that attempt to capture a file name with an optional file extension have additional considerations.</span></span> <span data-ttu-id="23763-4045">たとえば、テンプレート `files/{filename}.{ext?}` について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="23763-4045">For example, consider the template `files/{filename}.{ext?}`.</span></span> <span data-ttu-id="23763-4046">`filename` と `ext` の両方の値が存在するときに、両方の値が入力されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4046">When values for both `filename` and `ext` exist, both values are populated.</span></span> <span data-ttu-id="23763-4047">URL に `filename` の値だけが存在する場合、末尾のピリオド (`.`) は任意であるため、このルートは一致となります。</span><span class="sxs-lookup"><span data-stu-id="23763-4047">If only a value for `filename` exists in the URL, the route matches because the trailing period (`.`) is  optional.</span></span> <span data-ttu-id="23763-4048">次の URL はこのルートに一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4048">The following URLs match this route:</span></span>

* `/files/myFile.txt`
* `/files/myFile`

<span data-ttu-id="23763-4049">ルート パラメーターのプレフィックスとしてアスタリスク (`*`) を使用し、URI の残りの部分にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="23763-4049">You can use the asterisk (`*`) as a prefix to a route parameter to bind to the rest of the URI.</span></span> <span data-ttu-id="23763-4050">これは*キャッチオール* パラメーターと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="23763-4050">This is called a *catch-all* parameter.</span></span> <span data-ttu-id="23763-4051">たとえば、`blog/{*slug}` は、`/blog` で始まり、その後に (`slug` ルート値に割り当てられる) 任意の値が続くあらゆる URI に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4051">For example, `blog/{*slug}` matches any URI that starts with `/blog` and has any value following it, which is assigned to the `slug` route value.</span></span> <span data-ttu-id="23763-4052">キャッチオール パラメーターは空の文字列に一致することもあります。</span><span class="sxs-lookup"><span data-stu-id="23763-4052">Catch-all parameters can also match the empty string.</span></span>

<span data-ttu-id="23763-4053">キャッチオール パラメーターでは、パス区切り (`/`) 文字を含め、URL の生成にルートが使用されるときに適切な文字がエスケープされます。</span><span class="sxs-lookup"><span data-stu-id="23763-4053">The catch-all parameter escapes the appropriate characters when the route is used to generate a URL, including path separator (`/`) characters.</span></span> <span data-ttu-id="23763-4054">たとえば、ルート値が `{ path = "my/path" }` のルート `foo/{*path}` では、`foo/my%2Fpath` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4054">For example, the route `foo/{*path}` with route values `{ path = "my/path" }` generates `foo/my%2Fpath`.</span></span> <span data-ttu-id="23763-4055">エスケープされたスラッシュに注意してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4055">Note the escaped forward slash.</span></span>

<span data-ttu-id="23763-4056">ルート パラメーターには、*既定値* が含まれることがあります。パラメーター名の後に既定値を指定し、等号 (`=`) で区切ることで指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4056">Route parameters may have *default values* designated by specifying the default value after the parameter name separated by an equals sign (`=`).</span></span> <span data-ttu-id="23763-4057">たとえば、`{controller=Home}` では、`controller` の既定値として `Home` が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4057">For example, `{controller=Home}` defines `Home` as the default value for `controller`.</span></span> <span data-ttu-id="23763-4058">パラメーターの URL に値がない場合、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4058">The default value is used if no value is present in the URL for the parameter.</span></span> <span data-ttu-id="23763-4059">ルート パラメーターは、`id?` のように、パラメーター名の終わりに疑問符 (`?`) を追加することでオプションとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-4059">Route parameters are made optional by appending a question mark (`?`) to the end of the parameter name, as in `id?`.</span></span> <span data-ttu-id="23763-4060">オプション値と既定ルート パラメーターの違いは、既定値を含むルート パラメーターは常に値を生成するのに対し、オプションのパラメーターには、要求 URL によって値が指定されたときにのみ値が与えられることにあります。</span><span class="sxs-lookup"><span data-stu-id="23763-4060">The difference between optional values and default route parameters is that a route parameter with a default value always produces a value&mdash;an optional parameter has a value only when a value is provided by the request URL.</span></span>

<span data-ttu-id="23763-4061">ルート パラメーターには、URL からバインドされるルート値に一致しなければならないという制約が含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="23763-4061">Route parameters may have constraints that must match the route value bound from the URL.</span></span> <span data-ttu-id="23763-4062">コロン (`:`) と制約名をルート パラメーター名の後に追加すると、ルート パラメーターの*インライン制約*が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4062">Adding a colon (`:`) and constraint name after the route parameter name specifies an *inline constraint* on a route parameter.</span></span> <span data-ttu-id="23763-4063">その制約で引数が要求される場合、制約名の後にかっこ (`(...)`) で囲まれます。</span><span class="sxs-lookup"><span data-stu-id="23763-4063">If the constraint requires arguments, they're enclosed in parentheses (`(...)`) after the constraint name.</span></span> <span data-ttu-id="23763-4064">別のコロン (`:`) と制約名を追加することで、複数のインライン制約を指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4064">Multiple inline constraints can be specified by appending another colon (`:`) and constraint name.</span></span>

<span data-ttu-id="23763-4065">制約名と引数が <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> サービスに渡され、URL 処理で使用する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4065">The constraint name and arguments are passed to the <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> service to create an instance of <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> to use in URL processing.</span></span> <span data-ttu-id="23763-4066">たとえば、ルート テンプレート `blog/{article:minlength(10)}` によって、制約 `minlength` と引数 `10` が指定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4066">For example, the route template `blog/{article:minlength(10)}` specifies a `minlength` constraint with the argument `10`.</span></span> <span data-ttu-id="23763-4067">ルート制約の詳細とこのフレームワークによって指定される制約のリストについては、「[ルート制約参照](#route-constraint-reference)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4067">For more information on route constraints and a list of the constraints provided by the framework, see the [Route constraint reference](#route-constraint-reference) section.</span></span>

<span data-ttu-id="23763-4068">次の表は、ルート テンプレートの例とその動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-4068">The following table demonstrates example route templates and their behavior.</span></span>

| <span data-ttu-id="23763-4069">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="23763-4069">Route Template</span></span>                           | <span data-ttu-id="23763-4070">一致する URI の例</span><span class="sxs-lookup"><span data-stu-id="23763-4070">Example Matching URI</span></span>    | <span data-ttu-id="23763-4071">要求 URI&hellip;</span><span class="sxs-lookup"><span data-stu-id="23763-4071">The request URI&hellip;</span></span>                                                    |
| ---
<span data-ttu-id="23763-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4072">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4073">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4073">'Blazor'</span></span>
- <span data-ttu-id="23763-4074">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4074">'Identity'</span></span>
- <span data-ttu-id="23763-4075">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4075">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4076">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4076">'Razor'</span></span>
- <span data-ttu-id="23763-4077">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4077">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4078">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4079">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4079">'Blazor'</span></span>
- <span data-ttu-id="23763-4080">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4080">'Identity'</span></span>
- <span data-ttu-id="23763-4081">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4081">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4082">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4082">'Razor'</span></span>
- <span data-ttu-id="23763-4083">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4083">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4084">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4085">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4085">'Blazor'</span></span>
- <span data-ttu-id="23763-4086">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4086">'Identity'</span></span>
- <span data-ttu-id="23763-4087">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4087">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4088">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4088">'Razor'</span></span>
- <span data-ttu-id="23763-4089">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4089">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4090">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4091">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4091">'Blazor'</span></span>
- <span data-ttu-id="23763-4092">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4092">'Identity'</span></span>
- <span data-ttu-id="23763-4093">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4093">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4094">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4094">'Razor'</span></span>
- <span data-ttu-id="23763-4095">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4095">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4096">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4097">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4097">'Blazor'</span></span>
- <span data-ttu-id="23763-4098">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4098">'Identity'</span></span>
- <span data-ttu-id="23763-4099">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4099">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4100">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4100">'Razor'</span></span>
- <span data-ttu-id="23763-4101">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4101">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4102">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4103">'Blazor'</span></span>
- <span data-ttu-id="23763-4104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4104">'Identity'</span></span>
- <span data-ttu-id="23763-4105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4105">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4106">'Razor'</span></span>
- <span data-ttu-id="23763-4107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4107">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4108">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4109">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4109">'Blazor'</span></span>
- <span data-ttu-id="23763-4110">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4110">'Identity'</span></span>
- <span data-ttu-id="23763-4111">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4111">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4112">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4112">'Razor'</span></span>
- <span data-ttu-id="23763-4113">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4113">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4114">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4115">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4115">'Blazor'</span></span>
- <span data-ttu-id="23763-4116">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4116">'Identity'</span></span>
- <span data-ttu-id="23763-4117">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4117">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4118">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4118">'Razor'</span></span>
- <span data-ttu-id="23763-4119">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4119">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4120">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4121">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4121">'Blazor'</span></span>
- <span data-ttu-id="23763-4122">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4122">'Identity'</span></span>
- <span data-ttu-id="23763-4123">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4123">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4124">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4124">'Razor'</span></span>
- <span data-ttu-id="23763-4125">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4125">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4126">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4127">'Blazor'</span></span>
- <span data-ttu-id="23763-4128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4128">'Identity'</span></span>
- <span data-ttu-id="23763-4129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4129">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4130">'Razor'</span></span>
- <span data-ttu-id="23763-4131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4133">'Blazor'</span></span>
- <span data-ttu-id="23763-4134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4134">'Identity'</span></span>
- <span data-ttu-id="23763-4135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4135">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4136">'Razor'</span></span>
- <span data-ttu-id="23763-4137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4139">'Blazor'</span></span>
- <span data-ttu-id="23763-4140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4140">'Identity'</span></span>
- <span data-ttu-id="23763-4141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4141">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4142">'Razor'</span></span>
- <span data-ttu-id="23763-4143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4145">'Blazor'</span></span>
- <span data-ttu-id="23763-4146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4146">'Identity'</span></span>
- <span data-ttu-id="23763-4147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4147">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4148">'Razor'</span></span>
- <span data-ttu-id="23763-4149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4151">'Blazor'</span></span>
- <span data-ttu-id="23763-4152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4152">'Identity'</span></span>
- <span data-ttu-id="23763-4153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4153">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4154">'Razor'</span></span>
- <span data-ttu-id="23763-4155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4157">'Blazor'</span></span>
- <span data-ttu-id="23763-4158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4158">'Identity'</span></span>
- <span data-ttu-id="23763-4159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4159">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4160">'Razor'</span></span>
- <span data-ttu-id="23763-4161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4162">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4163">'Blazor'</span></span>
- <span data-ttu-id="23763-4164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4164">'Identity'</span></span>
- <span data-ttu-id="23763-4165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4165">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4166">'Razor'</span></span>
- <span data-ttu-id="23763-4167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4169">'Blazor'</span></span>
- <span data-ttu-id="23763-4170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4170">'Identity'</span></span>
- <span data-ttu-id="23763-4171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4171">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4172">'Razor'</span></span>
- <span data-ttu-id="23763-4173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4175">'Blazor'</span></span>
- <span data-ttu-id="23763-4176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4176">'Identity'</span></span>
- <span data-ttu-id="23763-4177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4177">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4178">'Razor'</span></span>
- <span data-ttu-id="23763-4179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4179">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4180">-------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4181">'Blazor'</span></span>
- <span data-ttu-id="23763-4182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4182">'Identity'</span></span>
- <span data-ttu-id="23763-4183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4183">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4184">'Razor'</span></span>
- <span data-ttu-id="23763-4185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4186">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4187">'Blazor'</span></span>
- <span data-ttu-id="23763-4188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4188">'Identity'</span></span>
- <span data-ttu-id="23763-4189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4189">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4190">'Razor'</span></span>
- <span data-ttu-id="23763-4191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4192">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4193">'Blazor'</span></span>
- <span data-ttu-id="23763-4194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4194">'Identity'</span></span>
- <span data-ttu-id="23763-4195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4195">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4196">'Razor'</span></span>
- <span data-ttu-id="23763-4197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4198">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4199">'Blazor'</span></span>
- <span data-ttu-id="23763-4200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4200">'Identity'</span></span>
- <span data-ttu-id="23763-4201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4201">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4202">'Razor'</span></span>
- <span data-ttu-id="23763-4203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4204">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4205">'Blazor'</span></span>
- <span data-ttu-id="23763-4206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4206">'Identity'</span></span>
- <span data-ttu-id="23763-4207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4207">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4208">'Razor'</span></span>
- <span data-ttu-id="23763-4209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4210">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4211">'Blazor'</span></span>
- <span data-ttu-id="23763-4212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4212">'Identity'</span></span>
- <span data-ttu-id="23763-4213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4213">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4214">'Razor'</span></span>
- <span data-ttu-id="23763-4215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4216">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4217">'Blazor'</span></span>
- <span data-ttu-id="23763-4218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4218">'Identity'</span></span>
- <span data-ttu-id="23763-4219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4219">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4220">'Razor'</span></span>
- <span data-ttu-id="23763-4221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4222">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4223">'Blazor'</span></span>
- <span data-ttu-id="23763-4224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4224">'Identity'</span></span>
- <span data-ttu-id="23763-4225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4225">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4226">'Razor'</span></span>
- <span data-ttu-id="23763-4227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4228">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4229">'Blazor'</span></span>
- <span data-ttu-id="23763-4230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4230">'Identity'</span></span>
- <span data-ttu-id="23763-4231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4231">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4232">'Razor'</span></span>
- <span data-ttu-id="23763-4233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4233">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4234">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4235">'Blazor'</span></span>
- <span data-ttu-id="23763-4236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4236">'Identity'</span></span>
- <span data-ttu-id="23763-4237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4237">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4238">'Razor'</span></span>
- <span data-ttu-id="23763-4239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4241">'Blazor'</span></span>
- <span data-ttu-id="23763-4242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4242">'Identity'</span></span>
- <span data-ttu-id="23763-4243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4243">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4244">'Razor'</span></span>
- <span data-ttu-id="23763-4245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4247">'Blazor'</span></span>
- <span data-ttu-id="23763-4248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4248">'Identity'</span></span>
- <span data-ttu-id="23763-4249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4249">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4250">'Razor'</span></span>
- <span data-ttu-id="23763-4251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4253">'Blazor'</span></span>
- <span data-ttu-id="23763-4254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4254">'Identity'</span></span>
- <span data-ttu-id="23763-4255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4255">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4256">'Razor'</span></span>
- <span data-ttu-id="23763-4257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4259">'Blazor'</span></span>
- <span data-ttu-id="23763-4260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4260">'Identity'</span></span>
- <span data-ttu-id="23763-4261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4261">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4262">'Razor'</span></span>
- <span data-ttu-id="23763-4263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4265">'Blazor'</span></span>
- <span data-ttu-id="23763-4266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4266">'Identity'</span></span>
- <span data-ttu-id="23763-4267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4267">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4268">'Razor'</span></span>
- <span data-ttu-id="23763-4269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4271">'Blazor'</span></span>
- <span data-ttu-id="23763-4272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4272">'Identity'</span></span>
- <span data-ttu-id="23763-4273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4273">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4274">'Razor'</span></span>
- <span data-ttu-id="23763-4275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4277">'Blazor'</span></span>
- <span data-ttu-id="23763-4278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4278">'Identity'</span></span>
- <span data-ttu-id="23763-4279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4279">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4280">'Razor'</span></span>
- <span data-ttu-id="23763-4281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4283">'Blazor'</span></span>
- <span data-ttu-id="23763-4284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4284">'Identity'</span></span>
- <span data-ttu-id="23763-4285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4285">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4286">'Razor'</span></span>
- <span data-ttu-id="23763-4287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4289">'Blazor'</span></span>
- <span data-ttu-id="23763-4290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4290">'Identity'</span></span>
- <span data-ttu-id="23763-4291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4291">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4292">'Razor'</span></span>
- <span data-ttu-id="23763-4293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4295">'Blazor'</span></span>
- <span data-ttu-id="23763-4296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4296">'Identity'</span></span>
- <span data-ttu-id="23763-4297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4297">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4298">'Razor'</span></span>
- <span data-ttu-id="23763-4299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4301">'Blazor'</span></span>
- <span data-ttu-id="23763-4302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4302">'Identity'</span></span>
- <span data-ttu-id="23763-4303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4303">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4304">'Razor'</span></span>
- <span data-ttu-id="23763-4305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4307">'Blazor'</span></span>
- <span data-ttu-id="23763-4308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4308">'Identity'</span></span>
- <span data-ttu-id="23763-4309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4309">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4310">'Razor'</span></span>
- <span data-ttu-id="23763-4311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4313">'Blazor'</span></span>
- <span data-ttu-id="23763-4314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4314">'Identity'</span></span>
- <span data-ttu-id="23763-4315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4315">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4316">'Razor'</span></span>
- <span data-ttu-id="23763-4317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4319">'Blazor'</span></span>
- <span data-ttu-id="23763-4320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4320">'Identity'</span></span>
- <span data-ttu-id="23763-4321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4321">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4322">'Razor'</span></span>
- <span data-ttu-id="23763-4323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4325">'Blazor'</span></span>
- <span data-ttu-id="23763-4326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4326">'Identity'</span></span>
- <span data-ttu-id="23763-4327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4327">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4328">'Razor'</span></span>
- <span data-ttu-id="23763-4329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4331">'Blazor'</span></span>
- <span data-ttu-id="23763-4332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4332">'Identity'</span></span>
- <span data-ttu-id="23763-4333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4333">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4334">'Razor'</span></span>
- <span data-ttu-id="23763-4335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4337">'Blazor'</span></span>
- <span data-ttu-id="23763-4338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4338">'Identity'</span></span>
- <span data-ttu-id="23763-4339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4339">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4340">'Razor'</span></span>
- <span data-ttu-id="23763-4341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4343">'Blazor'</span></span>
- <span data-ttu-id="23763-4344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4344">'Identity'</span></span>
- <span data-ttu-id="23763-4345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4345">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4346">'Razor'</span></span>
- <span data-ttu-id="23763-4347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4349">'Blazor'</span></span>
- <span data-ttu-id="23763-4350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4350">'Identity'</span></span>
- <span data-ttu-id="23763-4351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4351">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4352">'Razor'</span></span>
- <span data-ttu-id="23763-4353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4355">'Blazor'</span></span>
- <span data-ttu-id="23763-4356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4356">'Identity'</span></span>
- <span data-ttu-id="23763-4357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4357">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4358">'Razor'</span></span>
- <span data-ttu-id="23763-4359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4361">'Blazor'</span></span>
- <span data-ttu-id="23763-4362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4362">'Identity'</span></span>
- <span data-ttu-id="23763-4363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4363">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4364">'Razor'</span></span>
- <span data-ttu-id="23763-4365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4367">'Blazor'</span></span>
- <span data-ttu-id="23763-4368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4368">'Identity'</span></span>
- <span data-ttu-id="23763-4369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4369">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4370">'Razor'</span></span>
- <span data-ttu-id="23763-4371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4373">'Blazor'</span></span>
- <span data-ttu-id="23763-4374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4374">'Identity'</span></span>
- <span data-ttu-id="23763-4375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4375">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4376">'Razor'</span></span>
- <span data-ttu-id="23763-4377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4379">'Blazor'</span></span>
- <span data-ttu-id="23763-4380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4380">'Identity'</span></span>
- <span data-ttu-id="23763-4381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4381">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4382">'Razor'</span></span>
- <span data-ttu-id="23763-4383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4385">'Blazor'</span></span>
- <span data-ttu-id="23763-4386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4386">'Identity'</span></span>
- <span data-ttu-id="23763-4387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4387">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4388">'Razor'</span></span>
- <span data-ttu-id="23763-4389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4391">'Blazor'</span></span>
- <span data-ttu-id="23763-4392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4392">'Identity'</span></span>
- <span data-ttu-id="23763-4393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4393">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4394">'Razor'</span></span>
- <span data-ttu-id="23763-4395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4397">'Blazor'</span></span>
- <span data-ttu-id="23763-4398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4398">'Identity'</span></span>
- <span data-ttu-id="23763-4399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4399">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4400">'Razor'</span></span>
- <span data-ttu-id="23763-4401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4403">'Blazor'</span></span>
- <span data-ttu-id="23763-4404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4404">'Identity'</span></span>
- <span data-ttu-id="23763-4405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4405">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4406">'Razor'</span></span>
- <span data-ttu-id="23763-4407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4409">'Blazor'</span></span>
- <span data-ttu-id="23763-4410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4410">'Identity'</span></span>
- <span data-ttu-id="23763-4411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4411">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4412">'Razor'</span></span>
- <span data-ttu-id="23763-4413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4415">'Blazor'</span></span>
- <span data-ttu-id="23763-4416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4416">'Identity'</span></span>
- <span data-ttu-id="23763-4417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4417">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4418">'Razor'</span></span>
- <span data-ttu-id="23763-4419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4421">'Blazor'</span></span>
- <span data-ttu-id="23763-4422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4422">'Identity'</span></span>
- <span data-ttu-id="23763-4423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4423">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4424">'Razor'</span></span>
- <span data-ttu-id="23763-4425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4427">'Blazor'</span></span>
- <span data-ttu-id="23763-4428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4428">'Identity'</span></span>
- <span data-ttu-id="23763-4429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4429">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4430">'Razor'</span></span>
- <span data-ttu-id="23763-4431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4433">'Blazor'</span></span>
- <span data-ttu-id="23763-4434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4434">'Identity'</span></span>
- <span data-ttu-id="23763-4435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4435">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4436">'Razor'</span></span>
- <span data-ttu-id="23763-4437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4439">'Blazor'</span></span>
- <span data-ttu-id="23763-4440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4440">'Identity'</span></span>
- <span data-ttu-id="23763-4441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4441">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4442">'Razor'</span></span>
- <span data-ttu-id="23763-4443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4443">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4444">------------------------------------- | | `hello`                                  | `/hello`                | 単一パス `/hello` にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4444">------------------------------------- | | `hello`                                  | `/hello`                | Only matches the single path `/hello`.</span></span>                                     <span data-ttu-id="23763-4445">| | `{Page=Home}`                            | `/`                     | 一致し、`Page` が `Home` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4445">| | `{Page=Home}`                            | `/`                     | Matches and sets `Page` to `Home`.</span></span>                                         <span data-ttu-id="23763-4446">| | `{Page=Home}`                            | `/Contact`              | 一致し、`Page` が `Contact` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4446">| | `{Page=Home}`                            | `/Contact`              | Matches and sets `Page` to `Contact`.</span></span>                                      <span data-ttu-id="23763-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | `Products` コントローラーと `List` アクションにマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="23763-4447">| | `{controller}/{action}/{id?}`            | `/Products/List`        | Maps to the `Products` controller and `List` action.</span></span>                       <span data-ttu-id="23763-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | `Products` コントローラーと `Details` アクションにマッピングされます (`id` は 123 に設定されます)。</span><span class="sxs-lookup"><span data-stu-id="23763-4448">| | `{controller}/{action}/{id?}`            | `/Products/Details/123` | Maps to the `Products` controller and  `Details` action (`id` set to 123).</span></span> <span data-ttu-id="23763-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | `Home` コントローラーと `Index` メソッドにマッピングされます (`id` は無視されます)。</span><span class="sxs-lookup"><span data-stu-id="23763-4449">| | `{controller=Home}/{action=Index}/{id?}` | `/`                     | Maps to the `Home` controller and `Index` method (`id` is ignored).</span></span>        |

<span data-ttu-id="23763-4450">一般的に、テンプレートの利用が最も簡単なルーティングの手法となります。</span><span class="sxs-lookup"><span data-stu-id="23763-4450">Using a template is generally the simplest approach to routing.</span></span> <span data-ttu-id="23763-4451">ルート テンプレート以外では、制約と既定値も指定できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4451">Constraints and defaults can also be specified outside the route template.</span></span>

> [!TIP]
> <span data-ttu-id="23763-4452">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4452">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

## <a name="route-constraint-reference"></a><span data-ttu-id="23763-4453">ルート制約参照</span><span class="sxs-lookup"><span data-stu-id="23763-4453">Route constraint reference</span></span>

<span data-ttu-id="23763-4454">ルート制約は、受信 URL と一致し、URL パスがルート値にトークン化されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4454">Route constraints execute when a match has occurred to the incoming URL and the URL path is tokenized into route values.</span></span> <span data-ttu-id="23763-4455">ルート制約では、通常、ルート テンプレート経由で関連付けられるルート値を調べ、値が許容できるかどうかをはい/いいえで決定します。</span><span class="sxs-lookup"><span data-stu-id="23763-4455">Route constraints generally inspect the route value associated via the route template and make a yes/no decision about whether or not the value is acceptable.</span></span> <span data-ttu-id="23763-4456">一部のルート制約では、ルート値以外のデータを使用し、要求をルーティングできるかどうかが考慮されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4456">Some route constraints use data outside the route value to consider whether the request can be routed.</span></span> <span data-ttu-id="23763-4457">たとえば、<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> はその HTTP Verb に基づいて要求を承認または却下します。</span><span class="sxs-lookup"><span data-stu-id="23763-4457">For example, the <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> can accept or reject a request based on its HTTP verb.</span></span> <span data-ttu-id="23763-4458">制約は、要求のルーティングとリンクの生成で使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4458">Constraints are used in routing requests and link generation.</span></span>

> [!WARNING]
> <span data-ttu-id="23763-4459">**入力の検証**には制約を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="23763-4459">Don't use constraints for **input validation**.</span></span> <span data-ttu-id="23763-4460">**入力の検証**に制約が使用されると、無効な入力の結果、*400 - Bad Request* と適切なエラー メッセージではなく、*404 - Not Found* が表示されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4460">If constraints are used for **input validation**, invalid input results in a *404 - Not Found* response instead of a *400 - Bad Request* with an appropriate error message.</span></span> <span data-ttu-id="23763-4461">ルート制約は、特定のルートに対する入力の妥当性を検証するためではなく、似たようなルートの**違いを明らかにする**ために使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4461">Route constraints are used to **disambiguate** similar routes, not to validate the inputs for a particular route.</span></span>

<span data-ttu-id="23763-4462">次の表は、ルート制約の例とそれに求められる動作をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-4462">The following table demonstrates example route constraints and their expected behavior.</span></span>

| <span data-ttu-id="23763-4463">制約</span><span class="sxs-lookup"><span data-stu-id="23763-4463">constraint</span></span> | <span data-ttu-id="23763-4464">例</span><span class="sxs-lookup"><span data-stu-id="23763-4464">Example</span></span> | <span data-ttu-id="23763-4465">一致の例</span><span class="sxs-lookup"><span data-stu-id="23763-4465">Example Matches</span></span> | <span data-ttu-id="23763-4466">メモ</span><span class="sxs-lookup"><span data-stu-id="23763-4466">Notes</span></span> |
| ---
<span data-ttu-id="23763-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4467">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4468">'Blazor'</span></span>
- <span data-ttu-id="23763-4469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4469">'Identity'</span></span>
- <span data-ttu-id="23763-4470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4470">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4471">'Razor'</span></span>
- <span data-ttu-id="23763-4472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4473">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4474">'Blazor'</span></span>
- <span data-ttu-id="23763-4475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4475">'Identity'</span></span>
- <span data-ttu-id="23763-4476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4476">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4477">'Razor'</span></span>
- <span data-ttu-id="23763-4478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4478">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4479">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4480">'Blazor'</span></span>
- <span data-ttu-id="23763-4481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4481">'Identity'</span></span>
- <span data-ttu-id="23763-4482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4482">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4483">'Razor'</span></span>
- <span data-ttu-id="23763-4484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4484">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4485">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4486">'Blazor'</span></span>
- <span data-ttu-id="23763-4487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4487">'Identity'</span></span>
- <span data-ttu-id="23763-4488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4488">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4489">'Razor'</span></span>
- <span data-ttu-id="23763-4490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4490">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4491">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4492">'Blazor'</span></span>
- <span data-ttu-id="23763-4493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4493">'Identity'</span></span>
- <span data-ttu-id="23763-4494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4494">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4495">'Razor'</span></span>
- <span data-ttu-id="23763-4496">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4496">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4497">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4498">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4498">'Blazor'</span></span>
- <span data-ttu-id="23763-4499">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4499">'Identity'</span></span>
- <span data-ttu-id="23763-4500">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4500">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4501">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4501">'Razor'</span></span>
- <span data-ttu-id="23763-4502">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4502">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4503">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4504">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4504">'Blazor'</span></span>
- <span data-ttu-id="23763-4505">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4505">'Identity'</span></span>
- <span data-ttu-id="23763-4506">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4506">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4507">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4507">'Razor'</span></span>
- <span data-ttu-id="23763-4508">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4508">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4510">'Blazor'</span></span>
- <span data-ttu-id="23763-4511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4511">'Identity'</span></span>
- <span data-ttu-id="23763-4512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4512">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4513">'Razor'</span></span>
- <span data-ttu-id="23763-4514">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4516">'Blazor'</span></span>
- <span data-ttu-id="23763-4517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4517">'Identity'</span></span>
- <span data-ttu-id="23763-4518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4518">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4519">'Razor'</span></span>
- <span data-ttu-id="23763-4520">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4520">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`、`-123456789` | 任意の整数に一致します | | `bool` | `{active:bool}` | `true`、`FALSE` | `true` または `false` に一致します (大文字と小文字は区別されません) | | `datetime` | `{dob:datetime}` | `2016-12-31`、`2016-12-31 7:32pm` | インバリアント カルチャの有効な `DateTime` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4521">-------- | ----- | | `int` | `{id:int}` | `123456789`, `-123456789` | Matches any integer | | `bool` | `{active:bool}` | `true`, `FALSE` | Matches `true` or `false` (case-insensitive) | | `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Matches a valid `DateTime` value in the invariant culture.</span></span> <span data-ttu-id="23763-4522">前の警告を参照してください。| | `decimal` | `{price:decimal}` | `49.99`、`-1,000.01` | インバリアント カルチャの有効な `decimal` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4522">See  preceding warning.| | `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Matches a valid `decimal` value in the invariant culture.</span></span> <span data-ttu-id="23763-4523">前の警告を参照してください。| | `double` | `{weight:double}` | `1.234`、`-1,001.01e8` | インバリアント カルチャの有効な `double` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4523">See  preceding warning.| | `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Matches a valid `double` value in the invariant culture.</span></span> <span data-ttu-id="23763-4524">前の警告を参照してください。| | `float` | `{weight:float}` | `1.234`、`-1,001.01e8` | インバリアント カルチャの有効な `float` 値に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4524">See  preceding warning.| | `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Matches a valid `float` value in the invariant culture.</span></span> <span data-ttu-id="23763-4525">前の警告を参照してください。| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 有効な `Guid` 値に一致します | | `long` | `{ticks:long}` | `123456789`、`-123456789` | 有効な `long` 値に一致します | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | 文字列は 4 文字以上でなければなりません | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 文字列は 8 文字以下でなければなりません | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | 文字列は正確に 12 文字でなければなりません | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 文字列は 8 文字以上 16 文字以下でなければなりません | | `min(value)` | `{age:min(18)}` | `19` | 整数値は 18 以上でなければなりません | | `max(value)` | `{age:max(120)}` | `91` | 整数値は 120 以下でなければなりません | | `range(min,max)` | `{age:range(18,120)}` | `91` | 整数値は 18 以上 120 以下でなければなりません | | `alpha` | `{name:alpha}` | `Rick` | 文字列は 1 つ以上の英字で構成されている必要があります (`a`-`z`、大文字と小文字は区別されません) | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 文字列は正規式に一致していなければなりません (正規式の定義に関するヒントを参照) | | `required` | `{name:required}` | `Rick` | URL 生成中、非パラメーター値が提示されるように強制するために使用されます |</span><span class="sxs-lookup"><span data-stu-id="23763-4525">See  preceding warning.| | `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Matches a valid `Guid` value | | `long` | `{ticks:long}` | `123456789`, `-123456789` | Matches a valid `long` value | | `minlength(value)` | `{username:minlength(4)}` | `Rick` | String must be at least 4 characters | | `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | String must be no more than 8 characters | | `length(length)` | `{filename:length(12)}` | `somefile.txt` | String must be exactly 12 characters long | | `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | String must be at least 8 and no more than 16 characters long | | `min(value)` | `{age:min(18)}` | `19` | Integer value must be at least 18 | | `max(value)` | `{age:max(120)}` | `91` | Integer value must be no more than 120 | | `range(min,max)` | `{age:range(18,120)}` | `91` | Integer value must be at least 18 but no more than 120 | | `alpha` | `{name:alpha}` | `Rick` | String must consist of one or more alphabetical characters (`a`-`z`, case-insensitive) | | `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | String must match the regular expression (see tips about defining a regular expression) | | `required` | `{name:required}` | `Rick` | Used to enforce that a non-parameter value is present during URL generation |</span></span>

<span data-ttu-id="23763-4526">コロンで区切られた複数の制約を単一のパラメーターに適用することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-4526">Multiple, colon-delimited constraints can be applied to a single parameter.</span></span> <span data-ttu-id="23763-4527">たとえば、次の制約では、パラメーターが 1 以上の整数値に制限されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4527">For example, the following constraint restricts a parameter to an integer value of 1 or greater:</span></span>

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> <span data-ttu-id="23763-4528">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4528">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="23763-4529">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="23763-4529">These constraints assume that the URL is non-localizable.</span></span> <span data-ttu-id="23763-4530">フレームワークから提供されるルート制約がルート値に格納されている値を変更することはありません。</span><span class="sxs-lookup"><span data-stu-id="23763-4530">The framework-provided route constraints don't modify the values stored in route values.</span></span> <span data-ttu-id="23763-4531">URL から解析されたルート値はすべて文字列として格納されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4531">All route values parsed from the URL are stored as strings.</span></span> <span data-ttu-id="23763-4532">たとえば、`float` 制約はルート値を浮動小数に変換しますが、変換された値は、浮動小数に変換できることを検証するためにだけ利用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4532">For example, the `float` constraint attempts to convert the route value to a float, but the converted value is used only to verify it can be converted to a float.</span></span>

## <a name="regular-expressions"></a><span data-ttu-id="23763-4533">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-4533">Regular expressions</span></span>

<span data-ttu-id="23763-4534">ASP.NET Core フレームワークでは、正規表現コンストラクターに `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` が追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4534">The ASP.NET Core framework adds `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` to the regular expression constructor.</span></span> <span data-ttu-id="23763-4535">これらのメンバーの詳細については、「<xref:System.Text.RegularExpressions.RegexOptions>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4535">See <xref:System.Text.RegularExpressions.RegexOptions> for a description of these members.</span></span>

<span data-ttu-id="23763-4536">正規表現では、ルーティングや C# 言語で使用されるものに似た区切り記号とトークンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4536">Regular expressions use delimiters and tokens similar to those used by Routing and the C# language.</span></span> <span data-ttu-id="23763-4537">正規表現トークンはエスケープする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-4537">Regular expression tokens must be escaped.</span></span> <span data-ttu-id="23763-4538">ルーティングで正規表現 `^\d{3}-\d{2}-\d{4}$` を使用するには、C# ソース ファイルで `\\` (二重円記号) 文字として文字列に `\` (単一の円記号) 文字を指定し、文字列エスケープ文字である `\` をエスケープする必要があります ([逐語的な文字列リテラル](/dotnet/csharp/language-reference/keywords/string)を使用しない限り)。</span><span class="sxs-lookup"><span data-stu-id="23763-4538">To use the regular expression `^\d{3}-\d{2}-\d{4}$` in routing, the expression must have the `\` (single backslash) characters provided in the string as `\\` (double backslash) characters in the C# source file in order to escape the `\` string escape character (unless using [verbatim string literals](/dotnet/csharp/language-reference/keywords/string)).</span></span> <span data-ttu-id="23763-4539">ルーティング パラメーター区切り記号文字 (`{`、`}`、`[`、`]`) をエスケープするには、表現の文字を二重にします (`{{`、`}`、`[[`、`]]`)。</span><span class="sxs-lookup"><span data-stu-id="23763-4539">To escape routing parameter delimiter characters (`{`, `}`, `[`, `]`), double the characters in the expression (`{{`, `}`, `[[`, `]]`).</span></span> <span data-ttu-id="23763-4540">次の表に、正規表現とエスケープ適用後のものを示します。</span><span class="sxs-lookup"><span data-stu-id="23763-4540">The following table shows a regular expression and the escaped version.</span></span>

| <span data-ttu-id="23763-4541">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-4541">Regular Expression</span></span>    | <span data-ttu-id="23763-4542">エスケープ適用後の正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-4542">Escaped Regular Expression</span></span>     |
| ---
<span data-ttu-id="23763-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4544">'Blazor'</span></span>
- <span data-ttu-id="23763-4545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4545">'Identity'</span></span>
- <span data-ttu-id="23763-4546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4546">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4547">'Razor'</span></span>
- <span data-ttu-id="23763-4548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4550">'Blazor'</span></span>
- <span data-ttu-id="23763-4551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4551">'Identity'</span></span>
- <span data-ttu-id="23763-4552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4552">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4553">'Razor'</span></span>
- <span data-ttu-id="23763-4554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4556">'Blazor'</span></span>
- <span data-ttu-id="23763-4557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4557">'Identity'</span></span>
- <span data-ttu-id="23763-4558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4558">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4559">'Razor'</span></span>
- <span data-ttu-id="23763-4560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4562">'Blazor'</span></span>
- <span data-ttu-id="23763-4563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4563">'Identity'</span></span>
- <span data-ttu-id="23763-4564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4564">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4565">'Razor'</span></span>
- <span data-ttu-id="23763-4566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4568">'Blazor'</span></span>
- <span data-ttu-id="23763-4569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4569">'Identity'</span></span>
- <span data-ttu-id="23763-4570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4570">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4571">'Razor'</span></span>
- <span data-ttu-id="23763-4572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4574">'Blazor'</span></span>
- <span data-ttu-id="23763-4575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4575">'Identity'</span></span>
- <span data-ttu-id="23763-4576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4576">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4577">'Razor'</span></span>
- <span data-ttu-id="23763-4578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4580">'Blazor'</span></span>
- <span data-ttu-id="23763-4581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4581">'Identity'</span></span>
- <span data-ttu-id="23763-4582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4582">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4583">'Razor'</span></span>
- <span data-ttu-id="23763-4584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4586">'Blazor'</span></span>
- <span data-ttu-id="23763-4587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4587">'Identity'</span></span>
- <span data-ttu-id="23763-4588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4588">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4589">'Razor'</span></span>
- <span data-ttu-id="23763-4590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4590">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4591">----------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4592">'Blazor'</span></span>
- <span data-ttu-id="23763-4593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4593">'Identity'</span></span>
- <span data-ttu-id="23763-4594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4594">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4595">'Razor'</span></span>
- <span data-ttu-id="23763-4596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4598">'Blazor'</span></span>
- <span data-ttu-id="23763-4599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4599">'Identity'</span></span>
- <span data-ttu-id="23763-4600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4600">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4601">'Razor'</span></span>
- <span data-ttu-id="23763-4602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4604">'Blazor'</span></span>
- <span data-ttu-id="23763-4605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4605">'Identity'</span></span>
- <span data-ttu-id="23763-4606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4606">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4607">'Razor'</span></span>
- <span data-ttu-id="23763-4608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4610">'Blazor'</span></span>
- <span data-ttu-id="23763-4611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4611">'Identity'</span></span>
- <span data-ttu-id="23763-4612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4612">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4613">'Razor'</span></span>
- <span data-ttu-id="23763-4614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4616">'Blazor'</span></span>
- <span data-ttu-id="23763-4617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4617">'Identity'</span></span>
- <span data-ttu-id="23763-4618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4618">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4619">'Razor'</span></span>
- <span data-ttu-id="23763-4620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4622">'Blazor'</span></span>
- <span data-ttu-id="23763-4623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4623">'Identity'</span></span>
- <span data-ttu-id="23763-4624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4624">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4625">'Razor'</span></span>
- <span data-ttu-id="23763-4626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4628">'Blazor'</span></span>
- <span data-ttu-id="23763-4629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4629">'Identity'</span></span>
- <span data-ttu-id="23763-4630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4630">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4631">'Razor'</span></span>
- <span data-ttu-id="23763-4632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4634">'Blazor'</span></span>
- <span data-ttu-id="23763-4635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4635">'Identity'</span></span>
- <span data-ttu-id="23763-4636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4636">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4637">'Razor'</span></span>
- <span data-ttu-id="23763-4638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4640">'Blazor'</span></span>
- <span data-ttu-id="23763-4641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4641">'Identity'</span></span>
- <span data-ttu-id="23763-4642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4642">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4643">'Razor'</span></span>
- <span data-ttu-id="23763-4644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4646">'Blazor'</span></span>
- <span data-ttu-id="23763-4647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4647">'Identity'</span></span>
- <span data-ttu-id="23763-4648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4648">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4649">'Razor'</span></span>
- <span data-ttu-id="23763-4650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4652">'Blazor'</span></span>
- <span data-ttu-id="23763-4653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4653">'Identity'</span></span>
- <span data-ttu-id="23763-4654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4654">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4655">'Razor'</span></span>
- <span data-ttu-id="23763-4656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4658">'Blazor'</span></span>
- <span data-ttu-id="23763-4659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4659">'Identity'</span></span>
- <span data-ttu-id="23763-4660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4660">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4661">'Razor'</span></span>
- <span data-ttu-id="23763-4662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4664">'Blazor'</span></span>
- <span data-ttu-id="23763-4665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4665">'Identity'</span></span>
- <span data-ttu-id="23763-4666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4666">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4667">'Razor'</span></span>
- <span data-ttu-id="23763-4668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4668">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span><span class="sxs-lookup"><span data-stu-id="23763-4669">--------------- | | `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |</span></span>

<span data-ttu-id="23763-4670">ルーティングで使用される正規表現は、多くの場合、キャレット (`^`) 文字で始まり、文字列の開始位置と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4670">Regular expressions used in routing often start with the caret (`^`) character and match starting position of the string.</span></span> <span data-ttu-id="23763-4671">表現は、多くの場合、ドル記号 (`$`) で終わり、文字列の末尾と一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4671">The expressions often end with the dollar sign (`$`) character and match end of the string.</span></span> <span data-ttu-id="23763-4672">`^` 文字と `$` 文字により、正規表現はルート パラメーター値全体に一致します。</span><span class="sxs-lookup"><span data-stu-id="23763-4672">The `^` and `$` characters ensure that the regular expression match the entire route parameter value.</span></span> <span data-ttu-id="23763-4673">`^` 文字と `$` 文字がなければ、正規表現は、文字列内のあらゆる部分文字列に一致します。それは多くの場合、意図に反することです。</span><span class="sxs-lookup"><span data-stu-id="23763-4673">Without the `^` and `$` characters, the regular expression match any substring within the string, which is often undesirable.</span></span> <span data-ttu-id="23763-4674">下の表では例を示し、それらが一致する、または一致しない理由について説明します。</span><span class="sxs-lookup"><span data-stu-id="23763-4674">The following table provides examples and explains why they match or fail to match.</span></span>

| <span data-ttu-id="23763-4675">正規表現</span><span class="sxs-lookup"><span data-stu-id="23763-4675">Expression</span></span>   | <span data-ttu-id="23763-4676">String</span><span class="sxs-lookup"><span data-stu-id="23763-4676">String</span></span>    | <span data-ttu-id="23763-4677">一致したもの</span><span class="sxs-lookup"><span data-stu-id="23763-4677">Match</span></span> | <span data-ttu-id="23763-4678">コメント</span><span class="sxs-lookup"><span data-stu-id="23763-4678">Comment</span></span>               |
| ---
<span data-ttu-id="23763-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4680">'Blazor'</span></span>
- <span data-ttu-id="23763-4681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4681">'Identity'</span></span>
- <span data-ttu-id="23763-4682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4682">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4683">'Razor'</span></span>
- <span data-ttu-id="23763-4684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4684">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4685">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4686">'Blazor'</span></span>
- <span data-ttu-id="23763-4687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4687">'Identity'</span></span>
- <span data-ttu-id="23763-4688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4688">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4689">'Razor'</span></span>
- <span data-ttu-id="23763-4690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4692">'Blazor'</span></span>
- <span data-ttu-id="23763-4693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4693">'Identity'</span></span>
- <span data-ttu-id="23763-4694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4694">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4695">'Razor'</span></span>
- <span data-ttu-id="23763-4696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4698">'Blazor'</span></span>
- <span data-ttu-id="23763-4699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4699">'Identity'</span></span>
- <span data-ttu-id="23763-4700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4700">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4701">'Razor'</span></span>
- <span data-ttu-id="23763-4702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4702">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4703">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4704">'Blazor'</span></span>
- <span data-ttu-id="23763-4705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4705">'Identity'</span></span>
- <span data-ttu-id="23763-4706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4706">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4707">'Razor'</span></span>
- <span data-ttu-id="23763-4708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4710">'Blazor'</span></span>
- <span data-ttu-id="23763-4711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4711">'Identity'</span></span>
- <span data-ttu-id="23763-4712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4712">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4713">'Razor'</span></span>
- <span data-ttu-id="23763-4714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4714">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4715">----- | :---: |  --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4716">'Blazor'</span></span>
- <span data-ttu-id="23763-4717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4717">'Identity'</span></span>
- <span data-ttu-id="23763-4718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4718">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4719">'Razor'</span></span>
- <span data-ttu-id="23763-4720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4722">'Blazor'</span></span>
- <span data-ttu-id="23763-4723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4723">'Identity'</span></span>
- <span data-ttu-id="23763-4724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4724">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4725">'Razor'</span></span>
- <span data-ttu-id="23763-4726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4728">'Blazor'</span></span>
- <span data-ttu-id="23763-4729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4729">'Identity'</span></span>
- <span data-ttu-id="23763-4730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4730">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4731">'Razor'</span></span>
- <span data-ttu-id="23763-4732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4734">'Blazor'</span></span>
- <span data-ttu-id="23763-4735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4735">'Identity'</span></span>
- <span data-ttu-id="23763-4736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4736">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4737">'Razor'</span></span>
- <span data-ttu-id="23763-4738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4740">'Blazor'</span></span>
- <span data-ttu-id="23763-4741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4741">'Identity'</span></span>
- <span data-ttu-id="23763-4742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4742">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4743">'Razor'</span></span>
- <span data-ttu-id="23763-4744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4746">'Blazor'</span></span>
- <span data-ttu-id="23763-4747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4747">'Identity'</span></span>
- <span data-ttu-id="23763-4748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4748">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4749">'Razor'</span></span>
- <span data-ttu-id="23763-4750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4752">'Blazor'</span></span>
- <span data-ttu-id="23763-4753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4753">'Identity'</span></span>
- <span data-ttu-id="23763-4754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4754">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4755">'Razor'</span></span>
- <span data-ttu-id="23763-4756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4758">'Blazor'</span></span>
- <span data-ttu-id="23763-4759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4759">'Identity'</span></span>
- <span data-ttu-id="23763-4760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4760">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4761">'Razor'</span></span>
- <span data-ttu-id="23763-4762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4762">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4763">---------- | | `[a-z]{2}`   | hello     | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | 123abc456 | はい   | サブ文字列が一致します     | | `[a-z]{2}`   | mz        | はい   | 式が一致します    | | `[a-z]{2}`   | MZ        | はい   | 大文字と小文字は区別されません    | | `^[a-z]{2}$` | hello     | いいえ    | 上の `^` と `$` を参照してください | | `^[a-z]{2}$` | 123abc456 | いいえ    | 上の `^` と `$` を参照してください |</span><span class="sxs-lookup"><span data-stu-id="23763-4763">---------- | | `[a-z]{2}`   | hello     | Yes   | Substring matches     | | `[a-z]{2}`   | 123abc456 | Yes   | Substring matches     | | `[a-z]{2}`   | mz        | Yes   | Matches expression    | | `[a-z]{2}`   | MZ        | Yes   | Not case sensitive    | | `^[a-z]{2}$` | hello     | No    | See `^` and `$` above | | `^[a-z]{2}$` | 123abc456 | No    | See `^` and `$` above |</span></span>

<span data-ttu-id="23763-4764">正規表現構文の詳細については、[.NET Framework 正規表現](/dotnet/standard/base-types/regular-expression-language-quick-reference)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4764">For more information on regular expression syntax, see [.NET Framework Regular Expressions](/dotnet/standard/base-types/regular-expression-language-quick-reference).</span></span>

<span data-ttu-id="23763-4765">既知の入力可能値の集まりにパラメーターを制限するには、正規表現を使用します。</span><span class="sxs-lookup"><span data-stu-id="23763-4765">To constrain a parameter to a known set of possible values, use a regular expression.</span></span> <span data-ttu-id="23763-4766">たとえば、`{action:regex(^(list|get|create)$)}` の場合、`action` ルート値は `list`、`get`、`create` とのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4766">For example, `{action:regex(^(list|get|create)$)}` only matches the `action` route value to `list`, `get`, or `create`.</span></span> <span data-ttu-id="23763-4767">制約ディクショナリに渡された場合、文字列 `^(list|get|create)$` で同じものになります。</span><span class="sxs-lookup"><span data-stu-id="23763-4767">If passed into the constraints dictionary, the string `^(list|get|create)$` is equivalent.</span></span> <span data-ttu-id="23763-4768">(テンプレート内でインラインではなく) 制約ディクショナリに渡された制約が既知の制約に一致しない場合も、正規表現として扱われます。</span><span class="sxs-lookup"><span data-stu-id="23763-4768">Constraints that are passed in the constraints dictionary (not inline within a template) that don't match one of the known constraints are also treated as regular expressions.</span></span>

## <a name="custom-route-constraints"></a><span data-ttu-id="23763-4769">カスタム ルート制約</span><span class="sxs-lookup"><span data-stu-id="23763-4769">Custom Route Constraints</span></span>

<span data-ttu-id="23763-4770">組み込みのルート制約だけでなく、<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスを実装してカスタム ルート制約を作成することができます。</span><span class="sxs-lookup"><span data-stu-id="23763-4770">In addition to the built-in route constraints, custom route constraints can be created by implementing the <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface.</span></span> <span data-ttu-id="23763-4771"><xref:Microsoft.AspNetCore.Routing.IRouteConstraint> インターフェイスには、1 つのメソッド `Match` が含まれています。これは、制約が満たされている場合は `true` を、それ以外の場合は `false` を返します。</span><span class="sxs-lookup"><span data-stu-id="23763-4771">The <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> interface contains a single method, `Match`, which returns `true` if the constraint is satisfied and `false` otherwise.</span></span>

<span data-ttu-id="23763-4772">カスタムの <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> を使うには、アプリのサービス コンテナー内にあるアプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> に、ルート制約の種類を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-4772">To use a custom <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>, the route constraint type must be registered with the app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in the app's service container.</span></span> <span data-ttu-id="23763-4773"><xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、ルート制約キーを、その制約を検証する <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> の実装にマッピングするディクショナリです。</span><span class="sxs-lookup"><span data-stu-id="23763-4773">A <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> is a dictionary that maps route constraint keys to <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementations that validate those constraints.</span></span> <span data-ttu-id="23763-4774">アプリの <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> は、`Startup.ConfigureServices` で、[services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 呼び出しの一部として、または `services.Configure<RouteOptions>` を使って <xref:Microsoft.AspNetCore.Routing.RouteOptions> を直接構成することで、更新できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4774">An app's <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> can be updated in `Startup.ConfigureServices` either as part of a [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) call or by configuring <xref:Microsoft.AspNetCore.Routing.RouteOptions> directly with `services.Configure<RouteOptions>`.</span></span> <span data-ttu-id="23763-4775">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-4775">For example:</span></span>

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

<span data-ttu-id="23763-4776">これで、制約の種類を登録するときに指定した名前を使って、通常の方法でルートに制約を適用できます。</span><span class="sxs-lookup"><span data-stu-id="23763-4776">The constraint can then be applied to routes in the usual manner, using the name specified when registering the constraint type.</span></span> <span data-ttu-id="23763-4777">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="23763-4777">For example:</span></span>

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a><span data-ttu-id="23763-4778">URL 生成参照</span><span class="sxs-lookup"><span data-stu-id="23763-4778">URL generation reference</span></span>

<span data-ttu-id="23763-4779">次の例では、ルートのリンクを生成する方法を確認できます。ルート値のディクショナリと <xref:Microsoft.AspNetCore.Routing.RouteCollection> が指定されています。</span><span class="sxs-lookup"><span data-stu-id="23763-4779">The following example shows how to generate a link to a route given a dictionary of route values and a <xref:Microsoft.AspNetCore.Routing.RouteCollection>.</span></span>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

<span data-ttu-id="23763-4780">上のサンプルの終わりで生成された <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> は `/package/create/123` です。</span><span class="sxs-lookup"><span data-stu-id="23763-4780">The <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> generated at the end of the preceding sample is `/package/create/123`.</span></span> <span data-ttu-id="23763-4781">ディクショナリにより、"Track Package Route" テンプレート `package/{operation}/{id}` のルート値 `operation` と `id` が提供されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4781">The dictionary supplies the `operation` and `id` route values of the "Track Package Route" template, `package/{operation}/{id}`.</span></span> <span data-ttu-id="23763-4782">詳細については、「[ルーティング ミドルウェアの使用](#use-routing-middleware)」セクションのサンプル コードまたは[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23763-4782">For details, see the sample code in the [Use Routing Middleware](#use-routing-middleware) section or the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).</span></span>

<span data-ttu-id="23763-4783"><xref:Microsoft.AspNetCore.Routing.VirtualPathContext> コンストラクターの 2 番目のパラメーターは*アンビエント値*の集合です。</span><span class="sxs-lookup"><span data-stu-id="23763-4783">The second parameter to the <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> constructor is a collection of *ambient values*.</span></span> <span data-ttu-id="23763-4784">アンビエント値は、開発者が要求コンテキスト内で指定する必要がある値の数が制限されるため、使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="23763-4784">Ambient values are convenient to use because they limit the number of values a developer must specify within a request context.</span></span> <span data-ttu-id="23763-4785">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="23763-4785">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="23763-4786">ASP.NET Core MVC アプリの `HomeController` の `About` アクションでは、コントローラー ルート値を指定し、`Index` アクションにリンクする必要はありません。`Home` のアンビエント値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4786">In an ASP.NET Core MVC app's `About` action of the `HomeController`, you don't need to specify the controller route value to link to the `Index` action&mdash;the ambient value of `Home` is used.</span></span>

<span data-ttu-id="23763-4787">パラメーターに一致しないアンビエント値は無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4787">Ambient values that don't match a parameter are ignored.</span></span> <span data-ttu-id="23763-4788">アンビエント値は、明示的に指定された値でアンビエント値がオーバーライドされる場合にも無視されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4788">Ambient values are also ignored when an explicitly provided value overrides the ambient value.</span></span> <span data-ttu-id="23763-4789">URL では左から右に照合されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4789">Matching occurs from left to right in the URL.</span></span>

<span data-ttu-id="23763-4790">明示的に指定されているが、ルートのセグメントと一致しない値は、クエリ文字列に追加されます。</span><span class="sxs-lookup"><span data-stu-id="23763-4790">Values explicitly provided but that don't match a segment of the route are added to the query string.</span></span> <span data-ttu-id="23763-4791">次の表は、ルート テンプレート `{controller}/{action}/{id?}` の使用時の結果をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="23763-4791">The following table shows the result when using the route template `{controller}/{action}/{id?}`.</span></span>

| <span data-ttu-id="23763-4792">アンビエント値</span><span class="sxs-lookup"><span data-stu-id="23763-4792">Ambient Values</span></span>                     | <span data-ttu-id="23763-4793">明示的な値</span><span class="sxs-lookup"><span data-stu-id="23763-4793">Explicit Values</span></span>                        | <span data-ttu-id="23763-4794">結果</span><span class="sxs-lookup"><span data-stu-id="23763-4794">Result</span></span>                  |
| ---
<span data-ttu-id="23763-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4796">'Blazor'</span></span>
- <span data-ttu-id="23763-4797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4797">'Identity'</span></span>
- <span data-ttu-id="23763-4798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4798">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4799">'Razor'</span></span>
- <span data-ttu-id="23763-4800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4802">'Blazor'</span></span>
- <span data-ttu-id="23763-4803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4803">'Identity'</span></span>
- <span data-ttu-id="23763-4804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4804">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4805">'Razor'</span></span>
- <span data-ttu-id="23763-4806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4806">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4807">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4808">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4808">'Blazor'</span></span>
- <span data-ttu-id="23763-4809">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4809">'Identity'</span></span>
- <span data-ttu-id="23763-4810">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4810">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4811">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4811">'Razor'</span></span>
- <span data-ttu-id="23763-4812">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4812">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4813">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4814">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4814">'Blazor'</span></span>
- <span data-ttu-id="23763-4815">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4815">'Identity'</span></span>
- <span data-ttu-id="23763-4816">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4816">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4817">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4817">'Razor'</span></span>
- <span data-ttu-id="23763-4818">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4818">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4820">'Blazor'</span></span>
- <span data-ttu-id="23763-4821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4821">'Identity'</span></span>
- <span data-ttu-id="23763-4822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4822">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4823">'Razor'</span></span>
- <span data-ttu-id="23763-4824">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4826">'Blazor'</span></span>
- <span data-ttu-id="23763-4827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4827">'Identity'</span></span>
- <span data-ttu-id="23763-4828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4828">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4829">'Razor'</span></span>
- <span data-ttu-id="23763-4830">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4832">'Blazor'</span></span>
- <span data-ttu-id="23763-4833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4833">'Identity'</span></span>
- <span data-ttu-id="23763-4834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4834">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4835">'Razor'</span></span>
- <span data-ttu-id="23763-4836">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4838">'Blazor'</span></span>
- <span data-ttu-id="23763-4839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4839">'Identity'</span></span>
- <span data-ttu-id="23763-4840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4840">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4841">'Razor'</span></span>
- <span data-ttu-id="23763-4842">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4844">'Blazor'</span></span>
- <span data-ttu-id="23763-4845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4845">'Identity'</span></span>
- <span data-ttu-id="23763-4846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4846">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4847">'Razor'</span></span>
- <span data-ttu-id="23763-4848">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4850">'Blazor'</span></span>
- <span data-ttu-id="23763-4851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4851">'Identity'</span></span>
- <span data-ttu-id="23763-4852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4852">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4853">'Razor'</span></span>
- <span data-ttu-id="23763-4854">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4856">'Blazor'</span></span>
- <span data-ttu-id="23763-4857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4857">'Identity'</span></span>
- <span data-ttu-id="23763-4858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4858">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4859">'Razor'</span></span>
- <span data-ttu-id="23763-4860">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4862">'Blazor'</span></span>
- <span data-ttu-id="23763-4863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4863">'Identity'</span></span>
- <span data-ttu-id="23763-4864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4864">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4865">'Razor'</span></span>
- <span data-ttu-id="23763-4866">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4868">'Blazor'</span></span>
- <span data-ttu-id="23763-4869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4869">'Identity'</span></span>
- <span data-ttu-id="23763-4870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4870">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4871">'Razor'</span></span>
- <span data-ttu-id="23763-4872">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4874">'Blazor'</span></span>
- <span data-ttu-id="23763-4875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4875">'Identity'</span></span>
- <span data-ttu-id="23763-4876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4876">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4877">'Razor'</span></span>
- <span data-ttu-id="23763-4878">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4880">'Blazor'</span></span>
- <span data-ttu-id="23763-4881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4881">'Identity'</span></span>
- <span data-ttu-id="23763-4882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4882">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4883">'Razor'</span></span>
- <span data-ttu-id="23763-4884">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4884">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4885">----------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4886">'Blazor'</span></span>
- <span data-ttu-id="23763-4887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4887">'Identity'</span></span>
- <span data-ttu-id="23763-4888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4888">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4889">'Razor'</span></span>
- <span data-ttu-id="23763-4890">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4892">'Blazor'</span></span>
- <span data-ttu-id="23763-4893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4893">'Identity'</span></span>
- <span data-ttu-id="23763-4894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4894">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4895">'Razor'</span></span>
- <span data-ttu-id="23763-4896">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4898">'Blazor'</span></span>
- <span data-ttu-id="23763-4899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4899">'Identity'</span></span>
- <span data-ttu-id="23763-4900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4900">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4901">'Razor'</span></span>
- <span data-ttu-id="23763-4902">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4904">'Blazor'</span></span>
- <span data-ttu-id="23763-4905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4905">'Identity'</span></span>
- <span data-ttu-id="23763-4906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4906">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4907">'Razor'</span></span>
- <span data-ttu-id="23763-4908">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4908">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4909">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4910">'Blazor'</span></span>
- <span data-ttu-id="23763-4911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4911">'Identity'</span></span>
- <span data-ttu-id="23763-4912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4912">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4913">'Razor'</span></span>
- <span data-ttu-id="23763-4914">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4916">'Blazor'</span></span>
- <span data-ttu-id="23763-4917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4917">'Identity'</span></span>
- <span data-ttu-id="23763-4918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4918">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4919">'Razor'</span></span>
- <span data-ttu-id="23763-4920">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4922">'Blazor'</span></span>
- <span data-ttu-id="23763-4923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4923">'Identity'</span></span>
- <span data-ttu-id="23763-4924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4924">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4925">'Razor'</span></span>
- <span data-ttu-id="23763-4926">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4928">'Blazor'</span></span>
- <span data-ttu-id="23763-4929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4929">'Identity'</span></span>
- <span data-ttu-id="23763-4930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4930">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4931">'Razor'</span></span>
- <span data-ttu-id="23763-4932">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4934">'Blazor'</span></span>
- <span data-ttu-id="23763-4935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4935">'Identity'</span></span>
- <span data-ttu-id="23763-4936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4936">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4937">'Razor'</span></span>
- <span data-ttu-id="23763-4938">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4940">'Blazor'</span></span>
- <span data-ttu-id="23763-4941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4941">'Identity'</span></span>
- <span data-ttu-id="23763-4942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4942">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4943">'Razor'</span></span>
- <span data-ttu-id="23763-4944">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4946">'Blazor'</span></span>
- <span data-ttu-id="23763-4947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4947">'Identity'</span></span>
- <span data-ttu-id="23763-4948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4948">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4949">'Razor'</span></span>
- <span data-ttu-id="23763-4950">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4952">'Blazor'</span></span>
- <span data-ttu-id="23763-4953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4953">'Identity'</span></span>
- <span data-ttu-id="23763-4954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4954">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4955">'Razor'</span></span>
- <span data-ttu-id="23763-4956">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4958">'Blazor'</span></span>
- <span data-ttu-id="23763-4959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4959">'Identity'</span></span>
- <span data-ttu-id="23763-4960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4960">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4961">'Razor'</span></span>
- <span data-ttu-id="23763-4962">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4964">'Blazor'</span></span>
- <span data-ttu-id="23763-4965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4965">'Identity'</span></span>
- <span data-ttu-id="23763-4966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4966">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4967">'Razor'</span></span>
- <span data-ttu-id="23763-4968">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4970">'Blazor'</span></span>
- <span data-ttu-id="23763-4971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4971">'Identity'</span></span>
- <span data-ttu-id="23763-4972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4972">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4973">'Razor'</span></span>
- <span data-ttu-id="23763-4974">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4976">'Blazor'</span></span>
- <span data-ttu-id="23763-4977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4977">'Identity'</span></span>
- <span data-ttu-id="23763-4978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4978">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4979">'Razor'</span></span>
- <span data-ttu-id="23763-4980">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4980">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4981">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4982">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4982">'Blazor'</span></span>
- <span data-ttu-id="23763-4983">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4983">'Identity'</span></span>
- <span data-ttu-id="23763-4984">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4984">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4985">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4985">'Razor'</span></span>
- <span data-ttu-id="23763-4986">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4986">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4987">------------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4988">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4988">'Blazor'</span></span>
- <span data-ttu-id="23763-4989">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4989">'Identity'</span></span>
- <span data-ttu-id="23763-4990">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4990">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4991">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4991">'Razor'</span></span>
- <span data-ttu-id="23763-4992">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4992">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4993">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-4994">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-4994">'Blazor'</span></span>
- <span data-ttu-id="23763-4995">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-4995">'Identity'</span></span>
- <span data-ttu-id="23763-4996">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-4996">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-4997">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-4997">'Razor'</span></span>
- <span data-ttu-id="23763-4998">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-4998">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-4999">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5000">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5000">'Blazor'</span></span>
- <span data-ttu-id="23763-5001">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5001">'Identity'</span></span>
- <span data-ttu-id="23763-5002">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5002">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5003">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5003">'Razor'</span></span>
- <span data-ttu-id="23763-5004">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5004">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5005">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5006">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5006">'Blazor'</span></span>
- <span data-ttu-id="23763-5007">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5007">'Identity'</span></span>
- <span data-ttu-id="23763-5008">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5008">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5009">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5009">'Razor'</span></span>
- <span data-ttu-id="23763-5010">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5010">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5011">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5012">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5012">'Blazor'</span></span>
- <span data-ttu-id="23763-5013">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5013">'Identity'</span></span>
- <span data-ttu-id="23763-5014">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5014">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5015">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5015">'Razor'</span></span>
- <span data-ttu-id="23763-5016">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5016">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5017">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5018">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5018">'Blazor'</span></span>
- <span data-ttu-id="23763-5019">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5019">'Identity'</span></span>
- <span data-ttu-id="23763-5020">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5020">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5021">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5021">'Razor'</span></span>
- <span data-ttu-id="23763-5022">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5022">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5023">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5024">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5024">'Blazor'</span></span>
- <span data-ttu-id="23763-5025">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5025">'Identity'</span></span>
- <span data-ttu-id="23763-5026">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5026">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5027">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5027">'Razor'</span></span>
- <span data-ttu-id="23763-5028">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5028">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5029">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5030">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5030">'Blazor'</span></span>
- <span data-ttu-id="23763-5031">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5031">'Identity'</span></span>
- <span data-ttu-id="23763-5032">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5032">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5033">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5033">'Razor'</span></span>
- <span data-ttu-id="23763-5034">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5034">'SignalR' uid:</span></span> 

-
<span data-ttu-id="23763-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="23763-5035">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23763-5036">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23763-5036">'Blazor'</span></span>
- <span data-ttu-id="23763-5037">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23763-5037">'Identity'</span></span>
- <span data-ttu-id="23763-5038">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23763-5038">'Let's Encrypt'</span></span>
- <span data-ttu-id="23763-5039">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23763-5039">'Razor'</span></span>
- <span data-ttu-id="23763-5040">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="23763-5040">'SignalR' uid:</span></span> 

<span data-ttu-id="23763-5041">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order"、action = "About" | `/Order/About`          |
| controller = "Home"、color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About"、color = "Red"        | `/Home/About?color=Red` |</span><span class="sxs-lookup"><span data-stu-id="23763-5041">------------ | | controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |</span></span>

<span data-ttu-id="23763-5042">パラメーターに対応しない既定値がルートにあり、その値が明示的に指定される場合、それは既定値に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23763-5042">If a route has a default value that doesn't correspond to a parameter and that value is explicitly provided, it must match the default value:</span></span>

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

<span data-ttu-id="23763-5043">リンク生成では、`controller` と `action` について一致する値が指定されるときにのみ、このルートのリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="23763-5043">Link generation only generates a link for this route when the matching values for `controller` and `action` are provided.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="23763-5044">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="23763-5044">Complex segments</span></span>

<span data-ttu-id="23763-5045">複雑なセグメント (例: `[Route("/x{token}y")]`) は、リテラルを右から左に最短一致の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="23763-5045">Complex segments (for example `[Route("/x{token}y")]`) are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="23763-5046">複雑なセグメントの一致方法に関する詳しい説明については、[こちらのコード](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="23763-5046">See [this code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) for a detailed explanation of how complex segments are matched.</span></span> <span data-ttu-id="23763-5047">この[コード サンプル](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)は ASP.NET Core では使われていませんが、複雑なセグメントに関する優れた説明が提供されています。</span><span class="sxs-lookup"><span data-stu-id="23763-5047">The [code sample](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) is not used by ASP.NET Core, but it provides a good explanation of complex segments.</span></span>

::: moniker-end
