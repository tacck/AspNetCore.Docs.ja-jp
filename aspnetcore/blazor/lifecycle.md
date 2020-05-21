---
title: ASP.NET Core Blazor ライフサイクル
author: guardrex
description: ASP.NET Core Blazor アプリで Razor コンポーネント ライフサイクル メソッドを使用する方法について学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e4fcd86b6e6a84d9e34a83688f9fb80c6907e5f3
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438916"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="91ffe-103">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="91ffe-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="91ffe-104">著者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="91ffe-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="91ffe-105">Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="91ffe-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="91ffe-106">コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="91ffe-107">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="91ffe-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="91ffe-108">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="91ffe-108">Component initialization methods</span></span>

<span data-ttu-id="91ffe-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、その親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="91ffe-110">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、`OnInitializedAsync` を使用します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="91ffe-111">同期操作の場合は、`OnInitialized` をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="91ffe-112">非同期操作を実行するには、`OnInitializedAsync` をオーバーライドし、操作で `await` キーワードを使用します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="91ffe-113">[コンテンツをプリレンダリングする ](xref:blazor/hosting-model-configuration#render-mode)Blazor サーバー アプリは、`OnInitializedAsync` を "**_2 回_**" 呼び出します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-113">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="91ffe-114">コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。</span><span class="sxs-lookup"><span data-stu-id="91ffe-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="91ffe-115">ブラウザーがサーバーへの接続を確立するときに 2 回目。</span><span class="sxs-lookup"><span data-stu-id="91ffe-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="91ffe-116">`OnInitializedAsync` 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="91ffe-117">Blazor サーバー アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="91ffe-118">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="91ffe-119">詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="91ffe-120">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91ffe-121">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="91ffe-122">パラメーターが設定される前</span><span class="sxs-lookup"><span data-stu-id="91ffe-122">Before parameters are set</span></span>

<span data-ttu-id="91ffe-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> は、レンダリング ツリーのコンポーネントの親によって指定されたパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="91ffe-124"><xref:Microsoft.AspNetCore.Components.ParameterView> には、`SetParametersAsync` が呼び出されるたびに、パラメーター値のセット全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="91ffe-125">`SetParametersAsync` の既定の実装では、対応する値が `ParameterView` 内にある `[Parameter]` または `[CascadingParameter]` 属性を使用して、各プロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="91ffe-126">対応する値が `ParameterView` 内にないパラメーターは、変更されないままになります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="91ffe-127">`base.SetParametersAync` が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="91ffe-128">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="91ffe-129">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91ffe-130">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="91ffe-131">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="91ffe-131">After parameters are set</span></span>

<span data-ttu-id="91ffe-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="91ffe-133">コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。</span><span class="sxs-lookup"><span data-stu-id="91ffe-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="91ffe-134">親コンポーネントが再レンダリングし、次のものを提供するとき:</span><span class="sxs-lookup"><span data-stu-id="91ffe-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="91ffe-135">少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。</span><span class="sxs-lookup"><span data-stu-id="91ffe-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="91ffe-136">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="91ffe-136">Any complex-typed parameters.</span></span> <span data-ttu-id="91ffe-137">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="91ffe-138">パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクル イベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="91ffe-139">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91ffe-140">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="91ffe-141">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="91ffe-141">After component render</span></span>

<span data-ttu-id="91ffe-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="91ffe-143">この時点で、要素およびコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="91ffe-144">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="91ffe-145">`OnAfterRenderAsync` と `OnAfterRender` の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="91ffe-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="91ffe-146">コンポーネント インスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="91ffe-147">初期化作業が確実に 1 回だけ実行されるように使用できます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="91ffe-148">`OnAfterRenderAsync` ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="91ffe-149">`OnAfterRenderAsync` から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="91ffe-150">これは、無限のレンダリング ループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="91ffe-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="91ffe-151">返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="91ffe-152">`OnAfterRender` と `OnAfterRenderAsync` は、"*サーバー上でプリレンダリングするときには呼び出されません。* "</span><span class="sxs-lookup"><span data-stu-id="91ffe-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="91ffe-153">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91ffe-154">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="91ffe-155">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="91ffe-155">Suppress UI refreshing</span></span>

<span data-ttu-id="91ffe-156"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="91ffe-157">実装によって `true` が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="91ffe-158">`ShouldRender` は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="91ffe-159">`ShouldRender` がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="91ffe-160">詳細については、「<xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-160">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="91ffe-161">状態変更</span><span class="sxs-lookup"><span data-stu-id="91ffe-161">State changes</span></span>

<span data-ttu-id="91ffe-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="91ffe-163">必要に応じて、`StateHasChanged` を呼び出すと、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-163">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="91ffe-164">レンダリング時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="91ffe-164">Handle incomplete async actions at render</span></span>

<span data-ttu-id="91ffe-165">ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-165">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="91ffe-166">ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-166">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="91ffe-167">オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-167">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="91ffe-168">オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-168">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="91ffe-169">Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` はオーバーライドされ、予測データを非同期に受信します (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="91ffe-169">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="91ffe-170">`forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-170">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="91ffe-171">`OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-171">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="91ffe-172">Blazor サーバー テンプレート内の *Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="91ffe-172">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="91ffe-173">IDisposable を使用したコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="91ffe-173">Component disposal with IDisposable</span></span>

<span data-ttu-id="91ffe-174">コンポーネントが <xref:System.IDisposable> を実装している場合は、コンポーネントが UI から削除されると、[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-174">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="91ffe-175">次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-175">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="91ffe-176">`Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-176">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="91ffe-177">`StateHasChanged` は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-177">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="91ffe-178">.NET イベントからイベント ハンドラーのサブスクライブを解除します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-178">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="91ffe-179">次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーをアンフックする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="91ffe-179">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="91ffe-180">プライベート フィールドとラムダのアプローチ</span><span class="sxs-lookup"><span data-stu-id="91ffe-180">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="91ffe-181">プライベート メソッドのアプローチ</span><span class="sxs-lookup"><span data-stu-id="91ffe-181">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="91ffe-182">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="91ffe-182">Handle errors</span></span>

<span data-ttu-id="91ffe-183">ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-183">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="91ffe-184">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="91ffe-184">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="91ffe-185">Blazor サーバー アプリで `RenderMode` が `ServerPrerendered` の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-185">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="91ffe-186">ブラウザーがサーバーへの接続を確立すると、コンポーネントが "*再度*" レンダリングされ、コンポーネントがやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-186">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="91ffe-187">コンポーネントを初期化するための [OnInitialized{Async}](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは "*2 回*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="91ffe-187">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="91ffe-188">コンポーネントが静的にプリレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="91ffe-188">When the component is prerendered statically.</span></span>
* <span data-ttu-id="91ffe-189">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="91ffe-189">After the server connection has been established.</span></span>

<span data-ttu-id="91ffe-190">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-190">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="91ffe-191">Blazor サーバー アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="91ffe-191">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="91ffe-192">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-192">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="91ffe-193">識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-193">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="91ffe-194">識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-194">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="91ffe-195">次のコードは、二重レンダリングを回避するテンプレートベースの Blazor サーバー アプリ内で更新される `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="91ffe-195">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="91ffe-196">`RenderMode` の詳細については、「<xref:blazor/hosting-model-configuration#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91ffe-196">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="91ffe-197">アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="91ffe-197">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="91ffe-198">取り消し可能なバックグラウンド作業</span><span class="sxs-lookup"><span data-stu-id="91ffe-198">Cancelable background work</span></span>

<span data-ttu-id="91ffe-199">ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-199">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="91ffe-200">いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="91ffe-200">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="91ffe-201">たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。</span><span class="sxs-lookup"><span data-stu-id="91ffe-201">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="91ffe-202">バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-202">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="91ffe-203">実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="91ffe-203">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="91ffe-204">現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-204">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="91ffe-205">現在実行中のタスクの優先度を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-205">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="91ffe-206">アプリをサーバーに再展開するには、シャットダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="91ffe-206">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="91ffe-207">サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="91ffe-207">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="91ffe-208">コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:</span><span class="sxs-lookup"><span data-stu-id="91ffe-208">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="91ffe-209"><xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-209">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="91ffe-210">[コンポーネントの破棄](#component-disposal-with-idisposable)時と、任意の時点で手動でトークンを取り消すことで取り消しが望まれた場合は、[CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) を呼び出して、バックグラウンド作業を取り消す必要があることを通知します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-210">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="91ffe-211">非同期呼び出しが返された後、トークンに対して <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-211">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="91ffe-212">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-212">In the following example:</span></span>

* <span data-ttu-id="91ffe-213">`await Task.Delay(5000, cts.Token);` は、実行時間が長い非同期のバックグラウンド作業を表します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-213">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="91ffe-214">`BackgroundResourceMethod` は、メソッドが呼び出される前に `Resource` が破棄された場合に開始されない、実行時間が長いバックグラウンド メソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="91ffe-214">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
