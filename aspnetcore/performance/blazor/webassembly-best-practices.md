---
title: Blazorパフォーマンスのベストプラクティスを ASP.NET Core
author: pranavkm
description: ASP.NET Core アプリのパフォーマンスを向上させ Blazor 、一般的なパフォーマンスの問題を回避するためのヒントです。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/blazor/webassembly-best-practices
ms.openlocfilehash: 9e9b166cb9ce9870a8ff275b72bb12f04b84751b
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2020
ms.locfileid: "83439423"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="dbcab-103">Blazorパフォーマンスのベストプラクティスを ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbcab-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="dbcab-104">[Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="dbcab-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="dbcab-105">この記事では、パフォーマンスのベストプラクティス ASP.NET Core に関するガイドラインを提供 Blazor します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="dbcab-106">不要なコンポーネントのレンダリングを回避する</span><span class="sxs-lookup"><span data-stu-id="dbcab-106">Avoid unnecessary component renders</span></span>

Blazor<span data-ttu-id="dbcab-107">の比較アルゴリズムは、コンポーネントが変更されていないことをアルゴリズムが認識した場合に、コンポーネントの更新を回避します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-107">'s diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="dbcab-108">[Componentbase をオーバーライドします。](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A)コンポーネントのレンダリングをきめ細かく制御するためにレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="dbcab-108">Override [ComponentBase.ShouldRender](xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A) for fine-grained control over component rendering.</span></span>

<span data-ttu-id="dbcab-109">初期レンダリング後に変更されることがない UI のみのコンポーネントを作成する場合は、を次のように構成し `ShouldRender` `false` ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-109">If authoring a UI-only component that never changes after the initial render, configure `ShouldRender` to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="dbcab-110">ほとんどのアプリでは細かい制御は必要ありませんが、 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> UI イベントに応答するコンポーネントを選択的に表示するために使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can also be used to selectively render a component responding to a UI event.</span></span>

<span data-ttu-id="dbcab-111">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-111">In the following example:</span></span>

* <span data-ttu-id="dbcab-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>はオーバーライドされ、 `shouldRender` 最初はコンポーネントが読み込まれるときのフィールドの値に設定され `false` ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-112"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the `shouldRender` field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="dbcab-113">このボタンが選択されている場合、 `shouldRender` はに設定され `true` ます。これにより、コンポーネントは、更新されたに強制的にレンダリングされ `currentCount` ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-113">When the button is selected, `shouldRender` is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="dbcab-114">再リリースの直後に、 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の値を `shouldRender` に戻して、 `false` 次回ボタンが選択されるまで再実行が行われないようにします。</span><span class="sxs-lookup"><span data-stu-id="dbcab-114">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of `shouldRender` back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="dbcab-115">詳細については、「<xref:blazor/lifecycle#after-component-render>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dbcab-115">For more information, see <xref:blazor/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="dbcab-116">再利用可能なフラグメントを仮想化する</span><span class="sxs-lookup"><span data-stu-id="dbcab-116">Virtualize re-usable fragments</span></span>

<span data-ttu-id="dbcab-117">コンポーネントは、コードとマークアップの再利用可能なフラグメントを生成するための便利なアプローチを提供します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-117">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="dbcab-118">一般に、アプリの要件に最適な個々のコンポーネントを作成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dbcab-118">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="dbcab-119">注意点の1つは、追加の子コンポーネントが親コンポーネントのレンダリングにかかる合計時間に影響することです。</span><span class="sxs-lookup"><span data-stu-id="dbcab-119">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="dbcab-120">ほとんどのアプリでは、追加のオーバーヘッドはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="dbcab-120">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="dbcab-121">多数のコンポーネントを生成するアプリでは、表示するコンポーネントの数を制限するなど、処理のオーバーヘッドを減らすために戦略を使用することを検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dbcab-121">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="dbcab-122">たとえば、コンポーネントを含む数百の行を表示するグリッドまたはリストは、レンダリングにプロセッサを使用します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-122">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="dbcab-123">コンポーネントのサブセットのみが特定の時点でレンダリングされるように、グリッドまたはリストのレイアウトを仮想化することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="dbcab-123">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="dbcab-124">コンポーネントのサブセットレンダリングの例については、[仮想化サンプルアプリの次のコンポーネント (aspnet/Samples GitHub リポジトリ)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dbcab-124">For an example of component subset rendering, see the following components in the [Virtualization sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="dbcab-125">`Virtualize`コンポーネント ([共有/仮想](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)化): <xref:Microsoft.AspNetCore.Components.ComponentBase> ユーザーのスクロールに基づいて一連の天気データ行を表示するためにを実装する、C# で記述されたコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="dbcab-125">`Virtualize` component ([Shared/Virtualize.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="dbcab-126">`FetchData`コンポーネント ([Pages/fetchdata. razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): コンポーネントを使用して `Virtualize` 、一度に25行の気象データを表示します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-126">`FetchData` component ([Pages/FetchData.razor](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="dbcab-127">データをマーシャリングするための JavaScript の相互運用を回避する</span><span class="sxs-lookup"><span data-stu-id="dbcab-127">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="dbcab-128">BlazorWebassembly は、JavaScript (JS) 相互運用呼び出しは webassembly 境界をたどる必要があります。</span><span class="sxs-lookup"><span data-stu-id="dbcab-128">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="dbcab-129">2つのコンテキストでコンテンツをシリアル化または逆シリアル化すると、アプリの処理オーバーヘッドが発生します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-129">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="dbcab-130">多くの場合、JS 相互運用の呼び出しがパフォーマンスに悪影響を与えることがあります。</span><span class="sxs-lookup"><span data-stu-id="dbcab-130">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="dbcab-131">境界を越えたデータのマーシャリングを減らすには、アプリが多数の小さなペイロードを1つの大きなペイロードに統合できるかどうかを判断して、WebAssembly JS の間でコンテキストを頻繁に切り替えることがないようにします。</span><span class="sxs-lookup"><span data-stu-id="dbcab-131">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="dbcab-132">System.string を使用する</span><span class="sxs-lookup"><span data-stu-id="dbcab-132">Use System.Text.Json</span></span>

Blazor<span data-ttu-id="dbcab-133">の JS 相互運用機能の実装 <xref:System.Text.Json> は、メモリ割り当てが少ない高パフォーマンスの JSON シリアル化ライブラリであるに依存しています。</span><span class="sxs-lookup"><span data-stu-id="dbcab-133">'s JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="dbcab-134">を使用して <xref:System.Text.Json> も、1つまたは複数の代替 JSON ライブラリを追加しても、アプリペイロードのサイズは追加されません。</span><span class="sxs-lookup"><span data-stu-id="dbcab-134">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="dbcab-135">移行のガイダンスについては、「 [Newtonsoft. json から system.string に移行する方法](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dbcab-135">For migration guidance, see [How to migrate from Newtonsoft.Json to System.Text.Json](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="dbcab-136">必要に応じて、同期およびマーシャリング解除された JS 相互運用 Api を使用する</span><span class="sxs-lookup"><span data-stu-id="dbcab-136">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

Blazor<span data-ttu-id="dbcab-137"><xref:Microsoft.JSInterop.IJSRuntime>サーバーアプリで利用可能な1つのバージョンでは、2つの追加バージョンのが提供されてい Blazor ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-137"> WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="dbcab-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime>JS 相互運用呼び出しを同期的に呼び出すことができます。この場合、非同期バージョンよりもオーバーヘッドが少なくなります。</span><span class="sxs-lookup"><span data-stu-id="dbcab-138"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="dbcab-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime>マーシャリング解除した JS 相互運用呼び出しを許可します。</span><span class="sxs-lookup"><span data-stu-id="dbcab-139"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="dbcab-140">を使用すると、 <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> JS 相互運用アプローチのオーバーヘッドが最小限になりますが、これらの api を操作するために必要な JavaScript api は現在ドキュメントに記載されていません。将来のリリースでは、互換性に影響する変更の対象となります。</span><span class="sxs-lookup"><span data-stu-id="dbcab-140">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="dbcab-141">アプリのサイズを小さくする</span><span class="sxs-lookup"><span data-stu-id="dbcab-141">Reduce app size</span></span>

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="dbcab-142">中間言語 (IL) リンク</span><span class="sxs-lookup"><span data-stu-id="dbcab-142">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="dbcab-143">[リンク Blazorアプリの](xref:host-and-deploy/blazor/configure-linker)バイナリで使用されていないコードをトリミングすることで、アプリのサイズを縮小できます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-143">[Linking a Blazor WebAssembly app](xref:host-and-deploy/blazor/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="dbcab-144">既定では、リンカーは、構成でビルドするときにのみ有効になり `Release` ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-144">By default, the linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="dbcab-145">これを活用するには、 [dotnet publish](/dotnet/core/tools/dotnet-publish)コマンドを使用して、 [-c |--構成](/dotnet/core/tools/dotnet-publish#options)オプションをに設定して、デプロイ用にアプリを発行し `Release` ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-145">To benefit from this, publish the app for deployment using the [dotnet publish](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

```dotnetcli
dotnet publish -c Release
```

### <a name="disable-unused-features"></a><span data-ttu-id="dbcab-146">使用されていない機能を無効にする</span><span class="sxs-lookup"><span data-stu-id="dbcab-146">Disable unused features</span></span>

Blazor<span data-ttu-id="dbcab-147">WebAssembly ランタイムには、次の .NET 機能が含まれています。これらの機能は、アプリがペイロードサイズを小さくする必要がない場合に無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-147"> WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="dbcab-148">データファイルは、タイムゾーン情報を修正するために含まれています。</span><span class="sxs-lookup"><span data-stu-id="dbcab-148">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="dbcab-149">アプリがこの機能を必要としない場合は、 `BlazorEnableTimeZoneSupport` アプリのプロジェクトファイルの MSBuild プロパティを次のように設定して無効にすることを検討してください `false` 。</span><span class="sxs-lookup"><span data-stu-id="dbcab-149">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

* <span data-ttu-id="dbcab-150">照合順序情報は、などの Api を正常に動作させるために含まれてい <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="dbcab-150">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="dbcab-151">アプリが照合順序データを必要としないことがわかっている場合は、 `BlazorWebAssemblyPreserveCollationData` アプリのプロジェクトファイルの MSBuild プロパティを次のように設定して無効にすることを検討してください `false` 。</span><span class="sxs-lookup"><span data-stu-id="dbcab-151">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```
