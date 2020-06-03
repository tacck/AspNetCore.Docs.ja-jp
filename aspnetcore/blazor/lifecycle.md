---
<span data-ttu-id="a4a05-101">title:'ASP.NET Core Blazor ライフサイクル' author: description:'ASP.NET Core Blazor アプリで Razor コンポーネント ライフサイクル メソッドを使用する方法について学習します。'</span><span class="sxs-lookup"><span data-stu-id="a4a05-101">title: 'ASP.NET Core Blazor lifecycle' author: description: 'Learn how to use Razor component lifecycle methods in ASP.NET Core Blazor apps.'</span></span>
<span data-ttu-id="a4a05-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a4a05-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a4a05-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a4a05-103">'Blazor'</span></span>
- <span data-ttu-id="a4a05-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a4a05-104">'Identity'</span></span>
- <span data-ttu-id="a4a05-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a4a05-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a4a05-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a4a05-106">'Razor'</span></span>
- <span data-ttu-id="a4a05-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a4a05-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="a4a05-108">ASP.NET Core Blazor ライフサイクル</span><span class="sxs-lookup"><span data-stu-id="a4a05-108">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="a4a05-109">著者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a4a05-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a4a05-110">Blazor フレームワークには、同期と非同期のライフサイクル メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a4a05-110">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="a4a05-111">コンポーネントの初期化およびレンダリング中にコンポーネントで追加の操作を実行するには、ライフサイクル メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-111">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="a4a05-112">ライフサイクル メソッド</span><span class="sxs-lookup"><span data-stu-id="a4a05-112">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="a4a05-113">コンポーネントの初期化メソッド</span><span class="sxs-lookup"><span data-stu-id="a4a05-113">Component initialization methods</span></span>

<span data-ttu-id="a4a05-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> は、コンポーネントが、その親コンポーネントから初期パラメーターを受け取った後で初期化されるときに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-114"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="a4a05-115">コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-115">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="a4a05-116">同期操作の場合は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-116">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="a4a05-117">非同期操作を実行するには、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> をオーバーライドし、操作で [await](/dotnet/csharp/language-reference/operators/await) 演算子を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-117">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [await](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="a4a05-118">[コンテンツをプリレンダリングする ](xref:blazor/hosting-model-configuration#render-mode)Blazor サーバー アプリは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> を "**_2 回_**" 呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-118">Blazor Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="a4a05-119">コンポーネントが最初にページの一部として静的にレンダリングされるときに 1 回。</span><span class="sxs-lookup"><span data-stu-id="a4a05-119">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="a4a05-120">ブラウザーがサーバーへの接続を確立するときに 2 回目。</span><span class="sxs-lookup"><span data-stu-id="a4a05-120">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="a4a05-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 内で開発者コードが 2 回実行されないようにするには、「[プリレンダリング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-121">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="a4a05-122">Blazor サーバー アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-122">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="a4a05-123">コンポーネントは、プリレンダリング時に異なるレンダリングが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-123">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="a4a05-124">詳細については、「[アプリがプリレンダリングされていることを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-124">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="a4a05-125">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-125">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="a4a05-126">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-126">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="a4a05-127">パラメーターが設定される前</span><span class="sxs-lookup"><span data-stu-id="a4a05-127">Before parameters are set</span></span>

<span data-ttu-id="a4a05-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> は、レンダリング ツリーのコンポーネントの親によって指定されたパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="a4a05-129"><xref:Microsoft.AspNetCore.Components.ParameterView> には、<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> が呼び出されるたびに、パラメーター値のセット全体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-129"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="a4a05-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> の既定の実装では、対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にある [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) または [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用して、各プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-130">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="a4a05-131">対応する値が <xref:Microsoft.AspNetCore.Components.ParameterView> 内にないパラメーターは、変更されないままになります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-131">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="a4a05-132">[base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) が呼び出されない場合、カスタム コードでは、必要に応じて受信パラメーター値を解釈できます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-132">If [base.SetParametersAync](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="a4a05-133">たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-133">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="a4a05-134">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-134">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="a4a05-135">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-135">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="a4a05-136">パラメーターが設定された後</span><span class="sxs-lookup"><span data-stu-id="a4a05-136">After parameters are set</span></span>

<span data-ttu-id="a4a05-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-137"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="a4a05-138">コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。</span><span class="sxs-lookup"><span data-stu-id="a4a05-138">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="a4a05-139">親コンポーネントが再レンダリングし、次のものを提供するとき:</span><span class="sxs-lookup"><span data-stu-id="a4a05-139">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="a4a05-140">少なくとも 1 つのパラメーターが変更された既知のプリミティブ不変型のみ。</span><span class="sxs-lookup"><span data-stu-id="a4a05-140">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="a4a05-141">任意の複合型のパラメーター。</span><span class="sxs-lookup"><span data-stu-id="a4a05-141">Any complex-typed parameters.</span></span> <span data-ttu-id="a4a05-142">フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーター セットは変更済みとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-142">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="a4a05-143">パラメーターとプロパティ値を適用するときの非同期処理は、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> ライフサイクル イベント中に発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-143">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="a4a05-144">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-144">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="a4a05-145">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-145">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="a4a05-146">コンポーネントのレンダリング後</span><span class="sxs-lookup"><span data-stu-id="a4a05-146">After component render</span></span>

<span data-ttu-id="a4a05-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> および <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> は、コンポーネントのレンダリングが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="a4a05-148">この時点で、要素およびコンポーネント参照が設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-148">Element and component references are populated at this point.</span></span> <span data-ttu-id="a4a05-149">レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を行うには、この段階を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-149">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="a4a05-150"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> の `firstRender` パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a4a05-150">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="a4a05-151">コンポーネント インスタンスを初めて表示するときに `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-151">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="a4a05-152">初期化作業が確実に 1 回だけ実行されるように使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-152">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="a4a05-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> ライフサイクル イベント中に、レンダリング直後の非同期作業が発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-153">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="a4a05-154"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークでは、そのタスクが完了しても、コンポーネントに対してさらにレンダリング サイクルがスケジュールされることはありません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-154">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="a4a05-155">これは、無限のレンダリング ループを回避するためです。</span><span class="sxs-lookup"><span data-stu-id="a4a05-155">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="a4a05-156">返されたタスクが完了すると、さらにレンダリング サイクルをスケジュールする他のライフサイクル メソッドとは異なります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-156">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="a4a05-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> は、"*サーバー上でプリレンダリングするときには呼び出されません。* "</span><span class="sxs-lookup"><span data-stu-id="a4a05-157"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="a4a05-158">イベント ハンドラーが設定されている場合は、破棄時にそれらをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-158">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="a4a05-159">詳細については、「[IDisposable を使用したコンポーネントの破棄](#component-disposal-with-idisposable)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-159">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="a4a05-160">UI 更新の抑制</span><span class="sxs-lookup"><span data-stu-id="a4a05-160">Suppress UI refreshing</span></span>

<span data-ttu-id="a4a05-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> をオーバーライドして、UI の更新を抑制します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-161">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="a4a05-162">実装によって `true` が返された場合は、UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-162">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="a4a05-163"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> は、コンポーネントがレンダリングされるたびに呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-163"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="a4a05-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> がオーバーライドされる場合でも、コンポーネントは常に最初にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-164">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="a4a05-165">詳細については、「<xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-165">For more information, see <xref:performance/blazor/webassembly-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="a4a05-166">状態変更</span><span class="sxs-lookup"><span data-stu-id="a4a05-166">State changes</span></span>

<span data-ttu-id="a4a05-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、状態が変更されたことをコンポーネントに通知します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-167"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="a4a05-168">必要に応じて、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> を呼び出すと、コンポーネントが再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-168">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="a4a05-169">レンダリング時の不完全な非同期アクションを処理する</span><span class="sxs-lookup"><span data-stu-id="a4a05-169">Handle incomplete async actions at render</span></span>

<span data-ttu-id="a4a05-170">ライフサイクル イベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-170">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="a4a05-171">ライフサイクル メソッドの実行中に、オブジェクトが `null` またはデータが不完全に設定されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-171">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="a4a05-172">オブジェクトが初期化されていることを確認するレンダリング ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-172">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="a4a05-173">オブジェクトが `null` の間、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-173">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="a4a05-174">Blazor テンプレートの `FetchData` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> はオーバーライドされ、予測データを非同期に受信します (`forecasts`)。</span><span class="sxs-lookup"><span data-stu-id="a4a05-174">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="a4a05-175">`forecasts` が `null` の場合、読み込みメッセージがユーザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-175">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="a4a05-176"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> によって返された `Task` が完了すると、コンポーネントは更新された状態で再レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-176">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="a4a05-177">Blazor サーバー テンプレート内の *Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="a4a05-177">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="a4a05-178">IDisposable を使用したコンポーネントの破棄</span><span class="sxs-lookup"><span data-stu-id="a4a05-178">Component disposal with IDisposable</span></span>

<span data-ttu-id="a4a05-179">コンポーネントが <xref:System.IDisposable> を実装している場合は、コンポーネントが UI から削除されると、[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-179">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="a4a05-180">次のコンポーネントでは、`@implements IDisposable` および `Dispose` メソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-180">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="a4a05-181">`Dispose` では、<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> の呼び出しはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-181">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="a4a05-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-182"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="a4a05-183">.NET イベントからイベント ハンドラーのサブスクライブを解除します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-183">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="a4a05-184">次の [Blazor フォーム](xref:blazor/forms-validation)の例は、`Dispose` メソッドでイベント ハンドラーをアンフックする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a4a05-184">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="a4a05-185">プライベート フィールドとラムダのアプローチ</span><span class="sxs-lookup"><span data-stu-id="a4a05-185">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="a4a05-186">プライベート メソッドのアプローチ</span><span class="sxs-lookup"><span data-stu-id="a4a05-186">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="a4a05-187">エラーの処理</span><span class="sxs-lookup"><span data-stu-id="a4a05-187">Handle errors</span></span>

<span data-ttu-id="a4a05-188">ライフサイクル メソッド実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-188">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="a4a05-189">プリレンダリング後のステートフル再接続</span><span class="sxs-lookup"><span data-stu-id="a4a05-189">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="a4a05-190">Blazor サーバー アプリで <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> が <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> の場合、コンポーネントは最初にページの一部として静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-190">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="a4a05-191">ブラウザーがサーバーへの接続を確立すると、コンポーネントが "*再度*" レンダリングされ、コンポーネントがやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-191">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="a4a05-192">コンポーネントを初期化するための [OnInitialized{Async}](#component-initialization-methods) ライフサイクル メソッドが存在する場合、メソッドは "*2 回*" 実行されます。</span><span class="sxs-lookup"><span data-stu-id="a4a05-192">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="a4a05-193">コンポーネントが静的にプリレンダリングされたとき。</span><span class="sxs-lookup"><span data-stu-id="a4a05-193">When the component is prerendered statically.</span></span>
* <span data-ttu-id="a4a05-194">サーバー接続が確立された後。</span><span class="sxs-lookup"><span data-stu-id="a4a05-194">After the server connection has been established.</span></span>

<span data-ttu-id="a4a05-195">これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変わる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-195">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="a4a05-196">Blazor サーバー アプリ内の二重レンダリングのシナリオを回避するには、次の手順を行います。</span><span class="sxs-lookup"><span data-stu-id="a4a05-196">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="a4a05-197">プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-197">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="a4a05-198">識別子をプリレンダリング中に使用して、コンポーネントの状態を保存します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-198">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="a4a05-199">識別子をプリレンダリング後に使用して、キャッシュされた状態を取得します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-199">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="a4a05-200">次のコードは、二重レンダリングを回避するテンプレートベースの Blazor サーバー アプリ内で更新される `WeatherForecastService` を示しています。</span><span class="sxs-lookup"><span data-stu-id="a4a05-200">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="a4a05-201"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> の詳細については、「<xref:blazor/hosting-model-configuration#render-mode>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4a05-201">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="a4a05-202">アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="a4a05-202">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="a4a05-203">取り消し可能なバックグラウンド作業</span><span class="sxs-lookup"><span data-stu-id="a4a05-203">Cancelable background work</span></span>

<span data-ttu-id="a4a05-204">ネットワーク呼び出し (<xref:System.Net.Http.HttpClient>) の実行やデータベースとの対話など、コンポーネントによって実行時間の長いバックグラウンド作業が実行されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-204">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="a4a05-205">いくつかの状況でシステム リソースを節約するために、バックグラウンド作業を停止することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a4a05-205">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="a4a05-206">たとえば、ユーザーがコンポーネントの操作を止めても、バックグラウンドの非同期操作は自動的に停止しません。</span><span class="sxs-lookup"><span data-stu-id="a4a05-206">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="a4a05-207">バックグラウンド作業項目の取り消しが必要になるその他の理由には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-207">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="a4a05-208">実行中のバックグラウンド タスクは、不完全な入力データまたは処理パラメーターを使用して開始されました。</span><span class="sxs-lookup"><span data-stu-id="a4a05-208">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="a4a05-209">現在実行中のバックグラウンド作業項目のセットを、新しい作業項目のセットに置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-209">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="a4a05-210">現在実行中のタスクの優先度を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-210">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="a4a05-211">アプリをサーバーに再展開するには、シャットダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a4a05-211">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="a4a05-212">サーバー リソースが制限され、バックグラウンド作業項目の再スケジュールが必要になりました。</span><span class="sxs-lookup"><span data-stu-id="a4a05-212">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="a4a05-213">コンポーネントに取り消し可能なバックグラウンド作業パターンを実装するには:</span><span class="sxs-lookup"><span data-stu-id="a4a05-213">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="a4a05-214"><xref:System.Threading.CancellationTokenSource> と <xref:System.Threading.CancellationToken> を使用します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-214">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="a4a05-215">[コンポーネントの破棄](#component-disposal-with-idisposable)時と、任意の時点で手動でトークンを取り消すことで取り消しが望まれた場合は、[CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) を呼び出して、バックグラウンド作業を取り消す必要があることを通知します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-215">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="a4a05-216">非同期呼び出しが返された後、トークンに対して <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-216">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="a4a05-217">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-217">In the following example:</span></span>

* <span data-ttu-id="a4a05-218">`await Task.Delay(5000, cts.Token);` は、実行時間が長い非同期のバックグラウンド作業を表します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-218">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="a4a05-219">`BackgroundResourceMethod` は、メソッドが呼び出される前に `Resource` が破棄された場合に開始されない、実行時間が長いバックグラウンド メソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="a4a05-219">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
