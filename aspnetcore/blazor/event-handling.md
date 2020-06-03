---
<span data-ttu-id="4d782-101">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-103">'Blazor'</span></span>
- <span data-ttu-id="4d782-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-104">'Identity'</span></span>
- <span data-ttu-id="4d782-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-106">'Razor'</span></span>
- <span data-ttu-id="4d782-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="4d782-108">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="4d782-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="4d782-109">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4d782-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="4d782-110"> コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-110"> components provide event handling features.</span></span> <span data-ttu-id="4d782-111">[`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="4d782-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="4d782-112">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4d782-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="4d782-113">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4d782-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="4d782-114">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="4d782-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="4d782-115">[StateHasChanged](xref:blazor/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4d782-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="4d782-116">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="4d782-117">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="4d782-118">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="4d782-118">Event argument types</span></span>

<span data-ttu-id="4d782-119">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="4d782-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="4d782-120">メソッド呼び出しでイベント型を指定する必要があるのは、そのイベント型がメソッド内で使用されている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="4d782-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="4d782-121">サポートされている <xref:System.EventArgs> を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="4d782-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="4d782-122">event</span><span class="sxs-lookup"><span data-stu-id="4d782-122">Event</span></span>            | <span data-ttu-id="4d782-123">クラス</span><span class="sxs-lookup"><span data-stu-id="4d782-123">Class</span></span>                | <span data-ttu-id="4d782-124">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="4d782-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="4d782-125">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-127">'Blazor'</span></span>
- <span data-ttu-id="4d782-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-128">'Identity'</span></span>
- <span data-ttu-id="4d782-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-130">'Razor'</span></span>
- <span data-ttu-id="4d782-131">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-132">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-134">'Blazor'</span></span>
- <span data-ttu-id="4d782-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-135">'Identity'</span></span>
- <span data-ttu-id="4d782-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-137">'Razor'</span></span>
- <span data-ttu-id="4d782-138">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-139">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-141">'Blazor'</span></span>
- <span data-ttu-id="4d782-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-142">'Identity'</span></span>
- <span data-ttu-id="4d782-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-144">'Razor'</span></span>
- <span data-ttu-id="4d782-145">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-146">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-148">'Blazor'</span></span>
- <span data-ttu-id="4d782-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-149">'Identity'</span></span>
- <span data-ttu-id="4d782-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-151">'Razor'</span></span>
- <span data-ttu-id="4d782-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-153">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-155">'Blazor'</span></span>
- <span data-ttu-id="4d782-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-156">'Identity'</span></span>
- <span data-ttu-id="4d782-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-158">'Razor'</span></span>
- <span data-ttu-id="4d782-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-160">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-162">'Blazor'</span></span>
- <span data-ttu-id="4d782-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-163">'Identity'</span></span>
- <span data-ttu-id="4d782-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-165">'Razor'</span></span>
- <span data-ttu-id="4d782-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-166">'SignalR' uid:</span></span> 

<span data-ttu-id="4d782-167">-------- | --- title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-169">'Blazor'</span></span>
- <span data-ttu-id="4d782-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-170">'Identity'</span></span>
- <span data-ttu-id="4d782-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-172">'Razor'</span></span>
- <span data-ttu-id="4d782-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-174">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-176">'Blazor'</span></span>
- <span data-ttu-id="4d782-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-177">'Identity'</span></span>
- <span data-ttu-id="4d782-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-179">'Razor'</span></span>
- <span data-ttu-id="4d782-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-181">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-183">'Blazor'</span></span>
- <span data-ttu-id="4d782-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-184">'Identity'</span></span>
- <span data-ttu-id="4d782-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-186">'Razor'</span></span>
- <span data-ttu-id="4d782-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-188">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-190">'Blazor'</span></span>
- <span data-ttu-id="4d782-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-191">'Identity'</span></span>
- <span data-ttu-id="4d782-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-193">'Razor'</span></span>
- <span data-ttu-id="4d782-194">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-195">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-197">'Blazor'</span></span>
- <span data-ttu-id="4d782-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-198">'Identity'</span></span>
- <span data-ttu-id="4d782-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-200">'Razor'</span></span>
- <span data-ttu-id="4d782-201">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-202">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-204">'Blazor'</span></span>
- <span data-ttu-id="4d782-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-205">'Identity'</span></span>
- <span data-ttu-id="4d782-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-207">'Razor'</span></span>
- <span data-ttu-id="4d782-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-209">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-211">'Blazor'</span></span>
- <span data-ttu-id="4d782-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-212">'Identity'</span></span>
- <span data-ttu-id="4d782-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-214">'Razor'</span></span>
- <span data-ttu-id="4d782-215">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-216">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-218">'Blazor'</span></span>
- <span data-ttu-id="4d782-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-219">'Identity'</span></span>
- <span data-ttu-id="4d782-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-221">'Razor'</span></span>
- <span data-ttu-id="4d782-222">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-222">'SignalR' uid:</span></span> 

<span data-ttu-id="4d782-223">---------- | --- title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-225">'Blazor'</span></span>
- <span data-ttu-id="4d782-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-226">'Identity'</span></span>
- <span data-ttu-id="4d782-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-228">'Razor'</span></span>
- <span data-ttu-id="4d782-229">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-230">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-232">'Blazor'</span></span>
- <span data-ttu-id="4d782-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-233">'Identity'</span></span>
- <span data-ttu-id="4d782-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-235">'Razor'</span></span>
- <span data-ttu-id="4d782-236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-237">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-239">'Blazor'</span></span>
- <span data-ttu-id="4d782-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-240">'Identity'</span></span>
- <span data-ttu-id="4d782-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-242">'Razor'</span></span>
- <span data-ttu-id="4d782-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-244">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-246">'Blazor'</span></span>
- <span data-ttu-id="4d782-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-247">'Identity'</span></span>
- <span data-ttu-id="4d782-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-249">'Razor'</span></span>
- <span data-ttu-id="4d782-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-251">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-253">'Blazor'</span></span>
- <span data-ttu-id="4d782-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-254">'Identity'</span></span>
- <span data-ttu-id="4d782-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-256">'Razor'</span></span>
- <span data-ttu-id="4d782-257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-258">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-260">'Blazor'</span></span>
- <span data-ttu-id="4d782-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-261">'Identity'</span></span>
- <span data-ttu-id="4d782-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-263">'Razor'</span></span>
- <span data-ttu-id="4d782-264">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-265">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-267">'Blazor'</span></span>
- <span data-ttu-id="4d782-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-268">'Identity'</span></span>
- <span data-ttu-id="4d782-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-270">'Razor'</span></span>
- <span data-ttu-id="4d782-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d782-272">title:'ASP.NET Core Blazor のイベント処理' 作成者: 説明:'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。'</span><span class="sxs-lookup"><span data-stu-id="4d782-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="4d782-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d782-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d782-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d782-274">'Blazor'</span></span>
- <span data-ttu-id="4d782-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d782-275">'Identity'</span></span>
- <span data-ttu-id="4d782-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d782-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d782-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d782-277">'Razor'</span></span>
- <span data-ttu-id="4d782-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="4d782-278">'SignalR' uid:</span></span> 

<span data-ttu-id="4d782-279">---------- | | クリップボード        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`、`oncopy`、`onpaste` | | ドラッグ             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`、`ondragstart`、`ondragenter`、`ondragleave`、`ondragover`、`ondrop`、`ondragend`</span><span class="sxs-lookup"><span data-stu-id="4d782-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="4d782-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> および <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="4d782-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="4d782-281">| | エラー            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | イベント            | <xref:System.EventArgs> | *全般*</span><span class="sxs-lookup"><span data-stu-id="4d782-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="4d782-282">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="4d782-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="4d782-283">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="4d782-283">*Clipboard*</span></span><br><span data-ttu-id="4d782-284">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="4d782-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="4d782-285">*入力*</span><span class="sxs-lookup"><span data-stu-id="4d782-285">*Input*</span></span><br><span data-ttu-id="4d782-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="4d782-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="4d782-287">*メディア*</span><span class="sxs-lookup"><span data-stu-id="4d782-287">*Media*</span></span><br><span data-ttu-id="4d782-288">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="4d782-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="4d782-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> は、イベント名とイベント引数の型の間のマッピングを構成する属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="4d782-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="4d782-290">| | フォーカス            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`、`onblur`、`onfocusin`、`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="4d782-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="4d782-291">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="4d782-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="4d782-292">| | 入力            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`、`oninput` | | キーボード         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`、`onkeypress`、`onkeyup` | | マウス            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`、`oncontextmenu`、`ondblclick`、`onmousedown`、`onmouseup`、`onmouseover`、`onmousemove`、`onmouseout` | | マウス ポインター    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture` | | マウス ホイール      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`、`onmousewheel` | | 進行状況         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`、`onload`、`onloadend`、`onloadstart`、`onprogress`、`ontimeout` | | タッチ            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`、`ontouchend`、`ontouchmove`、`ontouchenter`、`ontouchleave`、`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="4d782-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="4d782-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="4d782-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="4d782-294">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4d782-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="4d782-295">[ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="4d782-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="4d782-296">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers):各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="4d782-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="4d782-297">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="4d782-297">Lambda expressions</span></span>

<span data-ttu-id="4d782-298">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="4d782-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="4d782-299">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="4d782-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="4d782-300">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4d782-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="4d782-301">ラムダ式では、`for` ループ内で直接、ループ変数 (`i`) を使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="4d782-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="4d782-302">そうしないと、すべてのラムダ式で同じ変数が使用され、`i` の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="4d782-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="4d782-303">値は常にローカル変数 (前の例では`buttonNumber`) でキャプチャしてから、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="4d782-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="4d782-304">EventCallback</span></span>

<span data-ttu-id="4d782-305">入れ子になったコンポーネントがある一般的なシナリオでは、子コンポーネントのイベントが発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d782-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="4d782-306">子コンポーネントで発生する `onclick` イベントが、一般的なユース ケースです。</span><span class="sxs-lookup"><span data-stu-id="4d782-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="4d782-307">コンポーネント間にわたってイベントを公開するには、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="4d782-308">親コンポーネントでは、コールバック メソッドを子コンポーネントの <xref:Microsoft.AspNetCore.Components.EventCallback> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="4d782-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="4d782-309">サンプル アプリの `ChildComponent` (*Components/ChildComponent.razor*) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から <xref:Microsoft.AspNetCore.Components.EventCallback> デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="4d782-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="4d782-310"><xref:Microsoft.AspNetCore.Components.EventCallback> は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="4d782-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="4d782-311">`ParentComponent` では、子の <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="4d782-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="4d782-312">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="4d782-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="4d782-313">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="4d782-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="4d782-314">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="4d782-315">`messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="4d782-316">コールバックのメソッド (`ShowMessage`) 内に、[StateHasChanged](xref:blazor/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4d782-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="4d782-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="4d782-318"><xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="4d782-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="4d782-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="4d782-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="4d782-320"><xref:Microsoft.AspNetCore.Components.EventCallback> は弱く型指定され、どの引数型でも許されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="4d782-321"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> を使用して <xref:Microsoft.AspNetCore.Components.EventCallback> または <xref:Microsoft.AspNetCore.Components.EventCallback%601> を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="4d782-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="4d782-322">イベント処理とバインド コンポーネントのパラメーターには、<xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="4d782-323">厳密に型指定された <xref:Microsoft.AspNetCore.Components.EventCallback%601> を <xref:Microsoft.AspNetCore.Components.EventCallback> よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="4d782-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="4d782-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="4d782-325">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="4d782-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="4d782-326">コールバックに渡される値がない場合は、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="4d782-327">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="4d782-327">Prevent default actions</span></span>

<span data-ttu-id="4d782-328">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="4d782-329">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="4d782-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="4d782-330">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="4d782-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="4d782-331">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="4d782-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="4d782-332">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="4d782-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="4d782-333">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="4d782-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="4d782-334">次の例では、`shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="4d782-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="4d782-335">既定のアクションを止めるためにイベント ハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4d782-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="4d782-336">イベント ハンドラーと、既定のアクションを止めるシナリオは、独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="4d782-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="4d782-337">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="4d782-337">Stop event propagation</span></span>

<span data-ttu-id="4d782-338">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d782-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="4d782-339">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="4d782-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
