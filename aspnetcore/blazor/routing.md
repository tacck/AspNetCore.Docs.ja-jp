---
<span data-ttu-id="af820-101">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-101">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-102">'Blazor'</span></span>
- <span data-ttu-id="af820-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-103">'Identity'</span></span>
- <span data-ttu-id="af820-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-105">'Razor'</span></span>
- <span data-ttu-id="af820-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="af820-107">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="af820-107">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="af820-108">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="af820-108">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="af820-109">Blazor アプリで、要求をルーティングする方法と、`NavLink` コンポーネントを使用してナビゲーション リンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="af820-109">Learn how to route requests and how to use the `NavLink` component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="af820-110">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="af820-110">ASP.NET Core endpoint routing integration</span></span>

Blazor<span data-ttu-id="af820-111"> サーバーは [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="af820-111"> Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="af820-112">ASP.NET Core アプリは、`Startup.Configure` で `MapBlazorHub` を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。</span><span class="sxs-lookup"><span data-stu-id="af820-112">An ASP.NET Core app is configured to accept incoming connections for interactive components with `MapBlazorHub` in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="af820-113">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor サーバー アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="af820-113">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="af820-114">通常、*ホスト* ページは、 *_Host.cshtml* という名前になります。</span><span class="sxs-lookup"><span data-stu-id="af820-114">By convention, the *host* page is usually named *_Host.cshtml*.</span></span> <span data-ttu-id="af820-115">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="af820-115">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="af820-116">フォールバック ルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="af820-116">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="af820-117">これにより、Blazor サーバー アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="af820-117">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

## <a name="route-templates"></a><span data-ttu-id="af820-118">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="af820-118">Route templates</span></span>

<span data-ttu-id="af820-119">`Router` コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="af820-119">The `Router` component enables routing to each component with a specified route.</span></span> <span data-ttu-id="af820-120">`Router` コンポーネントは *App.razor* ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="af820-120">The `Router` component appears in the *App.razor* file:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="af820-121">`@page` ディレクティブを含む *razor* ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="af820-121">When a *.razor* file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="af820-122">実行時に、`RouteView` コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="af820-122">At runtime, the `RouteView` component:</span></span>

* <span data-ttu-id="af820-123">`Router` から、必要なパラメーターと共に `RouteData` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af820-123">Receives the `RouteData` from the `Router` along with any desired parameters.</span></span>
* <span data-ttu-id="af820-124">指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="af820-124">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="af820-125">必要に応じて、レイアウト クラスで `DefaultLayout` パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。</span><span class="sxs-lookup"><span data-stu-id="af820-125">You can optionally specify a `DefaultLayout` parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="af820-126">既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。</span><span class="sxs-lookup"><span data-stu-id="af820-126">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="af820-127">*MainLayout.razor* は、テンプレート プロジェクトの *Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="af820-127">*MainLayout.razor* is in the template project's *Shared* folder.</span></span> <span data-ttu-id="af820-128">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af820-128">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="af820-129">コンポーネントには、複数のルート テンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="af820-129">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="af820-130">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="af820-130">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="af820-131">URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その *wwwroot/index.html* ファイル (Blazor WebAssembly) または *Pages/_Host.cshtml* ファイル (Blazor サーバー) に `<base>` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="af820-131">For URLs to resolve correctly, the app must include a `<base>` tag in its *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="af820-132">詳細については、「<xref:host-and-deploy/blazor/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af820-132">For more information, see <xref:host-and-deploy/blazor/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="af820-133">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="af820-133">Provide custom content when content isn't found</span></span>

<span data-ttu-id="af820-134">`Router` コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="af820-134">The `Router` component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="af820-135">*App.razor* ファイルで、`Router` コンポーネントの `NotFound` テンプレート パラメーターにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="af820-135">In the *App.razor* file, set custom content in the `NotFound` template parameter of the `Router` component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="af820-136">`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="af820-136">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="af820-137">`NotFound` コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af820-137">To apply a default layout to `NotFound` content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="af820-138">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="af820-138">Route to components from multiple assemblies</span></span>

<span data-ttu-id="af820-139">`AdditionalAssemblies` パラメーターを使用して、`Router` コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="af820-139">Use the `AdditionalAssemblies` parameter to specify additional assemblies for the `Router` component to consider when searching for routable components.</span></span> <span data-ttu-id="af820-140">指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="af820-140">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="af820-141">次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="af820-141">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="af820-142">次の `AdditionalAssemblies` の例では、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="af820-142">The following `AdditionalAssemblies` example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="af820-143">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="af820-143">Route parameters</span></span>

<span data-ttu-id="af820-144">ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="af820-144">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="af820-145">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="af820-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="af820-146">前の例では、2 つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="af820-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="af820-147">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="af820-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="af820-148">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="af820-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="af820-149">ルート制約</span><span class="sxs-lookup"><span data-stu-id="af820-149">Route constraints</span></span>

<span data-ttu-id="af820-150">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="af820-150">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="af820-151">次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="af820-151">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="af820-152">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="af820-152">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="af820-153">`Id` セグメントは整数 (`int`) である。</span><span class="sxs-lookup"><span data-stu-id="af820-153">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="af820-154">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="af820-154">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="af820-155">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="af820-155">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="af820-156">制約</span><span class="sxs-lookup"><span data-stu-id="af820-156">Constraint</span></span> | <span data-ttu-id="af820-157">例</span><span class="sxs-lookup"><span data-stu-id="af820-157">Example</span></span>           | <span data-ttu-id="af820-158">一致の例</span><span class="sxs-lookup"><span data-stu-id="af820-158">Example Matches</span></span>                                                                  | <span data-ttu-id="af820-159">インバリアント</span><span class="sxs-lookup"><span data-stu-id="af820-159">Invariant</span></span><br><span data-ttu-id="af820-160">カルチャ</span><span class="sxs-lookup"><span data-stu-id="af820-160">culture</span></span><br><span data-ttu-id="af820-161">一致</span><span class="sxs-lookup"><span data-stu-id="af820-161">matching</span></span> |
| ---
<span data-ttu-id="af820-162">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-162">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-163">'Blazor'</span></span>
- <span data-ttu-id="af820-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-164">'Identity'</span></span>
- <span data-ttu-id="af820-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-166">'Razor'</span></span>
- <span data-ttu-id="af820-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-168">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-168">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-169">'Blazor'</span></span>
- <span data-ttu-id="af820-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-170">'Identity'</span></span>
- <span data-ttu-id="af820-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-172">'Razor'</span></span>
- <span data-ttu-id="af820-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-174">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-174">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-175">'Blazor'</span></span>
- <span data-ttu-id="af820-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-176">'Identity'</span></span>
- <span data-ttu-id="af820-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-178">'Razor'</span></span>
- <span data-ttu-id="af820-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-179">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-180">----- | --- title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-180">----- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-181">'Blazor'</span></span>
- <span data-ttu-id="af820-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-182">'Identity'</span></span>
- <span data-ttu-id="af820-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-184">'Razor'</span></span>
- <span data-ttu-id="af820-185">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-186">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-186">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-187">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-187">'Blazor'</span></span>
- <span data-ttu-id="af820-188">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-188">'Identity'</span></span>
- <span data-ttu-id="af820-189">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-189">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-190">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-190">'Razor'</span></span>
- <span data-ttu-id="af820-191">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-191">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-192">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-192">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-193">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-193">'Blazor'</span></span>
- <span data-ttu-id="af820-194">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-194">'Identity'</span></span>
- <span data-ttu-id="af820-195">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-195">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-196">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-196">'Razor'</span></span>
- <span data-ttu-id="af820-197">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-197">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-198">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-198">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-199">'Blazor'</span></span>
- <span data-ttu-id="af820-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-200">'Identity'</span></span>
- <span data-ttu-id="af820-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-202">'Razor'</span></span>
- <span data-ttu-id="af820-203">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-204">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-204">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-205">'Blazor'</span></span>
- <span data-ttu-id="af820-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-206">'Identity'</span></span>
- <span data-ttu-id="af820-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-208">'Razor'</span></span>
- <span data-ttu-id="af820-209">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-210">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-210">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-211">'Blazor'</span></span>
- <span data-ttu-id="af820-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-212">'Identity'</span></span>
- <span data-ttu-id="af820-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-214">'Razor'</span></span>
- <span data-ttu-id="af820-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-215">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-216">--------- | --- title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-216">--------- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-217">'Blazor'</span></span>
- <span data-ttu-id="af820-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-218">'Identity'</span></span>
- <span data-ttu-id="af820-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-220">'Razor'</span></span>
- <span data-ttu-id="af820-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-222">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-222">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-223">'Blazor'</span></span>
- <span data-ttu-id="af820-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-224">'Identity'</span></span>
- <span data-ttu-id="af820-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-226">'Razor'</span></span>
- <span data-ttu-id="af820-227">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-228">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-228">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-229">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-229">'Blazor'</span></span>
- <span data-ttu-id="af820-230">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-230">'Identity'</span></span>
- <span data-ttu-id="af820-231">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-231">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-232">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-232">'Razor'</span></span>
- <span data-ttu-id="af820-233">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-233">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-234">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-234">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-235">'Blazor'</span></span>
- <span data-ttu-id="af820-236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-236">'Identity'</span></span>
- <span data-ttu-id="af820-237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-237">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-238">'Razor'</span></span>
- <span data-ttu-id="af820-239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-240">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-240">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-241">'Blazor'</span></span>
- <span data-ttu-id="af820-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-242">'Identity'</span></span>
- <span data-ttu-id="af820-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-244">'Razor'</span></span>
- <span data-ttu-id="af820-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-246">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-246">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-247">'Blazor'</span></span>
- <span data-ttu-id="af820-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-248">'Identity'</span></span>
- <span data-ttu-id="af820-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-250">'Razor'</span></span>
- <span data-ttu-id="af820-251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-252">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-252">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-253">'Blazor'</span></span>
- <span data-ttu-id="af820-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-254">'Identity'</span></span>
- <span data-ttu-id="af820-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-256">'Razor'</span></span>
- <span data-ttu-id="af820-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-258">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-258">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-259">'Blazor'</span></span>
- <span data-ttu-id="af820-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-260">'Identity'</span></span>
- <span data-ttu-id="af820-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-262">'Razor'</span></span>
- <span data-ttu-id="af820-263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-264">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-264">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-265">'Blazor'</span></span>
- <span data-ttu-id="af820-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-266">'Identity'</span></span>
- <span data-ttu-id="af820-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-268">'Razor'</span></span>
- <span data-ttu-id="af820-269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-270">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-270">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-271">'Blazor'</span></span>
- <span data-ttu-id="af820-272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-272">'Identity'</span></span>
- <span data-ttu-id="af820-273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-273">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-274">'Razor'</span></span>
- <span data-ttu-id="af820-275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-276">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-276">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-277">'Blazor'</span></span>
- <span data-ttu-id="af820-278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-278">'Identity'</span></span>
- <span data-ttu-id="af820-279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-279">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-280">'Razor'</span></span>
- <span data-ttu-id="af820-281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-282">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-282">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-283">'Blazor'</span></span>
- <span data-ttu-id="af820-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-284">'Identity'</span></span>
- <span data-ttu-id="af820-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-286">'Razor'</span></span>
- <span data-ttu-id="af820-287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-288">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-288">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-289">'Blazor'</span></span>
- <span data-ttu-id="af820-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-290">'Identity'</span></span>
- <span data-ttu-id="af820-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-292">'Razor'</span></span>
- <span data-ttu-id="af820-293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-294">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-294">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-295">'Blazor'</span></span>
- <span data-ttu-id="af820-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-296">'Identity'</span></span>
- <span data-ttu-id="af820-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-298">'Razor'</span></span>
- <span data-ttu-id="af820-299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-300">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-300">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-301">'Blazor'</span></span>
- <span data-ttu-id="af820-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-302">'Identity'</span></span>
- <span data-ttu-id="af820-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-304">'Razor'</span></span>
- <span data-ttu-id="af820-305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-306">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-306">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-307">'Blazor'</span></span>
- <span data-ttu-id="af820-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-308">'Identity'</span></span>
- <span data-ttu-id="af820-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-310">'Razor'</span></span>
- <span data-ttu-id="af820-311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-312">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-312">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-313">'Blazor'</span></span>
- <span data-ttu-id="af820-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-314">'Identity'</span></span>
- <span data-ttu-id="af820-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-316">'Razor'</span></span>
- <span data-ttu-id="af820-317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-318">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-318">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-319">'Blazor'</span></span>
- <span data-ttu-id="af820-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-320">'Identity'</span></span>
- <span data-ttu-id="af820-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-322">'Razor'</span></span>
- <span data-ttu-id="af820-323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-324">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-324">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-325">'Blazor'</span></span>
- <span data-ttu-id="af820-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-326">'Identity'</span></span>
- <span data-ttu-id="af820-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-328">'Razor'</span></span>
- <span data-ttu-id="af820-329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-330">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-330">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-331">'Blazor'</span></span>
- <span data-ttu-id="af820-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-332">'Identity'</span></span>
- <span data-ttu-id="af820-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-334">'Razor'</span></span>
- <span data-ttu-id="af820-335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-336">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-336">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-337">'Blazor'</span></span>
- <span data-ttu-id="af820-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-338">'Identity'</span></span>
- <span data-ttu-id="af820-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-340">'Razor'</span></span>
- <span data-ttu-id="af820-341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-342">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-342">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-343">'Blazor'</span></span>
- <span data-ttu-id="af820-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-344">'Identity'</span></span>
- <span data-ttu-id="af820-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-346">'Razor'</span></span>
- <span data-ttu-id="af820-347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-348">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-348">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-349">'Blazor'</span></span>
- <span data-ttu-id="af820-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-350">'Identity'</span></span>
- <span data-ttu-id="af820-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-352">'Razor'</span></span>
- <span data-ttu-id="af820-353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-354">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-354">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-355">'Blazor'</span></span>
- <span data-ttu-id="af820-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-356">'Identity'</span></span>
- <span data-ttu-id="af820-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-358">'Razor'</span></span>
- <span data-ttu-id="af820-359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-360">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-360">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-361">'Blazor'</span></span>
- <span data-ttu-id="af820-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-362">'Identity'</span></span>
- <span data-ttu-id="af820-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-364">'Razor'</span></span>
- <span data-ttu-id="af820-365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-366">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-366">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-367">'Blazor'</span></span>
- <span data-ttu-id="af820-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-368">'Identity'</span></span>
- <span data-ttu-id="af820-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-370">'Razor'</span></span>
- <span data-ttu-id="af820-371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-372">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-372">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-373">'Blazor'</span></span>
- <span data-ttu-id="af820-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-374">'Identity'</span></span>
- <span data-ttu-id="af820-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-376">'Razor'</span></span>
- <span data-ttu-id="af820-377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-378">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-378">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-379">'Blazor'</span></span>
- <span data-ttu-id="af820-380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-380">'Identity'</span></span>
- <span data-ttu-id="af820-381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-381">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-382">'Razor'</span></span>
- <span data-ttu-id="af820-383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-384">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-384">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-385">'Blazor'</span></span>
- <span data-ttu-id="af820-386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-386">'Identity'</span></span>
- <span data-ttu-id="af820-387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-387">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-388">'Razor'</span></span>
- <span data-ttu-id="af820-389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-390">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-390">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-391">'Blazor'</span></span>
- <span data-ttu-id="af820-392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-392">'Identity'</span></span>
- <span data-ttu-id="af820-393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-393">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-394">'Razor'</span></span>
- <span data-ttu-id="af820-395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-396">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-396">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-397">'Blazor'</span></span>
- <span data-ttu-id="af820-398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-398">'Identity'</span></span>
- <span data-ttu-id="af820-399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-399">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-400">'Razor'</span></span>
- <span data-ttu-id="af820-401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-402">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-402">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-403">'Blazor'</span></span>
- <span data-ttu-id="af820-404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-404">'Identity'</span></span>
- <span data-ttu-id="af820-405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-405">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-406">'Razor'</span></span>
- <span data-ttu-id="af820-407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-408">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-408">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-409">'Blazor'</span></span>
- <span data-ttu-id="af820-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-410">'Identity'</span></span>
- <span data-ttu-id="af820-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-412">'Razor'</span></span>
- <span data-ttu-id="af820-413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-414">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-414">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-415">'Blazor'</span></span>
- <span data-ttu-id="af820-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-416">'Identity'</span></span>
- <span data-ttu-id="af820-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-418">'Razor'</span></span>
- <span data-ttu-id="af820-419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-420">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-420">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-421">'Blazor'</span></span>
- <span data-ttu-id="af820-422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-422">'Identity'</span></span>
- <span data-ttu-id="af820-423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-423">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-424">'Razor'</span></span>
- <span data-ttu-id="af820-425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-426">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-426">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-427">'Blazor'</span></span>
- <span data-ttu-id="af820-428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-428">'Identity'</span></span>
- <span data-ttu-id="af820-429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-429">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-430">'Razor'</span></span>
- <span data-ttu-id="af820-431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-432">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-432">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-433">'Blazor'</span></span>
- <span data-ttu-id="af820-434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-434">'Identity'</span></span>
- <span data-ttu-id="af820-435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-435">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-436">'Razor'</span></span>
- <span data-ttu-id="af820-437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-438">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-438">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-439">'Blazor'</span></span>
- <span data-ttu-id="af820-440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-440">'Identity'</span></span>
- <span data-ttu-id="af820-441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-441">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-442">'Razor'</span></span>
- <span data-ttu-id="af820-443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-443">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-444">---------------------------------------- | :--- title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-444">---------------------------------------- | :--- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-445">'Blazor'</span></span>
- <span data-ttu-id="af820-446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-446">'Identity'</span></span>
- <span data-ttu-id="af820-447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-447">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-448">'Razor'</span></span>
- <span data-ttu-id="af820-449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-450">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-450">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-451">'Blazor'</span></span>
- <span data-ttu-id="af820-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-452">'Identity'</span></span>
- <span data-ttu-id="af820-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-454">'Razor'</span></span>
- <span data-ttu-id="af820-455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-456">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-456">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-457">'Blazor'</span></span>
- <span data-ttu-id="af820-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-458">'Identity'</span></span>
- <span data-ttu-id="af820-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-460">'Razor'</span></span>
- <span data-ttu-id="af820-461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-462">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-462">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-463">'Blazor'</span></span>
- <span data-ttu-id="af820-464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-464">'Identity'</span></span>
- <span data-ttu-id="af820-465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-465">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-466">'Razor'</span></span>
- <span data-ttu-id="af820-467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-467">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-468">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-468">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-469">'Blazor'</span></span>
- <span data-ttu-id="af820-470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-470">'Identity'</span></span>
- <span data-ttu-id="af820-471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-471">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-472">'Razor'</span></span>
- <span data-ttu-id="af820-473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-474">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-474">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-475">'Blazor'</span></span>
- <span data-ttu-id="af820-476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-476">'Identity'</span></span>
- <span data-ttu-id="af820-477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-477">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-478">'Razor'</span></span>
- <span data-ttu-id="af820-479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-480">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-480">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-481">'Blazor'</span></span>
- <span data-ttu-id="af820-482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-482">'Identity'</span></span>
- <span data-ttu-id="af820-483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-483">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-484">'Razor'</span></span>
- <span data-ttu-id="af820-485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-486">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-486">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-487">'Blazor'</span></span>
- <span data-ttu-id="af820-488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-488">'Identity'</span></span>
- <span data-ttu-id="af820-489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-489">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-490">'Razor'</span></span>
- <span data-ttu-id="af820-491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-492">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-492">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-493">'Blazor'</span></span>
- <span data-ttu-id="af820-494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-494">'Identity'</span></span>
- <span data-ttu-id="af820-495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-495">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-496">'Razor'</span></span>
- <span data-ttu-id="af820-497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-498">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-498">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-499">'Blazor'</span></span>
- <span data-ttu-id="af820-500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-500">'Identity'</span></span>
- <span data-ttu-id="af820-501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-501">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-502">'Razor'</span></span>
- <span data-ttu-id="af820-503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-504">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-504">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-505">'Blazor'</span></span>
- <span data-ttu-id="af820-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-506">'Identity'</span></span>
- <span data-ttu-id="af820-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-508">'Razor'</span></span>
- <span data-ttu-id="af820-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-510">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-510">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-511">'Blazor'</span></span>
- <span data-ttu-id="af820-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-512">'Identity'</span></span>
- <span data-ttu-id="af820-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-514">'Razor'</span></span>
- <span data-ttu-id="af820-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-516">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-516">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-517">'Blazor'</span></span>
- <span data-ttu-id="af820-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-518">'Identity'</span></span>
- <span data-ttu-id="af820-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-520">'Razor'</span></span>
- <span data-ttu-id="af820-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-521">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | いいえ                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | はい                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | はい                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | はい                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | はい                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | いいえ                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | はい                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | はい                              |</span><span class="sxs-lookup"><span data-stu-id="af820-522">---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | No                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Yes                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Yes                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Yes                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | No                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Yes                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Yes                              |</span></span>

> [!WARNING]
> <span data-ttu-id="af820-523">URL の妥当性を検証し、CLR 型 (`int` や `DateTime` など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="af820-523">Route constraints that verify the URL and are converted to a CLR type (such as `int` or `DateTime`) always use the invariant culture.</span></span> <span data-ttu-id="af820-524">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="af820-524">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="af820-525">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="af820-525">Routing with URLs that contain dots</span></span>

<span data-ttu-id="af820-526">Blazor サーバー アプリでは、 *_Host.cshtml* の既定のルートは `/` (`@page "/"`) です。</span><span class="sxs-lookup"><span data-stu-id="af820-526">In Blazor Server apps, the default route in *_Host.cshtml* is `/` (`@page "/"`).</span></span> <span data-ttu-id="af820-527">ドット (`.`) を含む要求 URL は、既定のルートによって照合されません。URL がファイルを要求しているように見えるためです。</span><span class="sxs-lookup"><span data-stu-id="af820-527">A request URL that contains a dot (`.`) isn't matched by the default route because the URL appears to request a file.</span></span> <span data-ttu-id="af820-528">Blazor アプリにより、存在しない静的ファイルに対して "*404 見つかりません*" 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="af820-528">A Blazor app returns a *404 - Not Found* response for a static file that doesn't exist.</span></span> <span data-ttu-id="af820-529">ドットを含むルートを使用するには、次のルート テンプレートを使用して *_Host.cshtml* を構成します。</span><span class="sxs-lookup"><span data-stu-id="af820-529">To use routes that contain a dot, configure *_Host.cshtml* with the following route template:</span></span>

```cshtml
@page "/{**path}"
```

<span data-ttu-id="af820-530">`"/{**path}"` テンプレートには次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="af820-530">The `"/{**path}"` template includes:</span></span>

* <span data-ttu-id="af820-531">二重アスタリスクの*キャッチオール*構文 (`**`)。スラッシュ (`/`) をエンコードせずに複数のフォルダー境界をまたがるパスをキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="af820-531">Double-asterisk *catch-all* syntax (`**`) to capture the path across multiple folder boundaries without encoding forward slashes (`/`).</span></span>
* <span data-ttu-id="af820-532">`path` ルート パラメーター名。</span><span class="sxs-lookup"><span data-stu-id="af820-532">`path` route parameter name.</span></span>

> [!NOTE]
> <span data-ttu-id="af820-533">"*キャッチオール*" パラメーター構文 (`*`/`**`) は、Razor コンポーネント ( *.razor*) ではサポートされて**いません**。</span><span class="sxs-lookup"><span data-stu-id="af820-533">*Catch-all* parameter syntax (`*`/`**`) is **not** supported in Razor components (*.razor*).</span></span>

<span data-ttu-id="af820-534">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af820-534">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="navlink-component"></a><span data-ttu-id="af820-535">NavLink コンポーネント</span><span class="sxs-lookup"><span data-stu-id="af820-535">NavLink component</span></span>

<span data-ttu-id="af820-536">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに `NavLink` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="af820-536">Use a `NavLink` component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="af820-537">`NavLink` コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="af820-537">A `NavLink` component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="af820-538">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="af820-538">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span>

<span data-ttu-id="af820-539">次の `NavMenu` コンポーネントでは、`NavLink` コンポーネントの使用方法を示す[ブートストラップ](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="af820-539">The following `NavMenu` component creates a [Bootstrap](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use `NavLink` components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="af820-540">`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの `NavLinkMatch` オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="af820-540">There are two `NavLinkMatch` options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="af820-541">`NavLinkMatch.All` &ndash; `NavLink` は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="af820-541">`NavLinkMatch.All` &ndash; The `NavLink` is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="af820-542">`NavLinkMatch.Prefix` (*既定*) &ndash; `NavLink` は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="af820-542">`NavLinkMatch.Prefix` (*default*) &ndash; The `NavLink` is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="af820-543">前の例では、ホーム `NavLink` `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af820-543">In the preceding example, the Home `NavLink` `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="af820-544">2 番目の `NavLink` は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="af820-544">The second `NavLink` receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="af820-545">追加の `NavLink` コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="af820-545">Additional `NavLink` component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="af820-546">次の例では、`NavLink` コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="af820-546">In the following example, the `NavLink` component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="af820-547">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="af820-547">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="af820-548">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="af820-548">URI and navigation state helpers</span></span>

<span data-ttu-id="af820-549">C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。</span><span class="sxs-lookup"><span data-stu-id="af820-549">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="af820-550">`NavigationManager` には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="af820-550">`NavigationManager` provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="af820-551">メンバー</span><span class="sxs-lookup"><span data-stu-id="af820-551">Member</span></span> | <span data-ttu-id="af820-552">説明</span><span class="sxs-lookup"><span data-stu-id="af820-552">Description</span></span> |
| ---
<span data-ttu-id="af820-553">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-553">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-554">'Blazor'</span></span>
- <span data-ttu-id="af820-555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-555">'Identity'</span></span>
- <span data-ttu-id="af820-556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-556">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-557">'Razor'</span></span>
- <span data-ttu-id="af820-558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-558">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-559">--- | --- title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-559">--- | --- title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-560">'Blazor'</span></span>
- <span data-ttu-id="af820-561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-561">'Identity'</span></span>
- <span data-ttu-id="af820-562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-562">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-563">'Razor'</span></span>
- <span data-ttu-id="af820-564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-565">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-565">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-566">'Blazor'</span></span>
- <span data-ttu-id="af820-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-567">'Identity'</span></span>
- <span data-ttu-id="af820-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-569">'Razor'</span></span>
- <span data-ttu-id="af820-570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af820-571">title:'ASP.NET Core Blazor のルーティング' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af820-571">title: 'ASP.NET Core Blazor routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="af820-572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af820-572">'Blazor'</span></span>
- <span data-ttu-id="af820-573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af820-573">'Identity'</span></span>
- <span data-ttu-id="af820-574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af820-574">'Let's Encrypt'</span></span>
- <span data-ttu-id="af820-575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af820-575">'Razor'</span></span>
- <span data-ttu-id="af820-576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="af820-576">'SignalR' uid:</span></span> 

<span data-ttu-id="af820-577">------ | | Uri | 現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="af820-577">------ | | Uri | Gets the current absolute URI.</span></span> <span data-ttu-id="af820-578">| | BaseUri | 絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="af820-578">| | BaseUri | Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="af820-579">通常、`BaseUri` は *wwwroot/index.html* (Blazor WebAssembly)、または *Pages/_Host.cshtml* (Blazor サーバー) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="af820-579">Typically, `BaseUri` corresponds to the `href` attribute on the document's `<base>` element in *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="af820-580">| | NavigateTo | 指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="af820-580">| | NavigateTo | Navigates to the specified URI.</span></span> <span data-ttu-id="af820-581">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="af820-581">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="af820-582">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="af820-582">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="af820-583">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="af820-583">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> <span data-ttu-id="af820-584">| | LocationChanged | ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="af820-584">| | LocationChanged | An event that fires when the navigation location has changed.</span></span> <span data-ttu-id="af820-585">| | ToAbsoluteUri | 相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="af820-585">| | ToAbsoluteUri | Converts a relative URI into an absolute URI.</span></span> <span data-ttu-id="af820-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | ベース URI (たとえば、`GetBaseUri` によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="af820-586">| | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Given a base URI (for example, a URI previously returned by `GetBaseUri`), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="af820-587">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="af820-587">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="af820-588">次のコンポーネントは、場所の変更イベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="af820-588">The following component handles a location changed event.</span></span> <span data-ttu-id="af820-589">`HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。</span><span class="sxs-lookup"><span data-stu-id="af820-589">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="af820-590">このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。</span><span class="sxs-lookup"><span data-stu-id="af820-590">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="af820-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="af820-591"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="af820-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; 新しい場所の URL。</span><span class="sxs-lookup"><span data-stu-id="af820-592"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> &ndash; The URL of the new location.</span></span>
* <span data-ttu-id="af820-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; `true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。</span><span class="sxs-lookup"><span data-stu-id="af820-593"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> &ndash; If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="af820-594">`false` の場合、[NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) によってナビゲーションが発生しました。</span><span class="sxs-lookup"><span data-stu-id="af820-594">If `false`, [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) caused the navigation to occur.</span></span>

<span data-ttu-id="af820-595">コンポーネントの破棄の詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af820-595">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>
