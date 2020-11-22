---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
ms.openlocfilehash: c4da8bf8447618c9a7a2d0f690164fe48a7ed006
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703697"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="db5a0-103">ASP.NET Core Blazor のルーティング</span><span class="sxs-lookup"><span data-stu-id="db5a0-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="db5a0-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db5a0-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="db5a0-105">Blazor アプリで、要求をルーティングする方法と、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用してナビゲーション リンクを作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="db5a0-106">ASP.NET Core エンドポイントのルーティングの統合</span><span class="sxs-lookup"><span data-stu-id="db5a0-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="db5a0-107">Blazor Server は [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="db5a0-108">ASP.NET Core アプリは、`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="db5a0-109">最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor Server アプリのサーバー側部分のホストとして機能します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="db5a0-110">通常、*ホスト* ページは、`_Host.cshtml` という名前になります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="db5a0-111">ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート* と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="db5a0-112">フォールバック ルートは、他のルートが一致しない場合に考慮されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="db5a0-113">これにより、Blazor Server アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="db5a0-114">ルート以外の URL のサーバー ホスト用に <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> を構成する方法の詳細については、<xref:blazor/host-and-deploy/index#app-base-path> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="db5a0-115">ルート テンプレート</span><span class="sxs-lookup"><span data-stu-id="db5a0-115">Route templates</span></span>

<span data-ttu-id="db5a0-116"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="db5a0-117"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは `App.razor` ファイルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

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

<span data-ttu-id="db5a0-118">`@page` ディレクティブを含む `.razor` ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="db5a0-119">実行時に、<xref:Microsoft.AspNetCore.Components.RouteView> コンポーネントは、</span><span class="sxs-lookup"><span data-stu-id="db5a0-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="db5a0-120"><xref:Microsoft.AspNetCore.Components.Routing.Router> から、必要なパラメーターと共に <xref:Microsoft.AspNetCore.Components.RouteData> を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="db5a0-121">指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="db5a0-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="db5a0-122">必要に応じて、レイアウト クラスで <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="db5a0-123">既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="db5a0-124">`MainLayout.razor` はテンプレート プロジェクトの `Shared` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="db5a0-125">レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="db5a0-126">コンポーネントには、複数のルート テンプレートを適用できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="db5a0-127">次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="db5a0-128">URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に `<base>` タグを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="db5a0-129">詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="db5a0-130">コンテンツが見つからないときにカスタム コンテンツを提供する</span><span class="sxs-lookup"><span data-stu-id="db5a0-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="db5a0-131"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="db5a0-132">`App.razor` ファイルで、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントの <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> テンプレート パラメーターにカスタム コンテンツを設定します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

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

<span data-ttu-id="db5a0-133">`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="db5a0-134"><xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="db5a0-135">複数のアセンブリからコンポーネントにルーティングする</span><span class="sxs-lookup"><span data-stu-id="db5a0-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="db5a0-136"><xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> パラメーターを使用して、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="db5a0-137">指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="db5a0-138">次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="db5a0-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="db5a0-139">次の <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> の例では、`Component1` のルーティング サポートの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="db5a0-140">ルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="db5a0-140">Route parameters</span></span>

<span data-ttu-id="db5a0-141">ルーターでルート パラメーターを使用すれば、同じ名前の対応するコンポーネント パラメーターを設定できます (大文字と小文字は区別されません)。</span><span class="sxs-lookup"><span data-stu-id="db5a0-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="db5a0-142">省略可能なパラメーターがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-142">Optional parameters are supported.</span></span> <span data-ttu-id="db5a0-143">次の例では、省略可能なパラメーター `text` を使用して、ルート セグメントの値をコンポーネントの `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-143">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="db5a0-144">セグメントが存在しない場合、`Text` の値は `fantastic` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-144">If the segment isn't present, the value of `Text` is set to `fantastic`:</span></span>

```razor
@page "/RouteParameter/{text?}"

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

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

<span data-ttu-id="db5a0-145">省略可能なパラメーターはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="db5a0-145">Optional parameters aren't supported.</span></span> <span data-ttu-id="db5a0-146">前の例では、2 つの `@page` ディレクティブが適用されています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-146">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="db5a0-147">1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-147">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="db5a0-148">2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-148">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="db5a0-149">オプションのパラメーター値が異なる同じコンポーネントへのアプリの移動を許可するには、[`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) ではなく、[`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) を使用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-149">Use on [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="db5a0-150">前の例に基づいて、ユーザーが `/RouteParameter` から `/RouteParameter/awesome` に、または `/RouteParameter/awesome` から `/RouteParameter` に移動できる必要がある場合は、`OnParametersSet` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/awesome` or from `/RouteParameter/awesome` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="db5a0-151">ルート制約</span><span class="sxs-lookup"><span data-stu-id="db5a0-151">Route constraints</span></span>

<span data-ttu-id="db5a0-152">ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="db5a0-153">次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-153">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="db5a0-154">要求 URL に `Id` ルート セグメントが存在する。</span><span class="sxs-lookup"><span data-stu-id="db5a0-154">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="db5a0-155">`Id` セグメントは整数 (`int`) である。</span><span class="sxs-lookup"><span data-stu-id="db5a0-155">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="db5a0-156">次の表に示すルート制約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-156">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="db5a0-157">インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-157">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="db5a0-158">制約</span><span class="sxs-lookup"><span data-stu-id="db5a0-158">Constraint</span></span> | <span data-ttu-id="db5a0-159">例</span><span class="sxs-lookup"><span data-stu-id="db5a0-159">Example</span></span>           | <span data-ttu-id="db5a0-160">一致の例</span><span class="sxs-lookup"><span data-stu-id="db5a0-160">Example Matches</span></span>                                                                  | <span data-ttu-id="db5a0-161">インバリアント</span><span class="sxs-lookup"><span data-stu-id="db5a0-161">Invariant</span></span><br><span data-ttu-id="db5a0-162">カルチャ</span><span class="sxs-lookup"><span data-stu-id="db5a0-162">culture</span></span><br><span data-ttu-id="db5a0-163">一致</span><span class="sxs-lookup"><span data-stu-id="db5a0-163">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="db5a0-164">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="db5a0-164">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="db5a0-165">いいえ</span><span class="sxs-lookup"><span data-stu-id="db5a0-165">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="db5a0-166">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="db5a0-166">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="db5a0-167">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-167">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="db5a0-168">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="db5a0-168">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="db5a0-169">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-169">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="db5a0-170">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="db5a0-170">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="db5a0-171">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-171">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="db5a0-172">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="db5a0-172">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="db5a0-173">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-173">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="db5a0-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="db5a0-174">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="db5a0-175">いいえ</span><span class="sxs-lookup"><span data-stu-id="db5a0-175">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="db5a0-176">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="db5a0-176">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="db5a0-177">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-177">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="db5a0-178">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="db5a0-178">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="db5a0-179">はい</span><span class="sxs-lookup"><span data-stu-id="db5a0-179">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="db5a0-180">URL の妥当性を検証し、CLR 型 (`int` や <xref:System.DateTime> など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-180">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="db5a0-181">これらの制約では、URL がローカライズ不可であることが前提となります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-181">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="db5a0-182">ドットを含む URL によるルーティング</span><span class="sxs-lookup"><span data-stu-id="db5a0-182">Routing with URLs that contain dots</span></span>

<span data-ttu-id="db5a0-183">ホストされている Blazor WebAssembly および Blazor Server アプリの場合、サーバー側の既定のルート テンプレートでは、ファイルが要求されているドット (`.`) が要求 URL の最後のセグメントに含まれていると想定されます (例: `https://localhost.com:5001/example/some.thing`)。</span><span class="sxs-lookup"><span data-stu-id="db5a0-183">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="db5a0-184">追加の構成がなければ、コンポーネントへのルーティングが意図されている場合は、アプリによって "*404 - 見つかりません*" が返されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-184">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="db5a0-185">ドットが含まれる 1 つ以上のパラメーターを指定してルートを使用するには、アプリでカスタム テンプレートを使用してルートを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-185">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="db5a0-186">URL の最後のセグメントからルート パラメーターを受け取ることができる次の `Example` コンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-186">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

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

<span data-ttu-id="db5a0-187">ホストされている Blazor WebAssembly ソリューションの "*サーバー*" アプリで、`param` パラメーターのドットを使用して要求をルーティングできるようにするには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ファイル ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-187">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="db5a0-188">`param` パラメーターのドットを使用して要求をルーティングするように Blazor Server アプリを構成するには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ページ ルート テンプレートを追加します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-188">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="db5a0-189">詳細については、「<xref:fundamentals/routing>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-189">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="db5a0-190">キャッチオールのルート パラメーター</span><span class="sxs-lookup"><span data-stu-id="db5a0-190">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="db5a0-191">"*このセクションは、.NET 5 リリース候補 1 (RC1) 以降の ASP.NET Core に適用されます。* "</span><span class="sxs-lookup"><span data-stu-id="db5a0-191">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="db5a0-192">複数のフォルダー境界にまたがるパスをキャプチャするキャッチオール ルート パラメーターが、コンポーネントでサポートされます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-192">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="db5a0-193">キャッチオール ルート パラメーターは次のとおりであることが必要です。</span><span class="sxs-lookup"><span data-stu-id="db5a0-193">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="db5a0-194">ルート セグメント名と一致する名前が付けられている。</span><span class="sxs-lookup"><span data-stu-id="db5a0-194">Named to match the route segment name.</span></span> <span data-ttu-id="db5a0-195">名前付けで大文字と小文字は区別されない。</span><span class="sxs-lookup"><span data-stu-id="db5a0-195">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="db5a0-196">`string` 型。</span><span class="sxs-lookup"><span data-stu-id="db5a0-196">A `string` type.</span></span> <span data-ttu-id="db5a0-197">フレームワークによって自動キャストは提供されません。</span><span class="sxs-lookup"><span data-stu-id="db5a0-197">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="db5a0-198">URL の末尾。</span><span class="sxs-lookup"><span data-stu-id="db5a0-198">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="db5a0-199">ルート テンプレートが `/page/{*pageRoute}` の URL `/page/this/is/a/test` の場合、`PageRoute` の値は `this/is/a/test` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-199">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="db5a0-200">キャプチャされたパスのスラッシュとセグメントはデコードされます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-200">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="db5a0-201">`/page/{*pageRoute}` のルート テンプレートでは、URL `/page/this/is/a%2Ftest%2A` から `this/is/a/test*` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-201">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="db5a0-202">キャッチオール ルート パラメーターは、ASP.NET Core 5.0 以降でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-202">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="db5a0-203">NavLink コンポーネント</span><span class="sxs-lookup"><span data-stu-id="db5a0-203">NavLink component</span></span>

<span data-ttu-id="db5a0-204">ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-204">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="db5a0-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-205">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="db5a0-206">`active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-206">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="db5a0-207">必要に応じて、CSS クラス名を <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> に割り当てて、現在のルートが `href` と一致したときに、レンダリングされるリンクにカスタム CSS クラスを適用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-207">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="db5a0-208">次の `NavMenu` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントの使用方法を示す [`Bootstrap`](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-208">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="db5a0-209">`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-209">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="db5a0-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>:<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL 全体に一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-210"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="db5a0-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*既定値*):<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-211"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="db5a0-212">前の例では、ホーム <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-212">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="db5a0-213">2 番目の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-213">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="db5a0-214">追加の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネント属性は、レンダリングされるアンカー タグに渡されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-214">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="db5a0-215">次の例では、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントに `target` 属性が含まれています。</span><span class="sxs-lookup"><span data-stu-id="db5a0-215">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="db5a0-216">次の HTML マークアップがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-216">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="db5a0-217">Blazor による子コンテンツのレンダリング方法により、`for` ループ内の `NavLink` コンポーネントのレンダリングでは、インクリメントするループ変数が `NavLink` (子) コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-217">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="db5a0-218">このシナリオでのインデックス変数の使用は、`NavLink` コンポーネントだけでなく、[子コンテンツ](xref:blazor/components/index#child-content)でループ変数を使用する **すべての** 子コンポーネントで必須です。</span><span class="sxs-lookup"><span data-stu-id="db5a0-218">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="db5a0-219">または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-219">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="db5a0-220">URI およびナビゲーション状態ヘルパー</span><span class="sxs-lookup"><span data-stu-id="db5a0-220">URI and navigation state helpers</span></span>

<span data-ttu-id="db5a0-221">C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-221">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="db5a0-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> には、次の表に示すイベントとメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-222"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="db5a0-223">メンバー</span><span class="sxs-lookup"><span data-stu-id="db5a0-223">Member</span></span> | <span data-ttu-id="db5a0-224">説明</span><span class="sxs-lookup"><span data-stu-id="db5a0-224">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="db5a0-225">現在の絶対 URI を取得します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-225">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="db5a0-226">絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-226">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="db5a0-227">通常、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> は `wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-227">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="db5a0-228">指定された URI に移動します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-228">Navigates to the specified URI.</span></span> <span data-ttu-id="db5a0-229">`forceLoad` が `true` の場合:</span><span class="sxs-lookup"><span data-stu-id="db5a0-229">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="db5a0-230">クライアント側のルーティングはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-230">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="db5a0-231">URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-231">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="db5a0-232">ナビゲーションの場所が変更されたときに発生するイベントです。</span><span class="sxs-lookup"><span data-stu-id="db5a0-232">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="db5a0-233">相対 URI を絶対 URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-233">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="db5a0-234">ベース URI (たとえば、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-234">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="db5a0-235">次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-235">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

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

<span data-ttu-id="db5a0-236">次のコンポーネントでは、<xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> をサブスクライブすることで、場所の変更イベントが処理されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-236">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="db5a0-237">`HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。</span><span class="sxs-lookup"><span data-stu-id="db5a0-237">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="db5a0-238">このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-238">Unhooking the method permits garbage collection of the component.</span></span>

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

<span data-ttu-id="db5a0-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-239"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="db5a0-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>:新しい場所の URL。</span><span class="sxs-lookup"><span data-stu-id="db5a0-240"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="db5a0-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>:`true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。</span><span class="sxs-lookup"><span data-stu-id="db5a0-241"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="db5a0-242">`false` の場合、<xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> によってナビゲーションが発生しました。</span><span class="sxs-lookup"><span data-stu-id="db5a0-242">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="db5a0-243">コンポーネントの破棄の詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db5a0-243">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="db5a0-244">クエリ文字列とパラメーターの解析</span><span class="sxs-lookup"><span data-stu-id="db5a0-244">Query string and parse parameters</span></span>

<span data-ttu-id="db5a0-245">要求のクエリ文字列は、<xref:Microsoft.AspNetCore.Components.NavigationManager> の <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> プロパティから取得できます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-245">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="db5a0-246">クエリ文字列のパラメーターを解析するには:</span><span class="sxs-lookup"><span data-stu-id="db5a0-246">To parse a query string's parameters:</span></span>

* <span data-ttu-id="db5a0-247">[Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) のパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-247">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="db5a0-248"><xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> を使用してクエリ文字列を解析した後に値を取得します。</span><span class="sxs-lookup"><span data-stu-id="db5a0-248">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

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

<span data-ttu-id="db5a0-249">前の例のプレースホルダー `{KEY}` は、クエリ文字列パラメーターのキーです。</span><span class="sxs-lookup"><span data-stu-id="db5a0-249">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="db5a0-250">たとえば、URL のキーと値のペア `?ship=Tardis` では、キー `ship` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="db5a0-250">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
