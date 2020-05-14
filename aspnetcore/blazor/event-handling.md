---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: aa338bbe61eec14bc1e1b3606e11e26bfb0e6a09
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967468"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="e2a3a-103">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="e2a3a-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="e2a3a-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e2a3a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e2a3a-105">Razor コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-105">Razor components provide event handling features.</span></span> <span data-ttu-id="e2a3a-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="e2a3a-107">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="e2a3a-108">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="e2a3a-109">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="e2a3a-110">[StateHasChanged](xref:blazor/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="e2a3a-111">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="e2a3a-112">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="e2a3a-113">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="e2a3a-113">Event argument types</span></span>

<span data-ttu-id="e2a3a-114">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="e2a3a-115">メソッド呼び出しでイベント型を指定する必要があるのは、そのイベント型がメソッド内で使用されている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="e2a3a-116">サポートされている `EventArgs` を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="e2a3a-117">event</span><span class="sxs-lookup"><span data-stu-id="e2a3a-117">Event</span></span>            | <span data-ttu-id="e2a3a-118">クラス</span><span class="sxs-lookup"><span data-stu-id="e2a3a-118">Class</span></span>                | <span data-ttu-id="e2a3a-119">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="e2a3a-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="e2a3a-120">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="e2a3a-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="e2a3a-121">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="e2a3a-122">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="e2a3a-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="e2a3a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="e2a3a-124">`DataTransfer` および `DataTransferItem` では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="e2a3a-125">Error</span><span class="sxs-lookup"><span data-stu-id="e2a3a-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="e2a3a-126">event</span><span class="sxs-lookup"><span data-stu-id="e2a3a-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="e2a3a-127">*全般*</span><span class="sxs-lookup"><span data-stu-id="e2a3a-127">*General*</span></span><br><span data-ttu-id="e2a3a-128">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="e2a3a-129">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="e2a3a-129">*Clipboard*</span></span><br><span data-ttu-id="e2a3a-130">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="e2a3a-131">*入力*</span><span class="sxs-lookup"><span data-stu-id="e2a3a-131">*Input*</span></span><br><span data-ttu-id="e2a3a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="e2a3a-133">*メディア*</span><span class="sxs-lookup"><span data-stu-id="e2a3a-133">*Media*</span></span><br><span data-ttu-id="e2a3a-134">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="e2a3a-135">フォーカス</span><span class="sxs-lookup"><span data-stu-id="e2a3a-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="e2a3a-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="e2a3a-137">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="e2a3a-138">入力</span><span class="sxs-lookup"><span data-stu-id="e2a3a-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="e2a3a-139">`onchange`、`oninput`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="e2a3a-140">キーボード</span><span class="sxs-lookup"><span data-stu-id="e2a3a-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="e2a3a-141">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="e2a3a-142">マウス</span><span class="sxs-lookup"><span data-stu-id="e2a3a-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="e2a3a-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="e2a3a-144">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="e2a3a-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="e2a3a-145">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="e2a3a-146">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="e2a3a-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="e2a3a-147">`onwheel`、`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="e2a3a-148">進行状況</span><span class="sxs-lookup"><span data-stu-id="e2a3a-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="e2a3a-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="e2a3a-150">タッチ</span><span class="sxs-lookup"><span data-stu-id="e2a3a-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="e2a3a-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="e2a3a-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="e2a3a-152">`TouchPoint` は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="e2a3a-153">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="e2a3a-154">[ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="e2a3a-155">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; 各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="e2a3a-156">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="e2a3a-156">Lambda expressions</span></span>

<span data-ttu-id="e2a3a-157">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="e2a3a-158">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="e2a3a-159">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (`MouseEventArgs`) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="e2a3a-160">ラムダ式では、`for` ループ内で直接、ループ変数 (`i`) を使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="e2a3a-161">そうしないと、すべてのラムダ式で同じ変数が使用され、`i` の値はすべてのラムダで同じになります。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="e2a3a-162">値は常にローカル変数 (前の例では`buttonNumber`) でキャプチャしてから、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="e2a3a-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="e2a3a-163">EventCallback</span></span>

<span data-ttu-id="e2a3a-164">入れ子になったコンポーネントがある一般的なシナリオでは、子で `onclick` イベントが発生したときなど、子コンポーネントのイベント&mdash;が発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="e2a3a-165">コンポーネント間にわたってイベントを公開するには、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="e2a3a-166">親コンポーネントでは、コールバック メソッドを子コンポーネントの `EventCallback` に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="e2a3a-167">サンプル アプリの `ChildComponent` (*Components/ChildComponent.razor*) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から `EventCallback` デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="e2a3a-168">`EventCallback` は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="e2a3a-169">`ParentComponent` では、子の `EventCallback<T>` (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="e2a3a-170">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="e2a3a-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="e2a3a-171">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="e2a3a-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="e2a3a-172">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="e2a3a-173">`messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-173">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="e2a3a-174">コールバックのメソッド (`ShowMessage`) 内に、[StateHasChanged](xref:blazor/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="e2a3a-175">`StateHasChanged` は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="e2a3a-176">`EventCallback` と `EventCallback<T>` では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="e2a3a-177">`EventCallback<T>` は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="e2a3a-178">`EventCallback` は弱く型指定され、どの引数型でも許されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="e2a3a-179">`InvokeAsync` を使用して `EventCallback` または `EventCallback<T>` を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="e2a3a-180">イベント処理とバインド コンポーネントのパラメーターには、`EventCallback` と `EventCallback<T>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="e2a3a-181">厳密に型指定された `EventCallback<T>` を `EventCallback` よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="e2a3a-182">`EventCallback<T>` では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="e2a3a-183">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="e2a3a-184">コールバックに渡される値がない場合は、`EventCallback` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="e2a3a-185">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="e2a3a-185">Prevent default actions</span></span>

<span data-ttu-id="e2a3a-186">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="e2a3a-187">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="e2a3a-188">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="e2a3a-189">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="e2a3a-190">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="e2a3a-191">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="e2a3a-192">次の例では、`shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-192">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="e2a3a-193">既定のアクションを止めるためにイベント ハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="e2a3a-194">イベント ハンドラーと、既定のアクションを止めるシナリオは、独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="e2a3a-195">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="e2a3a-195">Stop event propagation</span></span>

<span data-ttu-id="e2a3a-196">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="e2a3a-197">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e2a3a-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
