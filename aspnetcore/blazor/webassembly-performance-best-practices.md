---
title: ASP.NET Core Blazor WebAssembly パフォーマンスに関するベスト プラクティス
author: pranavkm
description: ASP.NET Core Blazor WebAssembly アプリのパフォーマンスの向上、および一般的なパフォーマンスの問題回避のためのヒント。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: f7bd0d356030e6ddb95c77d7376995320e3ec40e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401884"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="d355b-103">ASP.NET Core Blazor WebAssembly パフォーマンスに関するベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="d355b-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="d355b-104">作成者: [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="d355b-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d355b-105">この記事では、ASP.NET Core Blazor WebAssembly のパフォーマンスに関するベスト プラクティスのガイドラインを示します。</span><span class="sxs-lookup"><span data-stu-id="d355b-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="d355b-106">不要なコンポーネントのレンダリングを回避する</span><span class="sxs-lookup"><span data-stu-id="d355b-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="d355b-107"> の差分検出アルゴリズムでは、コンポーネントが変更されていないことがアルゴリズムで認識された場合に、コンポーネントの再レンダリングが回避されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="d355b-108">コンポーネントのレンダリングを細かく制御するために、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="d355b-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="d355b-109">初回のレンダリング後に変更されることがない、UI のみのコンポーネントを作成する場合は、`false` を返すように <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を構成します。</span><span class="sxs-lookup"><span data-stu-id="d355b-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="d355b-110">ほとんどのアプリでは細かい制御は必要ありませんが、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用すると、UI イベントに応答するコンポーネントを選択的にレンダリングできます。</span><span class="sxs-lookup"><span data-stu-id="d355b-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="d355b-111">多数のコンポーネントがレンダリングされるシナリオでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用することが重要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="d355b-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="d355b-112">1 つのグリッドについて考えてみます。グリッドの 1 つのセルにある 1 つのコンポーネントで <xref:Microsoft.AspNetCore.Components.EventCallback> を使用すると、グリッドに対して <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="d355b-113"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、すべての子コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d355b-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="d355b-114">再レンダリングが必要なセルの数が少ない場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> を使用して、不要なレンダリングによってパフォーマンスが低下しないようにします。</span><span class="sxs-lookup"><span data-stu-id="d355b-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="d355b-115">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d355b-115">In the following example:</span></span>

* <span data-ttu-id="d355b-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされ、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> フィールドの値に設定されます。これは、コンポーネントが読み込まれる時点では、最初は `false` です。</span><span class="sxs-lookup"><span data-stu-id="d355b-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="d355b-117">ボタンが選択されると、<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> が `true` に設定されます。これにより、コンポーネントが更新された `currentCount` で強制的に再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="d355b-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="d355b-118">再レンダリングの直後、次回ボタンが選択されるまでこれ以上の再レンダリングを回避するため、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> によって <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> の値が `false` に戻されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

<span data-ttu-id="d355b-119">詳細については、「<xref:blazor/components/lifecycle#after-component-render>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="d355b-120">再利用可能なフラグメントを仮想化する</span><span class="sxs-lookup"><span data-stu-id="d355b-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="d355b-121">コンポーネントでは、コードとマークアップの再利用可能なフラグメントを生成するための便利なアプローチが提供されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="d355b-122">一般的には、アプリの要件に最適な個別のコンポーネントを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d355b-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="d355b-123">注意点の 1 つは、追加の各子コンポーネントが親コンポーネントのレンダリングにかかる合計時間に影響するということです。</span><span class="sxs-lookup"><span data-stu-id="d355b-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="d355b-124">ほとんどのアプリでは、追加のオーバーヘッドはごくわずかです。</span><span class="sxs-lookup"><span data-stu-id="d355b-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="d355b-125">多数のコンポーネントを生成するアプリでは、レンダリングするコンポーネントの数を制限するなど、処理のオーバーヘッドを減らすための方法の使用を検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d355b-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="d355b-126">たとえば、コンポーネントを含む数百の行をレンダリングするグリッドまたはリストでは、レンダリングにプロセッサが集中的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="d355b-127">コンポーネントのサブセットのみが特定の時点でレンダリングされるように、グリッドまたはリストのレイアウトを仮想化することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="d355b-128">コンポーネントのサブセットのレンダリングの例については、[`Virtualization` サンプル アプリ (aspnet/samples GitHub リポジトリ)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization) にある次のコンポーネントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="d355b-129">`Virtualize` コンポーネント ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): ユーザーのスクロールに基づいて一連の気象データ行をレンダリングするために <xref:Microsoft.AspNetCore.Components.ComponentBase> を実装する、C# で記述されたコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="d355b-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="d355b-130">`FetchData` コンポーネント ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): 一度に 25 行の気象データを表示するために、`Virtualize` コンポーネントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="d355b-131">データをマーシャリングするための JavaScript 相互運用を回避する</span><span class="sxs-lookup"><span data-stu-id="d355b-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="d355b-132">Blazor WebAssembly では、JavaScript (JS) 相互運用呼び出しは WebAssembly と JS の境界を走査する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d355b-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="d355b-133">2 つのコンテキストにわたってコンテンツをシリアル化または逆シリアル化すると、アプリの処理オーバーヘッドが発生します。</span><span class="sxs-lookup"><span data-stu-id="d355b-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="d355b-134">多くの場合、JS 相互運用呼び出しを頻繁に行うとパフォーマンスに悪影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="d355b-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="d355b-135">境界を越えたデータのマーシャリングを減らすには、アプリによって多数の小さなペイロードを 1 つの大きなペイロードに統合できるかどうかを判断して、WebAssembly と JS の間でコンテキストを頻繁に切り替えることを回避します。</span><span class="sxs-lookup"><span data-stu-id="d355b-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="d355b-136">System.Text.Json を使用する</span><span class="sxs-lookup"><span data-stu-id="d355b-136">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="d355b-137"> の JS 相互運用の実装は <xref:System.Text.Json> に依存します。これは、メモリ割り当てが少ない高パフォーマンスの JSON シリアル化ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="d355b-137">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="d355b-138"><xref:System.Text.Json> を使用しても、1 つ以上の代替 JSON ライブラリを追加したときにアプリのペイロードのサイズは増加しません。</span><span class="sxs-lookup"><span data-stu-id="d355b-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="d355b-139">移行のガイダンスについては、「[`Newtonsoft.Json` から `System.Text.Json` に移行する方法](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="d355b-140">必要に応じて、同期およびマーシャリング解除された JS 相互運用 API を使用する</span><span class="sxs-lookup"><span data-stu-id="d355b-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor WebAssembly<span data-ttu-id="d355b-141"> では、Blazor Server アプリで使用できる 1 つのバージョンに対して、<xref:Microsoft.JSInterop.IJSRuntime> の 2 つの追加バージョンが提供されています。</span><span class="sxs-lookup"><span data-stu-id="d355b-141"> offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="d355b-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> を使用して、JS 相互運用呼び出しを同期的に呼び出すことができます。この場合、非同期バージョンよりもオーバーヘッドが少なくなります。</span><span class="sxs-lookup"><span data-stu-id="d355b-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <span data-ttu-id="d355b-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> によって、マーシャリング解除された JS 相互運用呼び出しが許可されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > <span data-ttu-id="d355b-144"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> を使用すると JS 相互運用アプローチのオーバーヘッドが最小限になりますが、これらの API とのやり取りに必要な JavaScript API は現在ドキュメントに記載されていません。また、今後のリリースでの破壊的変更の対象となることがあります。</span><span class="sxs-lookup"><span data-stu-id="d355b-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="d355b-145">アプリのサイズを小さくする</span><span class="sxs-lookup"><span data-stu-id="d355b-145">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="d355b-146">中間言語 (IL) のリンク</span><span class="sxs-lookup"><span data-stu-id="d355b-146">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="d355b-147">[Blazor WebAssembly アプリをリンク](xref:blazor/host-and-deploy/configure-linker)すると、アプリのバイナリで使用されていないコードをトリミングすることで、アプリのサイズが縮小されます。</span><span class="sxs-lookup"><span data-stu-id="d355b-147">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="d355b-148">既定では、リンカーは `Release` 構成でビルドする場合にのみ有効になります。</span><span class="sxs-lookup"><span data-stu-id="d355b-148">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="d355b-149">これを活用するには、[-c|--configuration](/dotnet/core/tools/dotnet-publish#options) オプションを `Release` に設定した状態で [`dotnet publish`](/dotnet/core/tools/dotnet-publish) コマンドを使用して、展開用にアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="d355b-149">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="compression"></a><span data-ttu-id="d355b-150">[圧縮]</span><span class="sxs-lookup"><span data-stu-id="d355b-150">Compression</span></span>

<span data-ttu-id="d355b-151">Blazor WebAssembly アプリが公開されると、公開中に出力が静的に圧縮されてアプリのサイズが縮小され、実行時の圧縮に必要なオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="d355b-151">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> Blazor<span data-ttu-id="d355b-152"> は、コンテンツ ネゴシエーションの実行および静的に圧縮されたファイルの提供に関して、サーバーに依存します。</span><span class="sxs-lookup"><span data-stu-id="d355b-152"> relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="d355b-153">アプリが展開されたら、アプリが圧縮ファイルを提供していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d355b-153">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="d355b-154">ブラウザーの開発者ツールの [ネットワーク] タブを調べ、ファイルが `Content-Encoding: br` または `Content-Encoding: gz` で提供されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d355b-154">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="d355b-155">ホストが圧縮ファイルを提供していない場合は、「<xref:blazor/host-and-deploy/webassembly#compression>」に記載されている手順に従ってください。</span><span class="sxs-lookup"><span data-stu-id="d355b-155">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="d355b-156">未使用の機能を無効にする</span><span class="sxs-lookup"><span data-stu-id="d355b-156">Disable unused features</span></span>

Blazor WebAssembly<span data-ttu-id="d355b-157"> のランタイムには、次の .NET 機能が含まれています。これらの機能はアプリで必要がない場合、ペイロード サイズを小さくするために無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="d355b-157">'s runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="d355b-158">データ ファイルは、タイムゾーン情報を正しく保つために含まれています。</span><span class="sxs-lookup"><span data-stu-id="d355b-158">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="d355b-159">アプリでこの機能を必要としない場合は、アプリのプロジェクト ファイル内の `BlazorEnableTimeZoneSupport` MSBuild プロパティを `false` に設定して無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-159">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="d355b-160"><xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> などの API を正常に動作させるために、照合順序情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d355b-160">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="d355b-161">アプリで照合順序情報を必要としないことが確実な場合は、アプリのプロジェクト ファイル内の `BlazorWebAssemblyPreserveCollationData` MSBuild プロパティを `false` に設定して無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="d355b-161">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
