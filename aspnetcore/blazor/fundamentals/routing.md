---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- appsettings.json
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 8f0aa80d092b6678131a2b7152f21ecb8e168257
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430992"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="cb5e3-103">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="cb5e3-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="cb5e3-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb5e3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cb5e3-105">Blazor アプリで、要求をルーティングする方法と、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用してナビゲーション リンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="cb5e3-106">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="cb5e3-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="cb5e3-107">Blazor Server は [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="cb5e3-108">ASP.NET Core アプリは、`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="cb5e3-109">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor Server アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="cb5e3-110">通常、*ホスト* ページは、`_Host.cshtml` という名前になります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="cb5e3-111">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="cb5e3-112">フォールバック ルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="cb5e3-113">これにより、Blazor Server アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="cb5e3-114">ルート以外の URL のサーバー ホスト用に <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> を構成する方法の詳細については、<xref:blazor/host-and-deploy/index#app-base-path> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="cb5e3-115">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="cb5e3-115">Route templates</span></span>

<span data-ttu-id="cb5e3-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="cb5e3-117"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは `App.razor` ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="cb5e3-118">`@page` ディレクティブを含む `.razor` ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="cb5e3-119">実行時に、<xref:Microsoft.AspNetCore.Components.RouteView> コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="cb5e3-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="cb5e3-120"><xref:Microsoft.AspNetCore.Components.Routing.Router> から、必要なパラメーターと共に <xref:Microsoft.AspNetCore.Components.RouteData> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="cb5e3-121">指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="cb5e3-122">必要に応じて、レイアウト クラスで <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="cb5e3-123">既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="cb5e3-124">`MainLayout.razor` はテンプレート プロジェクトの `Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="cb5e3-125">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="cb5e3-126">コンポーネントには、複数のルート テンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="cb5e3-127">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="cb5e3-128">URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に `<base>` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="cb5e3-129">詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="cb5e3-130">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="cb5e3-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="cb5e3-131"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="cb5e3-132">`App.razor` ファイルで、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントの <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> テンプレート パラメーターにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="cb5e3-133">`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="cb5e3-134"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="cb5e3-135">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="cb5e3-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="cb5e3-136"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> パラメーターを使用して、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="cb5e3-137">指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="cb5e3-138">次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="cb5e3-139">次の <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> の例では、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="cb5e3-140">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="cb5e3-140">Route parameters</span></span>

<span data-ttu-id="cb5e3-141">ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

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

<span data-ttu-id="cb5e3-142">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="cb5e3-143">前の例では、2 つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="cb5e3-144">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="cb5e3-145">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="cb5e3-146">ルート制約</span><span class="sxs-lookup"><span data-stu-id="cb5e3-146">Route constraints</span></span>

<span data-ttu-id="cb5e3-147">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="cb5e3-148">次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="cb5e3-149">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="cb5e3-150">`Id` セグメントは整数 (`int`) である。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="cb5e3-151">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="cb5e3-152">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="cb5e3-153">制約</span><span class="sxs-lookup"><span data-stu-id="cb5e3-153">Constraint</span></span> | <span data-ttu-id="cb5e3-154">例</span><span class="sxs-lookup"><span data-stu-id="cb5e3-154">Example</span></span>           | <span data-ttu-id="cb5e3-155">一致の例</span><span class="sxs-lookup"><span data-stu-id="cb5e3-155">Example Matches</span></span>                                                                  | <span data-ttu-id="cb5e3-156">インバリアント</span><span class="sxs-lookup"><span data-stu-id="cb5e3-156">Invariant</span></span><br><span data-ttu-id="cb5e3-157">カルチャ</span><span class="sxs-lookup"><span data-stu-id="cb5e3-157">culture</span></span><br><span data-ttu-id="cb5e3-158">一致</span><span class="sxs-lookup"><span data-stu-id="cb5e3-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="cb5e3-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="cb5e3-160">いいえ</span><span class="sxs-lookup"><span data-stu-id="cb5e3-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="cb5e3-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="cb5e3-162">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="cb5e3-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="cb5e3-164">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="cb5e3-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="cb5e3-166">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="cb5e3-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="cb5e3-168">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="cb5e3-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="cb5e3-170">いいえ</span><span class="sxs-lookup"><span data-stu-id="cb5e3-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="cb5e3-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="cb5e3-172">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="cb5e3-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="cb5e3-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="cb5e3-174">はい</span><span class="sxs-lookup"><span data-stu-id="cb5e3-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="cb5e3-175">URL の妥当性を検証し、CLR 型 (`int` や <xref:System.DateTime> など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="cb5e3-176">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="cb5e3-177">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="cb5e3-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="cb5e3-178">ホストされている Blazor WebAssembly および Blazor Server アプリの場合、サーバー側の既定のルート テンプレートでは、ファイルが要求されているドット (`.`) が要求 URL の最後のセグメントに含まれていると想定されます (例: `https://localhost.com:5001/example/some.thing`)。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-178">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="cb5e3-179">追加の構成がなければ、コンポーネントへのルーティングが意図されている場合は、アプリによって "*404 - 見つかりません*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="cb5e3-180">ドットが含まれる 1 つ以上のパラメーターを指定してルートを使用するには、アプリでカスタム テンプレートを使用してルートを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="cb5e3-181">URL の最後のセグメントからルート パラメーターを受け取ることができる次の `Example` コンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="cb5e3-182">ホストされている Blazor WebAssembly ソリューションの "*サーバー*" アプリで、`param` パラメーターのドットを使用して要求をルーティングできるようにするには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ファイル ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-182">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="cb5e3-183">`param` パラメーターのドットを使用して要求をルーティングするように Blazor Server アプリを構成するには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ページ ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-183">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="cb5e3-184">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="cb5e3-185">キャッチオールのルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="cb5e3-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cb5e3-186">"*このセクションは、.NET 5 リリース候補 1 (RC1) 以降の ASP.NET Core に適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="cb5e3-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="cb5e3-187">複数のフォルダー境界にまたがるパスをキャプチャするキャッチオール ルート パラメーターが、コンポーネントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="cb5e3-188">キャッチオール ルート パラメーターは次のとおりであることが必要です。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="cb5e3-189">ルート セグメント名と一致する名前が付けられている。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-189">Named to match the route segment name.</span></span> <span data-ttu-id="cb5e3-190">名前付けで大文字と小文字は区別されない。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="cb5e3-191">`string` 型。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-191">A `string` type.</span></span> <span data-ttu-id="cb5e3-192">フレームワークによって自動キャストは提供されません。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="cb5e3-193">URL の末尾。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="cb5e3-194">ルート テンプレートが `/page/{*pageRoute}` の URL `/page/this/is/a/test` の場合、`PageRoute` の値は `this/is/a/test` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="cb5e3-195">キャプチャされたパスのスラッシュとセグメントはデコードされます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="cb5e3-196">`/page/{*pageRoute}` のルート テンプレートでは、URL `/page/this/is/a%2Ftest%2A` から `this/is/a/test*` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cb5e3-197">キャッチオール ルート パラメーターは、ASP.NET Core 5.0 以降でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-197">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="cb5e3-198">NavLink コンポーネント</span><span class="sxs-lookup"><span data-stu-id="cb5e3-198">NavLink component</span></span>

<span data-ttu-id="cb5e3-199">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="cb5e3-200"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="cb5e3-201">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="cb5e3-202">必要に応じて、CSS クラス名を <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> に割り当てて、現在のルートが `href` と一致したときに、レンダリングされるリンクにカスタム CSS クラスを適用します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="cb5e3-203">次の `NavMenu` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントの使用方法を示す [`Bootstrap`](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="cb5e3-204">`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="cb5e3-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>:<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="cb5e3-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*既定値*):<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="cb5e3-207">前の例では、ホーム <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="cb5e3-208">2 番目の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="cb5e3-209">追加の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="cb5e3-210">次の例では、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="cb5e3-211">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="cb5e3-212">Blazor による子コンテンツのレンダリング方法により、`for` ループ内の `NavLink` コンポーネントのレンダリングでは、インクリメントするループ変数が `NavLink` (子) コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-212">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="cb5e3-213">このシナリオでのインデックス変数の使用は、`NavLink` コンポーネントだけでなく、[子コンテンツ](xref:blazor/components/index#child-content)でループ変数を使用する **すべての** 子コンポーネントで必須です。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="cb5e3-214">または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="cb5e3-215">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="cb5e3-215">URI and navigation state helpers</span></span>

<span data-ttu-id="cb5e3-216">C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="cb5e3-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="cb5e3-218">メンバー</span><span class="sxs-lookup"><span data-stu-id="cb5e3-218">Member</span></span> | <span data-ttu-id="cb5e3-219">説明</span><span class="sxs-lookup"><span data-stu-id="cb5e3-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="cb5e3-220">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="cb5e3-221">絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="cb5e3-222">通常、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> は `wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="cb5e3-223">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-223">Navigates to the specified URI.</span></span> <span data-ttu-id="cb5e3-224">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="cb5e3-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="cb5e3-225">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="cb5e3-226">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="cb5e3-227">ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="cb5e3-228">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="cb5e3-229">ベース URI (たとえば、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="cb5e3-230">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="cb5e3-231">次のコンポーネントでは、<xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> をサブスクライブすることで、場所の変更イベントが処理されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cb5e3-232">`HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="cb5e3-233">このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-233">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="cb5e3-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="cb5e3-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>:新しい場所の URL。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="cb5e3-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>:`true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="cb5e3-237">`false` の場合、<xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> によってナビゲーションが発生しました。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="cb5e3-238">コンポーネントの破棄の詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="cb5e3-239">クエリ文字列とパラメーターの解析</span><span class="sxs-lookup"><span data-stu-id="cb5e3-239">Query string and parse parameters</span></span>

<span data-ttu-id="cb5e3-240">要求のクエリ文字列は、<xref:Microsoft.AspNetCore.Components.NavigationManager> の <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> プロパティから取得できます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="cb5e3-241">クエリ文字列のパラメーターを解析するには:</span><span class="sxs-lookup"><span data-stu-id="cb5e3-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="cb5e3-242">[Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="cb5e3-243"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> を使用してクエリ文字列を解析した後に値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="cb5e3-244">前の例のプレースホルダー `{KEY}` は、クエリ文字列パラメーターのキーです。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="cb5e3-245">たとえば、URL のキーと値のペア `?ship=Tardis` では、キー `ship` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="cb5e3-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
