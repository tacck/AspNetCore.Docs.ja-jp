---
title: ASP.NET Core でのアセンブリの遅延読み込みBlazor WebAssembly
author: guardrex
description: ASP.NET Core Blazor WebAssembly アプリでアセンブリの遅延読み込みを行う方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/16/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 31e6c9638d3262d3cb0a5e0fbcf34d24e2d1e91c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625805"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="6f94b-103">ASP.NET Core でのアセンブリの遅延読み込みBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="6f94b-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="6f94b-104">作成者: [Safia Abdalla](https://safia.rocks)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6f94b-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6f94b-105">Blazor WebAssembly アプリの起動時のパフォーマンスは、一部のアプリケーション アセンブリの読み込みをそれが必要になるまで延期することで改善できます。これは "*遅延読み込み*" と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="6f94b-106">たとえば、1 つのコンポーネントをレンダリングするためにのみ使用されるアセンブリの読み込みを、ユーザーがそのコンポーネントに移動する場合にのみ行うように設定することができます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="6f94b-107">読み込みが完了すると、アセンブリはクライアント側にキャッシュされ、今後のすべてのナビゲーションで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6f94b-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="6f94b-108">Blazor の遅延読み込み機能を使用すると、アプリ アセンブリに遅延読み込みのマークを付けることができます。これにより、実行時にユーザーが特定のルートに移動したとき、アセンブリが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="6f94b-109">この機能は、プロジェクト ファイルに対する変更と、アプリケーションのルーターに対する変更で構成されています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="6f94b-110">Blazor Server アプリでは、アセンブリの遅延読み込みを行っても効果がありません。これは、Blazor Server アプリのクライアントにアセンブリがダウンロードされないためです。</span><span class="sxs-lookup"><span data-stu-id="6f94b-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="6f94b-111">プロジェクト ファイル</span><span class="sxs-lookup"><span data-stu-id="6f94b-111">Project file</span></span>

<span data-ttu-id="6f94b-112">アプリのプロジェクト ファイル (`.csproj`) 内で、`BlazorWebAssemblyLazyLoad` 項目を使用して、遅延読み込みのマークをアセンブリに付けます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="6f94b-113">`.dll` 拡張子を使用せずにアセンブリ名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="6f94b-114">Blazor フレームワークを使用すると、この項目グループによって指定されたアセンブリはアプリの起動時に読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="6f94b-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="6f94b-115">次の例では、大規模なカスタム アセンブリ (`GrantImaharaRobotControls.dll`) に遅延読み込みのマークを付けいています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="6f94b-116">遅延読み込みのマークが付けられているアセンブリに依存関係がある場合、それらにはプロジェクト ファイル内でも遅延読み込みのマークを付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="6f94b-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="6f94b-117">アプリで使用されるアセンブリだけを遅延読み込みすることができます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="6f94b-118">未使用のアセンブリはパブリッシュされた出力からリンカーによって除去されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-118">The linker strips unused assemblies from published output.</span></span>

## <a name="router-component"></a><span data-ttu-id="6f94b-119">`Router` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6f94b-119">`Router` component</span></span>

<span data-ttu-id="6f94b-120">Blazor がルーティング可能なコンポーネントを求めて探索するアセンブリは、Blazor の `Router` コンポーネントによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-120">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="6f94b-121">`Router` コンポーネントは、ユーザーが移動するルートのコンポーネントをレンダリングする役割も担います。</span><span class="sxs-lookup"><span data-stu-id="6f94b-121">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="6f94b-122">`Router` コンポーネントでは、遅延読み込みと組み合わせて使用できる `OnNavigateAsync` 機能がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-122">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="6f94b-123">アプリの `Router` コンポーネント (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="6f94b-123">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="6f94b-124">`OnNavigateAsync` コールバックを追加します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-124">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="6f94b-125">`OnNavigateAsync` ハンドラーは、ユーザーが次のことを行った場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-125">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="6f94b-126">使用しているブラウザーからの直接移動によって、ルートに初めてアクセスする。</span><span class="sxs-lookup"><span data-stu-id="6f94b-126">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="6f94b-127">リンクまたは <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 呼び出しを使用して新しいルートに移動する。</span><span class="sxs-lookup"><span data-stu-id="6f94b-127">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="6f94b-128">遅延読み込みされたアセンブリにルーティング可能なコンポーネントが含まれている場合は、コンポーネントに [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (たとえば、名前付きの `lazyLoadedAssemblies`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-128">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="6f94b-129">アセンブリにルーティング可能なコンポーネントが含まれている場合、そのアセンブリは <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> コレクションに戻されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-129">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="6f94b-130">フレームワークでは、アセンブリ内でルートが検索され、新しいルートが見つかった場合はルート コレクションが更新されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-130">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="6f94b-131">`OnNavigateAsync` コールバックから、ハンドルされない例外がスローされた場合は、[Blazorエラー UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-131">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="6f94b-132">`OnNavigateAsync` 内のアセンブリ読み込みロジック</span><span class="sxs-lookup"><span data-stu-id="6f94b-132">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="6f94b-133">`OnNavigateAsync` には `NavigationContext` パラメーターがあります。これによって、ターゲット パス (`Path`) やキャンセル トークン (`CancellationToken`) など、現在の非同期ナビゲーション イベントに関する情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-133">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="6f94b-134">`Path` プロパティは、`/robot` など、アプリのベース パスを基準としたユーザーの宛先パスです。</span><span class="sxs-lookup"><span data-stu-id="6f94b-134">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="6f94b-135">`CancellationToken` を使用すると、非同期タスクの取り消しを確認することができます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-135">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="6f94b-136">ユーザーが別のページに移動したときは、現在実行中のナビゲーション タスクが `OnNavigateAsync` によって自動的にキャンセルされます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-136">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="6f94b-137">`OnNavigateAsync` 内で、読み込むアセンブリを決定するロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-137">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="6f94b-138">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="6f94b-138">Options include:</span></span>

* <span data-ttu-id="6f94b-139">`OnNavigateAsync` メソッド内での条件チェック。</span><span class="sxs-lookup"><span data-stu-id="6f94b-139">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="6f94b-140">ルートをアセンブリ名にマップするルックアップ テーブル (コンポーネントに挿入されるか、[`@code`](xref:mvc/views/razor#code) ブロック内に実装される)。</span><span class="sxs-lookup"><span data-stu-id="6f94b-140">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="6f94b-141">`LazyAssemblyLoader` はフレームワークによって提供されるシングルトン サービスであり、アセンブリを読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="6f94b-141">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="6f94b-142">`Router` コンポーネントに `LazyAssemblyLoader` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-142">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="6f94b-143">`LazyAssemblyLoader` には、次のような `LoadAssembliesAsync` メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-143">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="6f94b-144">JS 相互運用を使用して、ネットワーク呼び出しを介してアセンブリをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="6f94b-144">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="6f94b-145">ブラウザー内の WebAssembly で実行されているランタイムにアセンブリを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-145">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="6f94b-146">フレームワークの遅延読み込みの実装では、サーバー上でのプリレンダリングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-146">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="6f94b-147">プリレンダリング中は、遅延読み込みのマークが付けられたものも含め、すべてのアセンブリが読み込まれると見なされます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-147">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="6f94b-148">`<Navigating>` コンテンツとのユーザー操作</span><span class="sxs-lookup"><span data-stu-id="6f94b-148">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="6f94b-149">アセンブリの読み込み中 (数秒かかることがあります)、`Router` コンポーネントからユーザーに、ページの切り替えが行われていることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-149">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="6f94b-150"><xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-150">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="6f94b-151">ページ切り替えイベント中に表示するマークアップを含むコンポーネントに `<Navigating>` タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-151">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="6f94b-152">`OnNavigateAsync` でキャンセルを処理する</span><span class="sxs-lookup"><span data-stu-id="6f94b-152">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="6f94b-153">`OnNavigateAsync` コールバックに渡される `NavigationContext` オブジェクトには、新しいナビゲーション イベントが発生したときに設定される `CancellationToken` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-153">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="6f94b-154">このキャンセル トークンが、古いナビゲーションに対して `OnNavigateAsync` コールバックを継続して実行しないように設定されている場合は、`OnNavigateAsync` コールバックをスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6f94b-154">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="6f94b-155">ユーザーがルート A に移動し、その後すぐにルート B に移動する場合、アプリでルート A の `OnNavigateAsync` コールバックを実行し続けるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="6f94b-155">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="6f94b-156">`NavigationContext` 内のキャンセル トークンが取り消された場合にスローしないと、前のナビゲーションからのコンポーネントをレンダリングするなど、意図しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6f94b-156">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="complete-example"></a><span data-ttu-id="6f94b-157">コード例全体</span><span class="sxs-lookup"><span data-stu-id="6f94b-157">Complete example</span></span>

<span data-ttu-id="6f94b-158">次の完全な `Router` コンポーネントによって、ユーザーが `/robot` に移動したときに `GrantImaharaRobotControls.dll` アセンブリを読み込む方法が実演されています。</span><span class="sxs-lookup"><span data-stu-id="6f94b-158">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="6f94b-159">ページの切り替え中に、スタイル付きのメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6f94b-159">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="6f94b-160">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="6f94b-160">Troubleshoot</span></span>

* <span data-ttu-id="6f94b-161">予期しないレンダリングが発生した場合 (たとえば、前のナビゲーションからのコンポーネントがレンダリングされる) は、キャンセル トークンが設定されている場合にコードがスローされることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="6f94b-161">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="6f94b-162">アプリケーションの起動時にアセンブリがまだ読み込まれている場合は、アセンブリがプロジェクト ファイル内で遅延読み込みとしてマークされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6f94b-162">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f94b-163">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6f94b-163">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
