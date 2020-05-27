---
<span data-ttu-id="5a21b-101">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-102">'Blazor'</span></span>
- <span data-ttu-id="5a21b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-103">'Identity'</span></span>
- <span data-ttu-id="5a21b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-105">'Razor'</span></span>
- <span data-ttu-id="5a21b-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="5a21b-107">ASP.NET Core でのコントローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="5a21b-108">作成者: [Ryan Nowak](https://github.com/rynowak)、[Kirk Larkin](https://twitter.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a21b-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a21b-109">ASP.NET Core コントローラーは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して受信要求の url を照合し、[アクション](#action)にマップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="5a21b-110">ルートテンプレート:</span><span class="sxs-lookup"><span data-stu-id="5a21b-110">Routes templates:</span></span>

* <span data-ttu-id="5a21b-111">はスタートアップコードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="5a21b-112">URL パスと[アクション](#action)の照合方法を記述します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="5a21b-113">リンクの Url を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="5a21b-114">生成されたリンクは通常、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="5a21b-115">アクションは、[慣例的にルーティング](#cr)されるか、または[属性ルーティング](#ar)されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="5a21b-116">コントローラーまたは[アクション](#action)にルートを配置すると、ルートが属性ルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="5a21b-117">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="5a21b-118">このドキュメント:</span><span class="sxs-lookup"><span data-stu-id="5a21b-118">This document:</span></span>

* <span data-ttu-id="5a21b-119">MVC とルーティングの間の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="5a21b-120">一般的な MVC アプリでルーティング機能を利用する方法。</span><span class="sxs-lookup"><span data-stu-id="5a21b-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="5a21b-121">次の両方について説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-121">Covers both:</span></span>
    * <span data-ttu-id="5a21b-122">通常、[従来のルーティング](#cr)では、コントローラーとビューが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="5a21b-123">REST Api で使用される*属性ルーティング*。</span><span class="sxs-lookup"><span data-stu-id="5a21b-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="5a21b-124">REST Api のルーティングに主に関心がある場合は、「 [Rest api の属性ルーティング](#ar)」セクションに進んでください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="5a21b-125">詳細については、「[ルーティング](xref:fundamentals/routing)の詳細」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="5a21b-126">ASP.NET Core 3.0 で追加された既定のルーティングシステムを指します (エンドポイントルーティングと呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="5a21b-127">以前のバージョンのルーティングでは、互換性のためにコントローラーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="5a21b-128">手順については、 [2.2-3.0 移行ガイド](xref:migration/22-to-30)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="5a21b-129">レガシルーティングシステムのリファレンス資料については、[このドキュメントの2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="5a21b-130">従来のルートの設定</span><span class="sxs-lookup"><span data-stu-id="5a21b-130">Set up conventional route</span></span>

<span data-ttu-id="5a21b-131">`Startup.Configure`通常、[従来のルーティング](#crd)を使用する場合、には次のようなコードがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="5a21b-132">の呼び出しの内部で <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> は、を使用して <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> 1 つのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="5a21b-133">単一のルートの名前は `default` route です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-133">The single route is named `default` route.</span></span> <span data-ttu-id="5a21b-134">コントローラーとビューを使用するほとんどのアプリでは、ルートと同様のルートテンプレートが使用さ `default` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="5a21b-135">REST Api では、[属性ルーティング](#ar)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="5a21b-136">ルートテンプレート `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="5a21b-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="5a21b-137">次のような URL パスと一致します`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="5a21b-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="5a21b-138">パスをトークン化して、ルートの値を抽出し `{ controller = Products, action = Details, id = 5 }` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="5a21b-139">アプリケーションにという名前のコントローラーがあり、アクションがある場合、ルート値を抽出した結果が一致し `ProductsController` `Details` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="5a21b-140">`/Products/Details/5`モデルは、の値をバインドし `id = 5` て、 `id` パラメーターをに設定 `5` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="5a21b-141">詳細については、「[モデルバインド](xref:mvc/models/model-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="5a21b-142">`{controller=Home}``Home`既定値としてを定義し `controller` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="5a21b-143">`{action=Index}``Index`既定値としてを定義し `action` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="5a21b-144">`?`の文字は `{id?}` 、 `id` 省略可能として定義されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="5a21b-145">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="5a21b-146">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="5a21b-147">URL パスと一致し `/` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="5a21b-148">ルート値を生成 `{ controller = Home, action = Index }` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="5a21b-149">との値によって、 `controller` `action` 既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="5a21b-150">`id`URL パスに対応するセグメントがないため、は値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="5a21b-151">`/`とのアクションが存在する場合にのみ一致 `HomeController` し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="5a21b-152">前のコントローラー定義とルートテンプレートを使用して、 `HomeController.Index` 次の URL パスに対してアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="5a21b-153">URL パスは、 `/` ルートテンプレートの既定の `Home` コントローラーとアクションを使用し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="5a21b-154">URL パスは `/Home` 、ルートテンプレートの既定のアクションを使用し `Index` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="5a21b-155">便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="5a21b-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="5a21b-156">もの</span><span class="sxs-lookup"><span data-stu-id="5a21b-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="5a21b-157">ルーティングは、とミドルウェアを使用して構成され <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="5a21b-158">コントローラーを使用するには:</span><span class="sxs-lookup"><span data-stu-id="5a21b-158">To use controllers:</span></span>
>
> * <span data-ttu-id="5a21b-159"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> `UseEndpoints` [属性ルーティング](#ar)コントローラーをマップするには、内でを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="5a21b-160"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>または <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> を呼び出して、[従来のルーティング](#cr)コントローラーをマップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="5a21b-161">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-161">Conventional routing</span></span>

<span data-ttu-id="5a21b-162">従来のルーティングは、コントローラーとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="5a21b-163">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="5a21b-164">これは、"*規則ルーティング*" の例です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="5a21b-165">これは、URL パスの*規則*を確立するため、*従来のルーティング*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="5a21b-166">最初のパスセグメントは、 `{controller=Home}` コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="5a21b-167">2番目のセグメントは、 `{action=Index}` [アクション](#action)名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="5a21b-168">3番目のセグメント `{id?}` は、オプションとして使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="5a21b-169">の `?` は `{id?}` 省略可能になります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="5a21b-170">`id`は、モデルエンティティにマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="5a21b-171">このルートを使用して `default` 、次の URL パスを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="5a21b-172">`/Products/List`アクションに割り当てら `ProductsController.List` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="5a21b-173">`/Blog/Article/17`はにマップされ、 `BlogController.Article` 通常、モデルは `id` パラメーターを17にバインドします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="5a21b-174">このマッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-174">This mapping:</span></span>

* <span data-ttu-id="5a21b-175">は、コントローラー名と[アクション](#action)名**のみ**に基づいています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="5a21b-176">は、名前空間、ソースファイルの場所、またはメソッドのパラメーターに基づいていません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="5a21b-177">既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを使用しなくてもアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="5a21b-178">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルのアクションを使用するアプリの場合、コントローラー間で url の一貫性を確保します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="5a21b-179">コードを簡略化するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-179">Helps simplify the code.</span></span>
* <span data-ttu-id="5a21b-180">UI の予測可能性を向上させます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="5a21b-181">前のコードのは、 `id` ルートテンプレートによってオプションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="5a21b-182">アクションは、URL の一部として指定された省略可能な ID なしで実行できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="5a21b-183">通常、URL からを省略すると、 `id` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="5a21b-184">`id`は `0` 、モデルバインドによってに設定されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="5a21b-185">データベース照合でエンティティが見つかりません `id == 0` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="5a21b-186">[属性ルーティング](#ar)を使用すると、他のアクションではなく、一部のアクションに必要な ID を作成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="5a21b-187">慣例により、ドキュメントには、 `id` 正しい使用状況で表示される可能性があるなどの省略可能なパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="5a21b-188">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="5a21b-189">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="5a21b-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="5a21b-190">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="5a21b-191">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="5a21b-192">は、多くの web UI アプリに必要な唯一のルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="5a21b-193">大規模な web UI アプリの場合は、必要な場合は、[領域](#areas)を使用する別のルートがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="5a21b-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>および <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="5a21b-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="5a21b-195">呼び出された順序に基づいて、エンドポイントに**注文**値を自動的に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="5a21b-196">ASP.NET Core 3.0 以降でのエンドポイントのルーティング:</span><span class="sxs-lookup"><span data-stu-id="5a21b-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="5a21b-197">ルートの概念がありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="5a21b-198">は、拡張性の実行に対する順序の保証を提供しません。すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="5a21b-199">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="5a21b-200">[属性ルーティング](#ar)については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="5a21b-201">複数の従来のルート</span><span class="sxs-lookup"><span data-stu-id="5a21b-201">Multiple conventional routes</span></span>

<span data-ttu-id="5a21b-202">との呼び出しをさらに追加することで、複数の従来の[ルート](#cr)を内部に追加でき `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="5a21b-203">これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の通常のルートを追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="5a21b-204">前のコードのルートは、 `blog` 専用の従来の**ルート**です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="5a21b-205">これは、次の理由で専用の従来のルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="5a21b-206">従来の[ルーティング](#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="5a21b-207">これは特定の[アクション](#action)専用です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="5a21b-208">`controller`とは `action` ルートテンプレートにパラメーターとして表示されないため、 `"blog/{*article}"` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="5a21b-209">既定値のみを持つことができ `{ controller = "Blog", action = "Article" }` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="5a21b-210">このルートは常にアクションにマップさ `BlogController.Article` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="5a21b-211">`/Blog`、 `/Blog/Article` 、および `/Blog/{any-string}` は、ブログルートに一致する唯一の URL パスです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="5a21b-212">前の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-212">The preceding example:</span></span>

* <span data-ttu-id="5a21b-213">`blog`ルートは最初に追加されるため、ルートより優先順位が高くなり `default` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="5a21b-214">とは、URL の一部としてアーティクル名を持つ一般的な[スラグ](https://developer.mozilla.org/docs/Glossary/Slug)スタイルのルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="5a21b-215">ASP.NET Core 3.0 以降では、ルーティングは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="5a21b-216">*ルート*と呼ばれる概念を定義します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-216">Define a concept called a *route*.</span></span> <span data-ttu-id="5a21b-217">`UseRouting` では、ルートの照合がミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="5a21b-218">ミドルウェアは、 `UseRouting` アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="5a21b-219">やなどの拡張機能の実行順序についての保証を提供 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="5a21b-220">ルーティングのリファレンス資料については、「[ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="5a21b-221">従来のルーティング順序</span><span class="sxs-lookup"><span data-stu-id="5a21b-221">Conventional routing order</span></span>

<span data-ttu-id="5a21b-222">従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="5a21b-223">これは、通常のルートが重複するケースを簡略化することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="5a21b-224">、、およびを使用してルートを追加する <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> と、 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 呼び出された順序に基づいて、そのエンドポイントに自動的に注文値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="5a21b-225">以前に表示されたルートからの一致は、優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="5a21b-226">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="5a21b-227">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="5a21b-228">などのすべてのルートパラメーターを持つ[専用の従来のルート](#dcr)では、ルートの `{*article}` [最長](xref:fundamentals/routing#greedy)一致が実現されます。これは、他のルートと一致するように意図した url と一致することを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="5a21b-229">最短一致の一致を防ぐために、ルートテーブルの中で最長一致のルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="5a21b-230">あいまいなアクションの解決</span><span class="sxs-lookup"><span data-stu-id="5a21b-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="5a21b-231">ルーティングを通じて2つのエンドポイントが一致する場合、ルーティングでは次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="5a21b-232">最適な候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-232">Choose the best candidate.</span></span>
* <span data-ttu-id="5a21b-233">例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-233">Throw an exception.</span></span>

<span data-ttu-id="5a21b-234">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="5a21b-235">前のコントローラーは、次の2つのアクションに一致するアクションを定義します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="5a21b-236">URL パス`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="5a21b-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="5a21b-237">データ `{ controller = Products33, action = Edit, id = 17 }` をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="5a21b-238">これは、MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="5a21b-239">`Edit(int)`製品を編集するためのフォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="5a21b-240">`Edit(int, Product)`ポストされたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="5a21b-241">正しいルートを解決するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-241">To resolve the correct route:</span></span>

* <span data-ttu-id="5a21b-242">`Edit(int, Product)`は、要求が HTTP の場合に選択され `POST` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="5a21b-243">`Edit(int)`[HTTP 動詞](#verb)が他のものである場合は、が選択されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="5a21b-244">`Edit(int)`は、一般に経由で呼び出され `GET` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="5a21b-245">、 <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> 、 `[HttpPost]` は、要求の HTTP メソッドに基づいて選択できるように、ルーティングのために用意されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="5a21b-246">は `HttpPostAttribute` `Edit(int, Product)` よりもより適切に一致し `Edit(int)` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="5a21b-247">のような属性の役割を理解しておくことが重要です `HttpPostAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="5a21b-248">同様の属性は、他の[HTTP 動詞](#verb)に対して定義されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="5a21b-249">[従来のルーティング](#cr)では、表示フォーム、送信フォームワークフローの一部である場合、アクションは同じアクション名を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="5a21b-250">例については、「 [2 つの編集アクションメソッドの確認](xref:tutorials/first-mvc-app/controller-methods-views#get-post)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="5a21b-251">ルーティングで最適な候補を選択できない場合は、 <xref:System.Reflection.AmbiguousMatchException> 一致する複数のエンドポイントを一覧表示するがスローされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="5a21b-252">従来のルート名</span><span class="sxs-lookup"><span data-stu-id="5a21b-252">Conventional route names</span></span>

<span data-ttu-id="5a21b-253">次の例の文字列とは、 `"blog"` `"default"` 通常のルート名です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="5a21b-254">ルート名によって、ルートに論理名が指定されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-254">The route names give the route a logical name.</span></span> <span data-ttu-id="5a21b-255">名前付きルートは、URL の生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="5a21b-256">ルートの順序によって URL の生成が複雑になる可能性がある場合、名前付きルートを使用すると、URL の作成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="5a21b-257">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="5a21b-258">ルート名:</span><span class="sxs-lookup"><span data-stu-id="5a21b-258">Route names:</span></span>

* <span data-ttu-id="5a21b-259">URL の照合や要求の処理には影響しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="5a21b-260">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-260">Are used only for URL generation.</span></span>

<span data-ttu-id="5a21b-261">ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="5a21b-262">**ルート名**と**エンドポイント名**の用語:</span><span class="sxs-lookup"><span data-stu-id="5a21b-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="5a21b-263">交換可能です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-263">Are interchangeable.</span></span>
* <span data-ttu-id="5a21b-264">ドキュメントとコードで使用されるものは、記述されている API によって異なります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="5a21b-265">REST Api の属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="5a21b-266">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が[HTTP 動詞](#verb)によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="5a21b-267">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="5a21b-268">次の `StartUp.Configure` コードは、REST API の一般的なコードであり、次のサンプルで使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="5a21b-269">前のコードで <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> は、 `UseEndpoints` 属性ルーティングコントローラーをマップするために、内でが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="5a21b-270">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-270">In the following example:</span></span>

* <span data-ttu-id="5a21b-271">上記の `Configure` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="5a21b-272">`HomeController`既定の従来のルートと同様の Url のセットと一致 `{controller=Home}/{action=Index}/{id?}` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="5a21b-273">`HomeController.Index`アクションは、URL パス、、 `/` 、またはのいずれかに対して実行され `/Home` `/Home/Index` `/Home/Index/3` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="5a21b-274">この例では、属性ルーティングと[従来のルーティング](#cr)の主なプログラミングの違いについて取り上げます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="5a21b-275">属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="5a21b-276">従来の既定のルートは、ルートをより簡潔に処理します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="5a21b-277">ただし、属性ルーティングでは、各[アクション](#action)に適用されるルートテンプレートを正確に制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="5a21b-278">次のコードの内容は以下のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-278">In the following code:</span></span>

* <span data-ttu-id="5a21b-279">コントローラー名とアクション名は、アクションが一致する役割を果たし**ません**。</span><span class="sxs-lookup"><span data-stu-id="5a21b-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="5a21b-280">前の例と同じ Url に一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="5a21b-281">次のコードでは、とのトークン置換を使用してい `action` `controller` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="5a21b-282">コントローラーには、次のコードが適用され `[Route("[controller]/[action]")]` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="5a21b-283">前のコードでは、 `Index` メソッドテンプレートをルートテンプレートに付加する必要があり `/` `~/` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="5a21b-284">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="5a21b-285">ルートテンプレートの選択の詳細については、[ルートテンプレートの優先順位](xref:fundamentals/routing#rtp)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="5a21b-286">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="5a21b-286">Reserved routing names</span></span>

<span data-ttu-id="5a21b-287">次のキーワードは、コントローラーまたはページを使用する場合の予約ルートパラメーター名です Razor 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="5a21b-288">`page`属性ルーティングでルートパラメーターとしてを使用することは、一般的なエラーです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="5a21b-289">その結果、URL の生成によって一貫性のない動作がわかりにくくなります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="5a21b-290">特別なパラメーター名は、url 生成操作がページまたはコントローラーのどちらを参照するかを判断するために、URL 生成によって使用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="5a21b-291">HTTP 動詞テンプレート</span><span class="sxs-lookup"><span data-stu-id="5a21b-291">HTTP verb templates</span></span>

<span data-ttu-id="5a21b-292">ASP.NET Core には、次の HTTP 動詞テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="5a21b-293">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="5a21b-294">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="5a21b-295">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="5a21b-296">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="5a21b-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="5a21b-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="5a21b-299">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="5a21b-299">Route templates</span></span>

<span data-ttu-id="5a21b-300">ASP.NET Core には、次のルートテンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="5a21b-301">すべての[HTTP 動詞テンプレート](#verb)はルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="5a21b-302">[回送](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="5a21b-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="5a21b-303">Http 動詞属性を使用した属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="5a21b-304">次のコントローラーを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="5a21b-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="5a21b-305">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-305">In the preceding code:</span></span>

* <span data-ttu-id="5a21b-306">各アクションには属性が含まれてい `[HttpGet]` ます。これにより、一致する HTTP GET 要求のみが制限されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="5a21b-307">アクションには `GetProduct` テンプレートが含まれ `"{id}"` ているため、 `id` コントローラーのテンプレートに追加され `"api/[controller]"` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="5a21b-308">メソッドテンプレートは `"api/[controller]/"{id}""` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="5a21b-309">したがって、このアクションでは、の GET 要求のみが、、、などの形式で一致 `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="5a21b-310">`GetIntProduct`アクションには、テンプレートが含まれてい `"int/{id:int}")` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="5a21b-311">`:int`テンプレートの部分では、 `id` 整数に変換できる文字列にルート値を制限します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="5a21b-312">GET 要求 `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="5a21b-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="5a21b-313">このアクションと一致しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="5a21b-314">[404 が見つからないこと](https://developer.mozilla.org/docs/Web/HTTP/Status/404)を示すエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="5a21b-315">`GetInt2Product`アクションはテンプレートに含まれてい `{id}` ますが、 `id` 整数に変換できる値に制限されていません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="5a21b-316">GET 要求 `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="5a21b-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="5a21b-317">このルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-317">Matches this route.</span></span>
  * <span data-ttu-id="5a21b-318">モデルバインドは整数への変換に失敗し `abc` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="5a21b-319">`id`メソッドのパラメーターは integer です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="5a21b-320">モデルバインドが整数に変換できなかったため、 [400 の無効な要求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)が返さ `abc` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="5a21b-321">属性ルーティングでは <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> 、、、などの属性を使用でき <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="5a21b-322">すべての[HTTP 動詞](#verb)属性は、ルートテンプレートを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="5a21b-323">次の例は、同じルートテンプレートに一致する2つのアクションを示しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="5a21b-324">URL パスを使用する場合 `/products3` :</span><span class="sxs-lookup"><span data-stu-id="5a21b-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="5a21b-325">アクションは、 `MyProductsController.ListProducts` [HTTP 動詞](#verb)がの場合に実行され `GET` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="5a21b-326">アクションは、 `MyProductsController.CreateProduct` [HTTP 動詞](#verb)がの場合に実行され `POST` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="5a21b-327">REST API をビルドする場合、アクション `[Route(...)]` はすべての HTTP メソッドを受け入れるため、アクションメソッドでを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="5a21b-328">API がサポートする内容については、より具体的な[HTTP 動詞属性](#verb)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="5a21b-329">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="5a21b-330">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が HTTP 動詞によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="5a21b-331">これは、同じ論理リソースに対する GET や POST などの多くの操作で同じ URL が使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="5a21b-332">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="5a21b-333">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="5a21b-334">次の例で `id` は、URL パスの一部としてが必要です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="5a21b-335">`Products2ApiController.GetProduct(int)`アクション:</span><span class="sxs-lookup"><span data-stu-id="5a21b-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="5a21b-336">次のように URL パスを使用して実行する`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="5a21b-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="5a21b-337">URL パスでは実行さ `/products2` れません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="5a21b-338">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="5a21b-339">詳細については、「[利用属性を使用したサポートされる要求のコンテンツの種類の定義](xref:web-api/index#consumes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="5a21b-340">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="5a21b-341">の詳細につい `[ApiController]` ては、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="5a21b-342">ルート名</span><span class="sxs-lookup"><span data-stu-id="5a21b-342">Route name</span></span>

<span data-ttu-id="5a21b-343">次のコードでは、 の "`Products_List`ルート名" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="5a21b-344">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="5a21b-345">ルート名:</span><span class="sxs-lookup"><span data-stu-id="5a21b-345">Route names:</span></span>

* <span data-ttu-id="5a21b-346">ルーティングの URL 照合動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="5a21b-347">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-347">Are only used for URL generation.</span></span>

<span data-ttu-id="5a21b-348">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="5a21b-349">前のコードと従来の既定のルートを比較します。これにより、 `id` パラメーターが省略可能な () として定義され `{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="5a21b-350">Api を正確に指定する機能には、 `/products` と `/products/5` を別のアクションにディスパッチできるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="5a21b-351">属性ルートの結合</span><span class="sxs-lookup"><span data-stu-id="5a21b-351">Combining attribute routes</span></span>

<span data-ttu-id="5a21b-352">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="5a21b-353">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="5a21b-354">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="5a21b-355">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="5a21b-355">In the preceding example:</span></span>

* <span data-ttu-id="5a21b-356">URL パスを `/products` 一致させることができます`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="5a21b-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="5a21b-357">URL パスを `/products/5` 一致させることができ `ProductsApi.GetProduct(int)` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="5a21b-358">これらのアクションはどちらも `GET` 、属性でマークされているので、HTTP とのみ一致し `[HttpGet]` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="5a21b-359">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="5a21b-360">次の例は、既定のルートに似た URL パスのセットに一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="5a21b-361">次の表では、上記のコードの属性について説明し `[Route]` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="5a21b-362">属性</span><span class="sxs-lookup"><span data-stu-id="5a21b-362">Attribute</span></span>               | <span data-ttu-id="5a21b-363">との組み合わせ`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="5a21b-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="5a21b-364">ルートテンプレートを定義します</span><span class="sxs-lookup"><span data-stu-id="5a21b-364">Defines route template</span></span> |
| ---
<span data-ttu-id="5a21b-365">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-366">'Blazor'</span></span>
- <span data-ttu-id="5a21b-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-367">'Identity'</span></span>
- <span data-ttu-id="5a21b-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-369">'Razor'</span></span>
- <span data-ttu-id="5a21b-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-371">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-372">'Blazor'</span></span>
- <span data-ttu-id="5a21b-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-373">'Identity'</span></span>
- <span data-ttu-id="5a21b-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-375">'Razor'</span></span>
- <span data-ttu-id="5a21b-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-377">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-378">'Blazor'</span></span>
- <span data-ttu-id="5a21b-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-379">'Identity'</span></span>
- <span data-ttu-id="5a21b-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-381">'Razor'</span></span>
- <span data-ttu-id="5a21b-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-383">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-384">'Blazor'</span></span>
- <span data-ttu-id="5a21b-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-385">'Identity'</span></span>
- <span data-ttu-id="5a21b-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-387">'Razor'</span></span>
- <span data-ttu-id="5a21b-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-389">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-390">'Blazor'</span></span>
- <span data-ttu-id="5a21b-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-391">'Identity'</span></span>
- <span data-ttu-id="5a21b-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-393">'Razor'</span></span>
- <span data-ttu-id="5a21b-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-395">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-396">'Blazor'</span></span>
- <span data-ttu-id="5a21b-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-397">'Identity'</span></span>
- <span data-ttu-id="5a21b-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-399">'Razor'</span></span>
- <span data-ttu-id="5a21b-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-400">'SignalR' uid:</span></span> 

<span data-ttu-id="5a21b-401">--------- |---タイトル: 作成者: 説明: ms. author: ms. 日付: no loc:</span><span class="sxs-lookup"><span data-stu-id="5a21b-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-402">'Blazor'</span></span>
- <span data-ttu-id="5a21b-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-403">'Identity'</span></span>
- <span data-ttu-id="5a21b-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-405">'Razor'</span></span>
- <span data-ttu-id="5a21b-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-407">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-408">'Blazor'</span></span>
- <span data-ttu-id="5a21b-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-409">'Identity'</span></span>
- <span data-ttu-id="5a21b-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-411">'Razor'</span></span>
- <span data-ttu-id="5a21b-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-413">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-414">'Blazor'</span></span>
- <span data-ttu-id="5a21b-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-415">'Identity'</span></span>
- <span data-ttu-id="5a21b-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-417">'Razor'</span></span>
- <span data-ttu-id="5a21b-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-419">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-420">'Blazor'</span></span>
- <span data-ttu-id="5a21b-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-421">'Identity'</span></span>
- <span data-ttu-id="5a21b-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-423">'Razor'</span></span>
- <span data-ttu-id="5a21b-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-424">'SignalR' uid:</span></span> 

<span data-ttu-id="5a21b-425">------ |---タイトル: 作成者: 説明: ms. author: ms. 日付: no loc:</span><span class="sxs-lookup"><span data-stu-id="5a21b-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-426">'Blazor'</span></span>
- <span data-ttu-id="5a21b-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-427">'Identity'</span></span>
- <span data-ttu-id="5a21b-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-429">'Razor'</span></span>
- <span data-ttu-id="5a21b-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a21b-431">タイトル: 作成者: 説明: ミリ秒、作成者: ミリ秒。日付: なし:</span><span class="sxs-lookup"><span data-stu-id="5a21b-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a21b-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-432">'Blazor'</span></span>
- <span data-ttu-id="5a21b-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a21b-433">'Identity'</span></span>
- <span data-ttu-id="5a21b-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a21b-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a21b-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a21b-435">'Razor'</span></span>
- <span data-ttu-id="5a21b-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a21b-436">'SignalR' uid:</span></span> 

<span data-ttu-id="5a21b-437">----- || `[Route("")]` |はい |`"Home"` |
|`[Route("Index")]` |はい |`"Home/Index"` |
|`[Route("/")]` | **いいえ** | `""` |
 | `[Route("About")]` |はい |`"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="5a21b-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="5a21b-438">属性のルートの順序</span><span class="sxs-lookup"><span data-stu-id="5a21b-438">Attribute route order</span></span>

<span data-ttu-id="5a21b-439">ルーティングによってツリーが構築され、すべてのエンドポイントが同時に一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="5a21b-440">ルートエントリは、最適な順序で配置されているかのように動作します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="5a21b-441">最も具体的なルートは、より一般的なルートの前に実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="5a21b-442">たとえば、のような属性ルート `blog/search/{topic}` は、のような属性ルートよりも具体的です `blog/{*article}` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="5a21b-443">既定では、 `blog/search/{topic}` ルートの優先度は高くなります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="5a21b-444">開発者は、[従来のルーティング](#cr)を使用して、ルートを目的の順序で配置します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="5a21b-445">属性ルートでは、プロパティを使用して注文を構成でき <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="5a21b-446">フレームワークに用意されているすべての[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、が含まれ `Order` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="5a21b-447">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="5a21b-448">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-448">The default order is `0`.</span></span> <span data-ttu-id="5a21b-449">を使用してルートを設定すると `Order = -1` 、順序が設定されていないルートよりも前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="5a21b-450">を使用したルートの設定は、 `Order = 1` 既定のルートの順序の後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="5a21b-451">によっては**避け**て `Order` ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="5a21b-452">アプリの URL 空間で、明示的な順序の値を正しくルーティングする必要がある場合は、クライアントにも混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="5a21b-453">一般に、属性ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="5a21b-454">URL の生成に使用される既定の順序が機能していない場合は、通常、プロパティを適用するよりも、ルート名をオーバーライドとして使用する方が簡単です `Order` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="5a21b-455">次の2つのコントローラーで、両方がルート一致を定義しているとし `/home` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="5a21b-456">`/home`前のコードでを要求すると、次のような例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="5a21b-457">`Order`ルート属性のいずれかにを追加すると、あいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="5a21b-458">上記のコードでは、によって `/home` エンドポイントが実行され `HomeController.Index` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="5a21b-459">にアクセスするには `MyDemoController.MyIndex` 、を要求 `/home/MyIndex` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="5a21b-460">**注**: </span><span class="sxs-lookup"><span data-stu-id="5a21b-460">**Note**:</span></span>

* <span data-ttu-id="5a21b-461">上記のコードは、ルーティング設計の一例または不十分です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="5a21b-462">これは、プロパティを示すために使用されていま `Order` した。</span><span class="sxs-lookup"><span data-stu-id="5a21b-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="5a21b-463">`Order`プロパティはあいまいさを解決するだけで、そのテンプレートは一致しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="5a21b-464">テンプレートを削除することをお勧めし `[Route("Home")]` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="5a21b-465">ページとルートの順序については[ Razor 、「ルートとアプリの規則](xref:razor-pages/razor-pages-conventions#route-order)」を参照してください Razor 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="5a21b-466">場合によっては、あいまいなルートで HTTP 500 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="5a21b-467">[ログ](xref:fundamentals/logging/index)を使用して、の原因となったエンドポイントを確認し `AmbiguousMatchException` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="5a21b-468">ルートテンプレートでのトークンの置換 [controller]、[action]、[area]</span><span class="sxs-lookup"><span data-stu-id="5a21b-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="5a21b-469">便宜上、属性ルートは、次のいずれかのトークンを囲むことによって、予約ルートパラメーターのトークン置換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="5a21b-470">角かっこ:`[]`</span><span class="sxs-lookup"><span data-stu-id="5a21b-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="5a21b-471">中かっこ:`{}`</span><span class="sxs-lookup"><span data-stu-id="5a21b-471">Curly braces: `{}`</span></span>

<span data-ttu-id="5a21b-472">トークン `[action]` 、 `[area]` 、および `[controller]` は、ルートが定義されているアクションのアクション名、領域名、およびコントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="5a21b-473">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="5a21b-474">一致`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="5a21b-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="5a21b-475">一致`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="5a21b-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="5a21b-476">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="5a21b-477">前の例では、次のコードと同じ動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="5a21b-478">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="5a21b-479">これは、トークンの置換と組み合わせた強力な機能です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="5a21b-480">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="5a21b-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="5a21b-482">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="5a21b-483"> では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="5a21b-484">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="5a21b-485">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="5a21b-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="5a21b-486">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="5a21b-487">パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="5a21b-488">たとえば、カスタム `SlugifyParameterTransformer` パラメータートランスフォーマーは、 `SubscriptionManagement` ルート値を次のように変更し `subscription-management` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="5a21b-489"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="5a21b-490">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="5a21b-491">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="5a21b-492">前の `ListAll` メソッドはと一致 `/subscription-management/list-all` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="5a21b-493">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="5a21b-494">スラグの定義については、[スラグの MDN web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="5a21b-495">複数の属性ルート</span><span class="sxs-lookup"><span data-stu-id="5a21b-495">Multiple attribute routes</span></span>

<span data-ttu-id="5a21b-496">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="5a21b-497">これの最も一般的な使用方法は、次の例で示すように、"既定の規則ルート" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="5a21b-498">コントローラーに複数のルート属性を配置することは、それぞれがアクションメソッドの各ルート属性と結合することを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="5a21b-499">すべての[HTTP 動詞](#verb)ルート制約は、を実装 `IActionConstraint` します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="5a21b-500">を実装する複数のルート属性 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> が1つのアクションに配置されている場合:</span><span class="sxs-lookup"><span data-stu-id="5a21b-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="5a21b-501">各アクション制約は、コントローラーに適用されたルートテンプレートと結合されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="5a21b-502">アクションで複数のルートを使用すると便利で強力な場合があります。アプリの URL 空間の基本を明確に定義しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="5a21b-503">既存のクライアントをサポートするために必要な場合に**のみ**、アクションで複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="5a21b-504">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="5a21b-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="5a21b-505">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="5a21b-506">前のコードでは、は `[HttpPost("product/{id:int}")]` ルート制約を適用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="5a21b-507">`ProductsController.ShowProduct`アクションは、のような URL パスによってのみ照合され `/product/3` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="5a21b-508">ルートテンプレートの部分は、 `{id:int}` そのセグメントを整数のみに制限します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="5a21b-509">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="5a21b-510">IRouteTemplateProvider を使用したカスタムルート属性</span><span class="sxs-lookup"><span data-stu-id="5a21b-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="5a21b-511">すべての[ルート属性](#rt)はを実装 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="5a21b-512">ASP.NET Core ランタイム:</span><span class="sxs-lookup"><span data-stu-id="5a21b-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="5a21b-513">アプリの起動時に、コントローラークラスとアクションメソッドの属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="5a21b-514">は、を実装する属性を使用して `IRouteTemplateProvider` 、ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="5a21b-515">を実装して `IRouteTemplateProvider` 、カスタムルート属性を定義します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="5a21b-516">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="5a21b-517">前の `Get` メソッドはを返し `Order = 2, Template = api/MyTestApi` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="5a21b-518">アプリケーションモデルを使用して属性ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="5a21b-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="5a21b-519">アプリケーションモデル:</span><span class="sxs-lookup"><span data-stu-id="5a21b-519">The application model:</span></span>

* <span data-ttu-id="5a21b-520">は、起動時に作成されるオブジェクトモデルです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="5a21b-521">アプリでアクションをルーティングおよび実行するために ASP.NET Core によって使用されるすべてのメタデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="5a21b-522">アプリケーションモデルには、ルート属性から収集されたすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="5a21b-523">ルート属性からのデータは、実装によって提供され `IRouteTemplateProvider` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="5a21b-524">規定</span><span class="sxs-lookup"><span data-stu-id="5a21b-524">Conventions:</span></span>

* <span data-ttu-id="5a21b-525">アプリケーションモデルを変更して、ルーティングの動作をカスタマイズするために、を記述できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="5a21b-526">アプリの起動時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-526">Are read at app startup.</span></span>

<span data-ttu-id="5a21b-527">このセクションでは、アプリケーションモデルを使用してルーティングをカスタマイズする基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="5a21b-528">次のコードでは、ルートがプロジェクトのフォルダー構造とほぼ同じになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="5a21b-529">次のコードは、 `namespace` 属性がルーティングされているコントローラーに規則が適用されないようにします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="5a21b-530">たとえば、次のコントローラーではを使用しません `NamespaceRoutingConvention` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="5a21b-531">`NamespaceRoutingConvention.Apply` メソッド:</span><span class="sxs-lookup"><span data-stu-id="5a21b-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="5a21b-532">コントローラーが属性ルーティングされている場合は、何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="5a21b-533">に基づいてコントローラーテンプレートを設定し `namespace` `namespace` ます。ベースは削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="5a21b-534">は、 `NamespaceRoutingConvention` 次の方法で適用でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="5a21b-535">たとえば、次のコントローラーを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="5a21b-536">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-536">In the preceding code:</span></span>

* <span data-ttu-id="5a21b-537">ベース `namespace` が `My.Application` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="5a21b-538">前のコントローラーの完全な名前は `My.Application.Admin.Controllers.UsersController` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="5a21b-539">によって、 `NamespaceRoutingConvention` コントローラーテンプレートがに設定され `Admin/Controllers/Users/[action]/{id?` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="5a21b-540">は、 `NamespaceRoutingConvention` コントローラーの属性として適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="5a21b-541">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="5a21b-542">ASP.NET Core アプリでは、従来のルーティングと属性ルーティングの使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="5a21b-543">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="5a21b-544">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="5a21b-545">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="5a21b-546">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="5a21b-547">コントローラー**のルート属性では、コントローラー**属性の**すべて**のアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="5a21b-548">属性ルーティングと従来のルーティングでは、同じルーティングエンジンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="5a21b-549">URL 生成とアンビエント値</span><span class="sxs-lookup"><span data-stu-id="5a21b-549">URL Generation and ambient values</span></span>

<span data-ttu-id="5a21b-550">アプリでは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="5a21b-551">Url を生成することで、Url をハードコーディングすることがなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="5a21b-552">このセクションでは、MVC によって提供される URL 生成機能について説明し、URL の生成のしくみの基本についてのみ説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="5a21b-553">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="5a21b-554"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>インターフェイスは、MVC と URL 生成のルーティングの間のインフラストラクチャの基になる要素です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="5a21b-555">のインスタンス `IUrlHelper` は、 `Url` コントローラー、ビュー、およびビューコンポーネントのプロパティを介して使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="5a21b-556">次の例では、 `IUrlHelper` プロパティを通じてインターフェイスを使用して、 `Controller.Url` 別のアクションへの URL を生成しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="5a21b-557">アプリで既定のルートを使用している場合、変数の値 `url` は URL パス文字列に `/UrlGeneration/Destination` なります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="5a21b-558">この URL パスは、次の組み合わせによってルーティングによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="5a21b-559">現在の要求からのルート値 (**アンビエント値**と呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="5a21b-560">に渡され、 `Url.Action` それらの値をルートテンプレートに置き換える値。</span><span class="sxs-lookup"><span data-stu-id="5a21b-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="5a21b-561">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="5a21b-562">値を持たないルートパラメーターには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="5a21b-563">既定値がある場合は、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="5a21b-564">省略可能な場合はスキップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-564">Be skipped if it's optional.</span></span> <span data-ttu-id="5a21b-565">たとえば、ルートテンプレートのを使用し `id` `{controller}/{action}/{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="5a21b-566">必要なルートパラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="5a21b-567">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="5a21b-568">前の例では、従来のルーティングが想定されてい `Url.Action` ます。 [conventional routing](#cr)</span><span class="sxs-lookup"><span data-stu-id="5a21b-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="5a21b-569">URL の生成は、[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="5a21b-570">従来のルーティングの場合:</span><span class="sxs-lookup"><span data-stu-id="5a21b-570">With conventional routing:</span></span>

* <span data-ttu-id="5a21b-571">ルート値は、テンプレートを展開するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="5a21b-572">とのルート値は、 `controller` `action` 通常、そのテンプレートに表示されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="5a21b-573">これは、ルーティングによって一致した Url が規則に準拠しているために機能します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="5a21b-574">次の例では、属性ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="5a21b-575">前のコードのアクションによってが `Source` 生成さ `custom/url/to/destination` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="5a21b-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>は、の代わりに ASP.NET Core 3.0 で追加されました `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="5a21b-577">`LinkGenerator`は、同様に柔軟な機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="5a21b-578">の各メソッドに `IUrlHelper` は、に対応するメソッドのファミリ `LinkGenerator` もあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="5a21b-579">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-579">Generating URLs by action name</span></span>

<span data-ttu-id="5a21b-580">[Url. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)、 [Linkgenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)、およびすべての関連するすべてのオーバーロードは、コントローラー名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="5a21b-581">を使用する場合 `Url.Action` 、との現在のルート値 `controller` は、 `action` ランタイムによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="5a21b-582">との値 `controller` `action` は、[アンビエント値](#ambient)と値の両方に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="5a21b-583">メソッドは、 `Url.Action` 常にとの現在の値を使用 `action` し、現在の `controller` アクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="5a21b-584">ルーティングでは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="5a21b-585">アンビエント値のようなルートを考えてみましょう `{a}/{b}/{c}/{d}` `{ a = Alice, b = Bob, c = Carol, d = David }` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="5a21b-586">ルーティングには、URL を生成するための十分な情報が含まれており、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="5a21b-587">すべてのルートパラメーターに値があるため、ルーティングに十分な情報が得られます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="5a21b-588">値が追加された場合は、 `{ d = Donovan }` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="5a21b-589">この値 `{ d = David }` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="5a21b-590">生成された URL パスが `Alice/Bob/Carol/Donovan` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="5a21b-591">**警告**: URL パスは階層化されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="5a21b-592">前の例で、値が追加されている場合は、 `{ c = Cheryl }` 次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="5a21b-593">両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="5a21b-594">の値はなく `d` 、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="5a21b-595">URL を生成するには、との必要な値を `c` `d` 指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="5a21b-596">既定のルートでは、この問題が発生する可能性があり `{controller}/{action}/{id?}` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="5a21b-597">この問題は、常にとの値が明示的に指定されているため、実際にはめったにあり `Url.Action` `controller` `action` ません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="5a21b-598">[Url](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)のいくつかのオーバーロードでは、ルート値オブジェクトを使用し `controller` て、および以外のルートパラメーターの値を指定します。 `action`</span><span class="sxs-lookup"><span data-stu-id="5a21b-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="5a21b-599">ルート値オブジェクトは、と共によく使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="5a21b-600">たとえば、「 `Url.Action("Buy", "Products", new { id = 17 })` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="5a21b-601">ルート値オブジェクト:</span><span class="sxs-lookup"><span data-stu-id="5a21b-601">The route values object:</span></span>

* <span data-ttu-id="5a21b-602">慣例により、通常は匿名型のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="5a21b-603">には、 `IDictionary<>` または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)を指定できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="5a21b-604">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="5a21b-605">前のコードによってが生成さ `/Products/Buy/17?color=red` れます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="5a21b-606">次のコードでは、絶対 URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="5a21b-607">絶対 URL を作成するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="5a21b-608">を受け入れるオーバーロード `protocol` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="5a21b-609">たとえば、上記のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-609">For example, the preceding code.</span></span>
* <span data-ttu-id="5a21b-610">[Linkgenerator. GetUriByAction。](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)既定で絶対 uri を生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="5a21b-611">ルートによる Url の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-611">Generate URLs by route</span></span>

<span data-ttu-id="5a21b-612">前のコードでは、コントローラーとアクション名を渡すことによって URL を生成することを示していました。</span><span class="sxs-lookup"><span data-stu-id="5a21b-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="5a21b-613">`IUrlHelper`には、メソッドの[Url routeurl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも用意されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="5a21b-614">これらのメソッドは、 [Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、との現在の値 `action` `controller` をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="5a21b-615">の最も一般的な使用方法は `Url.RouteUrl` 次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="5a21b-616">URL を生成するルート名を指定します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="5a21b-617">通常、コントローラーまたはアクション名は指定しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="5a21b-618">次の Razor ファイルでは、への HTML リンクが生成され `Destination_Route` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="5a21b-619">HTML およびでの Url の生成Razor</span><span class="sxs-lookup"><span data-stu-id="5a21b-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="5a21b-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>メソッドを使用して、 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> 要素と要素をそれぞれ生成するように[Html.actionlink と html](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*)を[提供します](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)。 `<form>` `<a>`</span><span class="sxs-lookup"><span data-stu-id="5a21b-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="5a21b-621">これらのメソッドは、Url を生成するために[url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用し、同様の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="5a21b-622">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="5a21b-623">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="5a21b-624">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="5a21b-625">詳細については、「[フォームのタグヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="5a21b-626">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="5a21b-627">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-627">URL generation in Action Results</span></span>

<span data-ttu-id="5a21b-628">前の例では、コントローラーでを使用して示しました `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="5a21b-629">コントローラーで最も一般的に使用されるのは、アクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="5a21b-630">基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="5a21b-631">一般的な使用方法の1つは、ユーザー入力を受け入れた後にリダイレクトすることです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="5a21b-632">アクションの結果ファクトリメソッド (やなど) は、 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> のメソッドと同様のパターンに従い `IUrlHelper` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="5a21b-633">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="5a21b-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="5a21b-634">[従来のルーティング](#cr)では、[専用の従来のルート](#dcr)と呼ばれる特別な種類のルート定義を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="5a21b-635">次の例では、という名前のルート `blog` が専用の従来のルートになっています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="5a21b-636">前のルート定義を使用すると、は `Url.Action("Index", "Home")` ルートを使用して URL パスを生成し `/` `default` ますが、その理由は何でしょうか。</span><span class="sxs-lookup"><span data-stu-id="5a21b-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="5a21b-637">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="5a21b-638">[専用の従来のルート](#dcr)では、ルートパラメーターが指定されていない既定値の特殊な動作に依存しています。これにより、ルートが URL 生成に[最長一致](xref:fundamentals/routing#greedy)するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="5a21b-639">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="5a21b-640">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="5a21b-641">値が一致しないため、を使用して URL を生成すること `blog` はでき `{ controller = Home, action = Index }` ません `{ controller = Blog, action = Article }` 。</span><span class="sxs-lookup"><span data-stu-id="5a21b-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="5a21b-642">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="5a21b-643">Areas</span><span class="sxs-lookup"><span data-stu-id="5a21b-643">Areas</span></span>

<span data-ttu-id="5a21b-644">[区分](xref:mvc/controllers/areas)は、関連する機能を別のグループとしてグループにまとめるために使用される MVC 機能です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="5a21b-645">コントローラーアクションのルーティング名前空間。</span><span class="sxs-lookup"><span data-stu-id="5a21b-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="5a21b-646">ビューのフォルダー構造。</span><span class="sxs-lookup"><span data-stu-id="5a21b-646">Folder structure for views.</span></span>

<span data-ttu-id="5a21b-647">区分を使用すると、異なる領域がある限り、同じ名前の複数のコントローラーをアプリに割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="5a21b-648">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="5a21b-649">ここでは、ルーティングと領域の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="5a21b-650">領域をビューで使用する方法の詳細については、「[区分](xref:mvc/controllers/areas)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="5a21b-651">次の例では、既定の従来のルートと、 `area` という名前ののルートを使用するように MVC を構成し `area` `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="5a21b-652">上記のコードで <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> は、が呼び出され、が作成され `"blog_route"` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="5a21b-653">2番目のパラメーターは、 `"Blog"` 領域名です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="5a21b-654">のような URL パスを照合すると、ルート `/Manage/Users/AddUser` によっ `"blog_route"` てルート値が生成され `{ area = Blog, controller = Users, action = AddUser }` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="5a21b-655">`area`ルート値は、の既定値によって生成され `area` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="5a21b-656">によって作成されるルート `MapAreaControllerRoute` は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="5a21b-657">`MapAreaControllerRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="5a21b-658">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="5a21b-659">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="5a21b-660">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="5a21b-661">前の例を使用すると、ルート値は `{ area = Blog, controller = Users, action = AddUser }` 次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="5a21b-662">[[区分]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、1つの領域の一部としてコントローラーを表します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="5a21b-663">このコントローラーは、この `Blog` 領域にあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="5a21b-664">属性を持たないコントローラー `[Area]` は、どの領域のメンバーでも**not**なく、 `area` ルーティングによってルート値が提供されるときには一致しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="5a21b-665">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="5a21b-666">各コントローラーの名前空間は、完全を期すためにここに示されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="5a21b-667">前のコントローラーで同じ名前空間が使用されている場合は、コンパイラエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="5a21b-668">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="5a21b-669">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="5a21b-670">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="5a21b-671">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="5a21b-672">領域内でアクションを実行する場合、のルート値 `area` は、URL の生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="5a21b-673">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="5a21b-674">次のコードでは、への URL が生成され `/Zebra/Users/AddUser` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="5a21b-675">アクションの定義</span><span class="sxs-lookup"><span data-stu-id="5a21b-675">Action definition</span></span>

<span data-ttu-id="5a21b-676">[非 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つパブリックメソッドは、アクションです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5a21b-677">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="5a21b-677">Sample code</span></span>

 * <span data-ttu-id="5a21b-678">[Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="5a21b-679">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5a21b-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a21b-680">ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="5a21b-681">ルートは、スタートアップ コードまたは属性で定義されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="5a21b-682">ルートでは、URL パスとアクションの照合方法が記述されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="5a21b-683">また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="5a21b-684">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="5a21b-685">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="5a21b-686">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="5a21b-687">このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="5a21b-688">高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="5a21b-689">ルーティング ミドルウェアの設定</span><span class="sxs-lookup"><span data-stu-id="5a21b-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="5a21b-690">*Configure* メソッドには、次のようなコードが含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="5a21b-691">`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="5a21b-692">ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="5a21b-693">ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="5a21b-694">MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="5a21b-695">この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="5a21b-696">詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="5a21b-697">`default` ルートの使用:</span><span class="sxs-lookup"><span data-stu-id="5a21b-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="5a21b-698">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="5a21b-698">The route template:</span></span>

* <span data-ttu-id="5a21b-699">`{controller=Home}` は、`Home` を既定の `controller` として定義します</span><span class="sxs-lookup"><span data-stu-id="5a21b-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="5a21b-700">`{action=Index}` は、`Index` を既定の `action` として定義します</span><span class="sxs-lookup"><span data-stu-id="5a21b-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="5a21b-701">`{id?}` は、`id` を省略可能として定義します</span><span class="sxs-lookup"><span data-stu-id="5a21b-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="5a21b-702">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="5a21b-703">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="5a21b-704">`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="5a21b-705">`controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="5a21b-706">MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="5a21b-707">このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="5a21b-708">便利なメソッド `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="5a21b-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="5a21b-709">これは、次の代わりに使うことができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="5a21b-710">`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="5a21b-711">MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="5a21b-712">MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="5a21b-713">`UseMvc` の内部のコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="5a21b-714">`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="5a21b-715">オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="5a21b-716">`UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="5a21b-717">`UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="5a21b-718">「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="5a21b-719">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-719">Conventional routing</span></span>

<span data-ttu-id="5a21b-720">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="5a21b-721">前のコードは、従来のルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="5a21b-722">このスタイルは、URL パスの*規則*を確立するため、従来のルーティングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="5a21b-723">最初のパスセグメントは、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="5a21b-724">2つ目は、アクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-724">The second maps to the action name.</span></span>
* <span data-ttu-id="5a21b-725">3番目のセグメントは、省略可能なに使用され `id` ます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="5a21b-726">`id`モデルエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="5a21b-727">この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="5a21b-728">このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="5a21b-729">既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="5a21b-730">CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="5a21b-731">`id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="5a21b-732">通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="5a21b-733">属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="5a21b-734">慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="5a21b-735">複数のルート</span><span class="sxs-lookup"><span data-stu-id="5a21b-735">Multiple routes</span></span>

<span data-ttu-id="5a21b-736">`MapRoute` の呼び出しをさらに追加することで、`UseMvc` の内部に複数のルートを追加できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="5a21b-737">これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="5a21b-738">`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="5a21b-739">`controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="5a21b-740">ルート コレクション内のルートには順序があり、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="5a21b-741">そのため、この例では、`blog` ルートが `default` ルートより先に試されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-742">*専用の従来のルート*では、多くの場合、のような**キャッチオール**ルートパラメーターを使用して、 `{*article}` URL パスの残りの部分をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="5a21b-743">このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="5a21b-744">この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="5a21b-745">フォールバック</span><span class="sxs-lookup"><span data-stu-id="5a21b-745">Fallback</span></span>

<span data-ttu-id="5a21b-746">要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="5a21b-747">ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="5a21b-748">これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="5a21b-749">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="5a21b-749">Disambiguating actions</span></span>

<span data-ttu-id="5a21b-750">2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="5a21b-751">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="5a21b-752">このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="5a21b-753">これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="5a21b-754">これを MVC で可能にするには、要求が HTTP `POST` の場合は `Edit(int, Product)` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="5a21b-755">`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `POST` の場合にのみそのアクションの選択を許可する `IActionConstraint` の実装です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="5a21b-756">`IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="5a21b-757">カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="5a21b-758">規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="5a21b-759">「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="5a21b-760">複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="5a21b-761">ルート名</span><span class="sxs-lookup"><span data-stu-id="5a21b-761">Route names</span></span>

<span data-ttu-id="5a21b-762">次の例の文字列 `"blog"` と `"default"` は、ルート名です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="5a21b-763">ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="5a21b-764">これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="5a21b-765">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="5a21b-766">ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="5a21b-767">MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="5a21b-768">属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-768">Attribute routing</span></span>

<span data-ttu-id="5a21b-769">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="5a21b-770">次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="5a21b-771">`HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="5a21b-772">`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-773">この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="5a21b-774">属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="5a21b-775">ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="5a21b-776">属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を**持ちません**。</span><span class="sxs-lookup"><span data-stu-id="5a21b-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="5a21b-777">この例では、前の例と同じ URL を照合します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-777">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="5a21b-778">上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="5a21b-779">実際、これらのルート パラメーターは属性ルートでは許可されていません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="5a21b-780">ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="5a21b-781">Http[Verb] 属性を使用する属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="5a21b-782">属性ルーティングでは、`HttpPostAttribute` などの `Http[Verb]` 属性も使うことができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="5a21b-783">これらの属性はすべて、ルート テンプレートを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="5a21b-784">次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-784">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="5a21b-785">`/products` のような URL パスの場合、HTTP 動詞が `GET` のときは `ProductsApi.ListProducts` アクションが実行され、HTTP 動詞が `POST` のときは `ProductsApi.CreateProduct` が実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="5a21b-786">属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="5a21b-787">ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="5a21b-788">REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="5a21b-789">より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="5a21b-790">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="5a21b-791">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="5a21b-792">次の例では、`id` は URL パスの一部として必須です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="5a21b-793">`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="5a21b-794">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="5a21b-795">ルート名</span><span class="sxs-lookup"><span data-stu-id="5a21b-795">Route Name</span></span>

<span data-ttu-id="5a21b-796">次のコードでは、`Products_List` の "*ルート名*" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="5a21b-797">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="5a21b-798">ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="5a21b-799">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-800">これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="5a21b-801">API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="5a21b-802">ルートの結合</span><span class="sxs-lookup"><span data-stu-id="5a21b-802">Combining routes</span></span>

<span data-ttu-id="5a21b-803">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="5a21b-804">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="5a21b-805">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="5a21b-806">次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="5a21b-807">どちらのアクションも、`HttpGetAttribute` でマークされているため、HTTP `GET` だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="5a21b-808">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="5a21b-809">次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-809">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="5a21b-810">属性ルートの順序の指定</span><span class="sxs-lookup"><span data-stu-id="5a21b-810">Ordering attribute routes</span></span>

<span data-ttu-id="5a21b-811">定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="5a21b-812">これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="5a21b-813">たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="5a21b-814">論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="5a21b-815">規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="5a21b-816">属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="5a21b-817">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="5a21b-818">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-818">The default order is `0`.</span></span> <span data-ttu-id="5a21b-819">`Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="5a21b-820">`Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="5a21b-821">`Order` には依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="5a21b-822">URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="5a21b-823">一般に、属性ルーティングは URL 照合で正しいルートを選びます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="5a21b-824">URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="5a21b-825"> Pages ルーティングと MVC コントローラー ルーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="5a21b-826">「ページのルート Razor [ Razor とアプリの規則: ルートの順序](xref:razor-pages/razor-pages-conventions#route-order)」で、ルートの順序に関する情報を参照できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="5a21b-827">ルート テンプレートでのトークンの置換 ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="5a21b-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="5a21b-828">便宜上、属性ルートは、トークンを角かっこ (,) で囲むことによって*トークンの置換*をサポートして `[` `]` います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="5a21b-829">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="5a21b-830">次の例では、アクションはコメントで説明されているように URL パスと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="5a21b-831">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="5a21b-832">上の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="5a21b-833">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="5a21b-834">これをトークンの置換と組み合わせると特に強力です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-834">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="5a21b-835">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="5a21b-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="5a21b-837">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="5a21b-838">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="5a21b-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="5a21b-839">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="5a21b-840">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="5a21b-841">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="5a21b-842">`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="5a21b-843">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="5a21b-844">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="5a21b-845">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="5a21b-846">複数のルート</span><span class="sxs-lookup"><span data-stu-id="5a21b-846">Multiple Routes</span></span>

<span data-ttu-id="5a21b-847">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="5a21b-848">これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="5a21b-849">コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="5a21b-850">アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="5a21b-851">アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="5a21b-852">アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="5a21b-853">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="5a21b-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="5a21b-854">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="5a21b-855">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="5a21b-856">`IRouteTemplateProvider` を使用するカスタム ルート属性</span><span class="sxs-lookup"><span data-stu-id="5a21b-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="5a21b-857">フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="5a21b-858">アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="5a21b-859">`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="5a21b-860">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="5a21b-861">上の例の属性は、`[MyApiController]` が適用されると、自動的に `Template` を `"api/[controller]"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="5a21b-862">アプリケーション モデルを使用した属性ルートのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="5a21b-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="5a21b-863">"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="5a21b-864">"*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="5a21b-865">起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="5a21b-866">このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="5a21b-867">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="5a21b-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="5a21b-868">MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="5a21b-869">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="5a21b-870">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="5a21b-871">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="5a21b-872">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="5a21b-873">コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-874">2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="5a21b-875">規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="5a21b-876">属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="5a21b-877">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="5a21b-877">Complex segments</span></span>

<span data-ttu-id="5a21b-878">複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="5a21b-879">説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="5a21b-880">詳しくは、[こちらの懸案事項](https://github.com/dotnet/AspNetCore.Docs/issues/8197)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="5a21b-881">URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-881">URL Generation</span></span>

<span data-ttu-id="5a21b-882">MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="5a21b-883">URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="5a21b-884">このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="5a21b-885">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="5a21b-886">`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="5a21b-887">使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="5a21b-888">この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="5a21b-889">アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="5a21b-890">この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="5a21b-891">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="5a21b-892">値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="5a21b-893">必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="5a21b-894">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="5a21b-895">上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="5a21b-896">規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="5a21b-897">属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="5a21b-898">この例では、属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="5a21b-899">MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="5a21b-900">上の例では、`custom/url/to/destination` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="5a21b-901">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-901">Generating URLs by action name</span></span>

<span data-ttu-id="5a21b-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="5a21b-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="5a21b-903">`Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-904">`Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"\*アンビエント値" \* **と "** *値*" 両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="5a21b-905">`Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="5a21b-906">ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="5a21b-907">`{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="5a21b-908">値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="5a21b-909">URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-909">URL paths are hierarchical.</span></span> <span data-ttu-id="5a21b-910">上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="5a21b-911">この場合、`d` には値がなくなり、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="5a21b-912">`c` および `d` の目的の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="5a21b-913">既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="5a21b-914">`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="5a21b-915">これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="5a21b-916">慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="5a21b-917">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="5a21b-918">絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="5a21b-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="5a21b-919">ルートによる URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-919">Generating URLs by route</span></span>

<span data-ttu-id="5a21b-920">上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。</span><span class="sxs-lookup"><span data-stu-id="5a21b-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="5a21b-921">`IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="5a21b-922">これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="5a21b-923">最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="5a21b-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="5a21b-924">HTML での URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-924">Generating URLs in HTML</span></span>

<span data-ttu-id="5a21b-925">`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="5a21b-926">これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="5a21b-927">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="5a21b-928">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="5a21b-929">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="5a21b-930">詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="5a21b-931">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="5a21b-932">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="5a21b-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="5a21b-933">上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="5a21b-934">基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="5a21b-935">一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-935">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="5a21b-936">アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="5a21b-937">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="5a21b-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="5a21b-938">規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="5a21b-939">次の例の `blog` という名前のルートが専用規則ルートです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="5a21b-940">これらのルート定義を使うと、`Url.Action("Index", "Home")` は `default` ルートで URL パス `/` を生成します。なぜでしょうか。</span><span class="sxs-lookup"><span data-stu-id="5a21b-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="5a21b-941">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="5a21b-942">専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="5a21b-943">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="5a21b-944">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="5a21b-945">値 `{ controller = Home, action = Index }` は `{ controller = Blog, action = Article }` と一致しないため、`blog` を使った URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="5a21b-946">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="5a21b-947">Areas</span><span class="sxs-lookup"><span data-stu-id="5a21b-947">Areas</span></span>

<span data-ttu-id="5a21b-948">[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="5a21b-949">区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。</span><span class="sxs-lookup"><span data-stu-id="5a21b-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="5a21b-950">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="5a21b-951">このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="5a21b-952">次の例では、既定の規則ルートと、`Blog` という名前の区分に対する "*区分ルート*" を使うように、MVC を構成しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="5a21b-953">`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="5a21b-954">`area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="5a21b-955">`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="5a21b-956">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="5a21b-957">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="5a21b-958">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="5a21b-959">上の例を使うと、ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="5a21b-960">`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="5a21b-961">`[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致**しません**。</span><span class="sxs-lookup"><span data-stu-id="5a21b-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="5a21b-962">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="5a21b-963">ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="5a21b-964">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="5a21b-965">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="5a21b-966">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-967">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="5a21b-968">区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="5a21b-969">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="5a21b-970">IActionConstraint について</span><span class="sxs-lookup"><span data-stu-id="5a21b-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="5a21b-971">このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="5a21b-972">一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5a21b-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="5a21b-973">インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="5a21b-974">`[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="5a21b-975">既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す**両方**のアクションと一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="5a21b-976">`IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="5a21b-977">`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="5a21b-978">`Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="5a21b-979">したがって、`POST` は `Edit(...)` のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="5a21b-980">一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="5a21b-981">したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="5a21b-982">概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。</span><span class="sxs-lookup"><span data-stu-id="5a21b-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="5a21b-983">属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="5a21b-984">IActionConstraint の実装</span><span class="sxs-lookup"><span data-stu-id="5a21b-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="5a21b-985">`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。</span><span class="sxs-lookup"><span data-stu-id="5a21b-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="5a21b-986">MVC は、属性として適用された `IActionConstraint` を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="5a21b-987">アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="5a21b-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="5a21b-988">次の例では、制約は、ルートデータから*国コード*に基づいてアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="5a21b-989">[GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="5a21b-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="5a21b-990">ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="5a21b-991">この例では、`country` ルート値が一致すると、`Accept` メソッドは `true` を返してアクションが一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="5a21b-992">この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。</span><span class="sxs-lookup"><span data-stu-id="5a21b-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="5a21b-993">サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。</span><span class="sxs-lookup"><span data-stu-id="5a21b-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="5a21b-994">`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。</span><span class="sxs-lookup"><span data-stu-id="5a21b-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="5a21b-995">アクションの制約は、`Order` に基づいてグループで実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="5a21b-996">たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。</span><span class="sxs-lookup"><span data-stu-id="5a21b-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="5a21b-997">目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="5a21b-998">`Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="5a21b-999">小さい値が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a21b-999">Lower numbers run first.</span></span>

::: moniker-end
