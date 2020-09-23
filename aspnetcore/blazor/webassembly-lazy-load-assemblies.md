---
title: ASP.NET Core でのアセンブリの遅延読み込みBlazor WebAssembly
author: guardrex
description: ASP.NET Core Blazor WebAssembly アプリでアセンブリの遅延読み込みを行う方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
ms.openlocfilehash: eb4aaa2f3d412cdf650ed2daf7c12166991d92a1
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592905"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="09082-103">ASP.NET Core でのアセンブリの遅延読み込みBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="09082-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="09082-104">作成者: [Safia Abdalla](https://safia.rocks)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="09082-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="09082-105">Blazor WebAssembly アプリの起動時のパフォーマンスは、一部のアプリケーション アセンブリの読み込みをそれが必要になるまで延期することで改善できます。これは "*遅延読み込み*" と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="09082-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="09082-106">たとえば、1 つのコンポーネントをレンダリングするためにのみ使用されるアセンブリの読み込みを、ユーザーがそのコンポーネントに移動する場合にのみ行うように設定することができます。</span><span class="sxs-lookup"><span data-stu-id="09082-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="09082-107">読み込みが完了すると、アセンブリはクライアント側にキャッシュされ、今後のすべてのナビゲーションで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="09082-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="09082-108">Blazor の遅延読み込み機能を使用すると、アプリ アセンブリに遅延読み込みのマークを付けることができます。これにより、実行時にユーザーが特定のルートに移動したとき、アセンブリが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="09082-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="09082-109">この機能は、プロジェクト ファイルに対する変更と、アプリケーションのルーターに対する変更で構成されています。</span><span class="sxs-lookup"><span data-stu-id="09082-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="09082-110">Blazor Server アプリでは、アセンブリの遅延読み込みを行っても効果がありません。これは、Blazor Server アプリのクライアントにアセンブリがダウンロードされないためです。</span><span class="sxs-lookup"><span data-stu-id="09082-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="09082-111">プロジェクト ファイル</span><span class="sxs-lookup"><span data-stu-id="09082-111">Project file</span></span>

<span data-ttu-id="09082-112">アプリのプロジェクト ファイル (`.csproj`) 内で、`BlazorWebAssemblyLazyLoad` 項目を使用して、遅延読み込みのマークをアセンブリに付けます。</span><span class="sxs-lookup"><span data-stu-id="09082-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="09082-113">`.dll` 拡張子を使用せずにアセンブリ名を使用します。</span><span class="sxs-lookup"><span data-stu-id="09082-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="09082-114">Blazor フレームワークを使用すると、この項目グループによって指定されたアセンブリはアプリの起動時に読み込まれません。</span><span class="sxs-lookup"><span data-stu-id="09082-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="09082-115">次の例では、大規模なカスタム アセンブリ (`GrantImaharaRobotControls.dll`) に遅延読み込みのマークを付けいています。</span><span class="sxs-lookup"><span data-stu-id="09082-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="09082-116">遅延読み込みのマークが付けられているアセンブリに依存関係がある場合、それらにはプロジェクト ファイル内でも遅延読み込みのマークを付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="09082-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a><span data-ttu-id="09082-117">`Router` コンポーネント</span><span class="sxs-lookup"><span data-stu-id="09082-117">`Router` component</span></span>

<span data-ttu-id="09082-118">Blazor がルーティング可能なコンポーネントを求めて探索するアセンブリは、Blazor の `Router` コンポーネントによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="09082-118">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="09082-119">`Router` コンポーネントは、ユーザーが移動するルートのコンポーネントをレンダリングする役割も担います。</span><span class="sxs-lookup"><span data-stu-id="09082-119">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="09082-120">`Router` コンポーネントでは、遅延読み込みと組み合わせて使用できる `OnNavigateAsync` 機能がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="09082-120">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="09082-121">アプリの `Router` コンポーネント (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="09082-121">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="09082-122">`OnNavigateAsync` コールバックを追加します。</span><span class="sxs-lookup"><span data-stu-id="09082-122">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="09082-123">`OnNavigateAsync` ハンドラーは、ユーザーが次のことを行った場合に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="09082-123">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="09082-124">使用しているブラウザーからの直接移動によって、ルートに初めてアクセスする。</span><span class="sxs-lookup"><span data-stu-id="09082-124">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="09082-125">リンクまたは <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 呼び出しを使用して新しいルートに移動する。</span><span class="sxs-lookup"><span data-stu-id="09082-125">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="09082-126">遅延読み込みされたアセンブリにルーティング可能なコンポーネントが含まれている場合は、コンポーネントに [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (たとえば、名前付きの `lazyLoadedAssemblies`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="09082-126">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="09082-127">アセンブリにルーティング可能なコンポーネントが含まれている場合、そのアセンブリは <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> コレクションに戻されます。</span><span class="sxs-lookup"><span data-stu-id="09082-127">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="09082-128">フレームワークでは、アセンブリ内でルートが検索され、新しいルートが見つかった場合はルート コレクションが更新されます。</span><span class="sxs-lookup"><span data-stu-id="09082-128">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

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

<span data-ttu-id="09082-129">`OnNavigateAsync` コールバックから、ハンドルされない例外がスローされた場合は、[Blazorエラー UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="09082-129">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="09082-130">`OnNavigateAsync` 内のアセンブリ読み込みロジック</span><span class="sxs-lookup"><span data-stu-id="09082-130">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="09082-131">`OnNavigateAsync` には `NavigationContext` パラメーターがあります。これによって、ターゲット パス (`Path`) やキャンセル トークン (`CancellationToken`) など、現在の非同期ナビゲーション イベントに関する情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="09082-131">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="09082-132">`Path` プロパティは、`/robot` など、アプリのベース パスを基準としたユーザーの宛先パスです。</span><span class="sxs-lookup"><span data-stu-id="09082-132">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="09082-133">`CancellationToken` を使用すると、非同期タスクの取り消しを確認することができます。</span><span class="sxs-lookup"><span data-stu-id="09082-133">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="09082-134">ユーザーが別のページに移動したときは、現在実行中のナビゲーション タスクが `OnNavigateAsync` によって自動的にキャンセルされます。</span><span class="sxs-lookup"><span data-stu-id="09082-134">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="09082-135">`OnNavigateAsync` 内で、読み込むアセンブリを決定するロジックを実装します。</span><span class="sxs-lookup"><span data-stu-id="09082-135">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="09082-136">次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="09082-136">Options include:</span></span>

* <span data-ttu-id="09082-137">`OnNavigateAsync` メソッド内での条件チェック。</span><span class="sxs-lookup"><span data-stu-id="09082-137">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="09082-138">ルートをアセンブリ名にマップするルックアップ テーブル (コンポーネントに挿入されるか、[`@code`](xref:mvc/views/razor#code) ブロック内に実装される)。</span><span class="sxs-lookup"><span data-stu-id="09082-138">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="09082-139">`LazyAssemblyLoader` はフレームワークによって提供されるシングルトン サービスであり、アセンブリを読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="09082-139">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="09082-140">`Router` コンポーネントに `LazyAssemblyLoader` を挿入します。</span><span class="sxs-lookup"><span data-stu-id="09082-140">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="09082-141">`LazyAssemblyLoader` には、次のような `LoadAssembliesAsync` メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="09082-141">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="09082-142">JS 相互運用を使用して、ネットワーク呼び出しを介してアセンブリをフェッチします。</span><span class="sxs-lookup"><span data-stu-id="09082-142">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="09082-143">ブラウザー内の WebAssembly で実行されているランタイムにアセンブリを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="09082-143">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

<span data-ttu-id="09082-144">フレームワークの遅延読み込みの実装では、ホストされた Blazor ソリューションでのプリレンダリングによる遅延読み込みがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="09082-144">The framework's lazy loading implementation supports lazy loading with prerendering in a hosted Blazor solution.</span></span> <span data-ttu-id="09082-145">プリレンダリング中は、遅延読み込みのマークが付けられたものも含め、すべてのアセンブリが読み込まれると見なされます。</span><span class="sxs-lookup"><span data-stu-id="09082-145">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span> <span data-ttu-id="09082-146">"*サーバー*" プロジェクトの `Startup.ConfigureServices` メソッド (`Startup.cs`) に、手動で `LazyAssemblyLoader` を登録します。</span><span class="sxs-lookup"><span data-stu-id="09082-146">Manually register `LazyAssemblyLoader` in the *Server* project's `Startup.ConfigureServices` method (`Startup.cs`):</span></span>

```csharp
services.AddSingleton<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="09082-147">`<Navigating>` コンテンツとのユーザー操作</span><span class="sxs-lookup"><span data-stu-id="09082-147">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="09082-148">アセンブリの読み込み中 (数秒かかることがあります)、`Router` コンポーネントからユーザーに、ページの切り替えが行われていることを示すことができます。</span><span class="sxs-lookup"><span data-stu-id="09082-148">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="09082-149"><xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> 名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブを追加します。</span><span class="sxs-lookup"><span data-stu-id="09082-149">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="09082-150">ページ切り替えイベント中に表示するマークアップを含むコンポーネントに `<Navigating>` タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="09082-150">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

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

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="09082-151">`OnNavigateAsync` でキャンセルを処理する</span><span class="sxs-lookup"><span data-stu-id="09082-151">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="09082-152">`OnNavigateAsync` コールバックに渡される `NavigationContext` オブジェクトには、新しいナビゲーション イベントが発生したときに設定される `CancellationToken` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="09082-152">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="09082-153">このキャンセル トークンが、古いナビゲーションに対して `OnNavigateAsync` コールバックを継続して実行しないように設定されている場合は、`OnNavigateAsync` コールバックをスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="09082-153">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="09082-154">ユーザーがルート A に移動し、その後すぐにルート B に移動する場合、アプリでルート A の `OnNavigateAsync` コールバックを実行し続けるべきではありません。</span><span class="sxs-lookup"><span data-stu-id="09082-154">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

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
> <span data-ttu-id="09082-155">`NavigationContext` 内のキャンセル トークンが取り消された場合にスローしないと、前のナビゲーションからのコンポーネントをレンダリングするなど、意図しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="09082-155">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="09082-156">`OnNavigateAsync` イベントと名前が変更されたアセンブリ ファイル</span><span class="sxs-lookup"><span data-stu-id="09082-156">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="09082-157">リソース ローダーでは、`blazor.boot.json` ファイルで定義されているアセンブリ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="09082-157">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="09082-158">[アセンブリの名前が変更された](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files)場合、`OnNavigateAsync` メソッドで使用されるアセンブリ名と `blazor.boot.json` ファイル内のアセンブリ名が同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="09082-158">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="09082-159">これを修正するには:</span><span class="sxs-lookup"><span data-stu-id="09082-159">To rectify this:</span></span>

* <span data-ttu-id="09082-160">使用するアセンブリ名を決定するときに、アプリが運用環境で実行されているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="09082-160">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="09082-161">名前を変更したアセンブリ名を別のファイルに格納し、そのファイルから読み取りを行い、`LazyLoadAssemblyService` および `OnNavigateAsync` メソッドで使用するアセンブリ名を決定します。</span><span class="sxs-lookup"><span data-stu-id="09082-161">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="09082-162">コード例全体</span><span class="sxs-lookup"><span data-stu-id="09082-162">Complete example</span></span>

<span data-ttu-id="09082-163">次の完全な `Router` コンポーネントによって、ユーザーが `/robot` に移動したときに `GrantImaharaRobotControls.dll` アセンブリを読み込む方法が実演されています。</span><span class="sxs-lookup"><span data-stu-id="09082-163">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="09082-164">ページの切り替え中に、スタイル付きのメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="09082-164">During page transitions, a styled message is displayed to the user.</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="09082-165">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="09082-165">Troubleshoot</span></span>

* <span data-ttu-id="09082-166">予期しないレンダリングが発生した場合 (たとえば、前のナビゲーションからのコンポーネントがレンダリングされる) は、キャンセル トークンが設定されている場合にコードがスローされることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="09082-166">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="09082-167">アプリケーションの起動時にアセンブリがまだ読み込まれている場合は、アセンブリがプロジェクト ファイル内で遅延読み込みとしてマークされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="09082-167">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="09082-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="09082-168">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
