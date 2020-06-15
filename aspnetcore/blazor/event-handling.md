---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 2d806413eb66d4c1301df4a0db950e8b919c09a4
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419750"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="f0470-103">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="f0470-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="f0470-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f0470-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="f0470-105"> コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-105"> components provide event handling features.</span></span> <span data-ttu-id="f0470-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="f0470-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="f0470-107">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f0470-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="f0470-108">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f0470-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="f0470-109">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f0470-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="f0470-110">[StateHasChanged](xref:blazor/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f0470-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="f0470-111">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="f0470-112">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="f0470-113">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="f0470-113">Event argument types</span></span>

<span data-ttu-id="f0470-114">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="f0470-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="f0470-115">メソッド呼び出しでイベント型を指定する必要があるのは、そのイベント型がメソッド内で使用されている場合のみです。</span><span class="sxs-lookup"><span data-stu-id="f0470-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="f0470-116">サポートされている <xref:System.EventArgs> を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="f0470-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="f0470-117">event</span><span class="sxs-lookup"><span data-stu-id="f0470-117">Event</span></span>            | <span data-ttu-id="f0470-118">クラス</span><span class="sxs-lookup"><span data-stu-id="f0470-118">Class</span></span>                | <span data-ttu-id="f0470-119">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="f0470-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="f0470-120">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="f0470-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="f0470-121">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="f0470-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="f0470-122">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="f0470-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="f0470-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="f0470-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="f0470-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> および <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="f0470-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="f0470-125">Error</span><span class="sxs-lookup"><span data-stu-id="f0470-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="f0470-126">event</span><span class="sxs-lookup"><span data-stu-id="f0470-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="f0470-127">*全般*</span><span class="sxs-lookup"><span data-stu-id="f0470-127">*General*</span></span><br><span data-ttu-id="f0470-128">`onactivate`、`onbeforeactivate`、`onbeforedeactivate`、`ondeactivate`、`onended`、`onfullscreenchange`、`onfullscreenerror`、`onloadeddata`、`onloadedmetadata`、`onpointerlockchange`、`onpointerlockerror`、`onreadystatechange`、`onscroll`</span><span class="sxs-lookup"><span data-stu-id="f0470-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="f0470-129">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="f0470-129">*Clipboard*</span></span><br><span data-ttu-id="f0470-130">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="f0470-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="f0470-131">*入力*</span><span class="sxs-lookup"><span data-stu-id="f0470-131">*Input*</span></span><br><span data-ttu-id="f0470-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="f0470-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="f0470-133">*メディア*</span><span class="sxs-lookup"><span data-stu-id="f0470-133">*Media*</span></span><br><span data-ttu-id="f0470-134">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="f0470-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="f0470-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> は、イベント名とイベント引数の型の間のマッピングを構成する属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="f0470-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="f0470-136">フォーカス</span><span class="sxs-lookup"><span data-stu-id="f0470-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="f0470-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="f0470-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="f0470-138">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="f0470-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="f0470-139">入力</span><span class="sxs-lookup"><span data-stu-id="f0470-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="f0470-140">`onchange`、`oninput`</span><span class="sxs-lookup"><span data-stu-id="f0470-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="f0470-141">キーボード</span><span class="sxs-lookup"><span data-stu-id="f0470-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="f0470-142">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="f0470-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="f0470-143">マウス</span><span class="sxs-lookup"><span data-stu-id="f0470-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="f0470-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="f0470-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="f0470-145">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="f0470-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="f0470-146">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="f0470-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="f0470-147">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="f0470-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="f0470-148">`onwheel`、`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="f0470-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="f0470-149">進行状況</span><span class="sxs-lookup"><span data-stu-id="f0470-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="f0470-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="f0470-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="f0470-151">タッチ</span><span class="sxs-lookup"><span data-stu-id="f0470-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="f0470-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="f0470-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="f0470-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="f0470-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="f0470-154">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f0470-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="f0470-155">[ASP.NET Core 参照ソースの EventArgs クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="f0470-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="f0470-156">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers):各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f0470-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="f0470-157">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="f0470-157">Lambda expressions</span></span>

<span data-ttu-id="f0470-158">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="f0470-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="f0470-159">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="f0470-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="f0470-160">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f0470-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="f0470-161">前の `for` ループの例の `i` や、`foreach` ループ内の参照変数などのラムダ式内で、ループ変数を直接使用し**ない**でください。</span><span class="sxs-lookup"><span data-stu-id="f0470-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="f0470-162">そうしないと、すべてのラムダ式で同じ変数が使用され、すべてのラムダで同じ値が使用されることになります。</span><span class="sxs-lookup"><span data-stu-id="f0470-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="f0470-163">常にローカル変数の変数値をキャプチャしてから使用してください。</span><span class="sxs-lookup"><span data-stu-id="f0470-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="f0470-164">上の例では、ループ変数の `i` は `buttonNumber` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="f0470-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="f0470-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="f0470-165">EventCallback</span></span>

<span data-ttu-id="f0470-166">入れ子になったコンポーネントがある一般的なシナリオでは、子コンポーネントのイベントが発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0470-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="f0470-167">子コンポーネントで発生する `onclick` イベントが、一般的なユース ケースです。</span><span class="sxs-lookup"><span data-stu-id="f0470-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="f0470-168">コンポーネント間にわたってイベントを公開するには、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0470-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f0470-169">親コンポーネントでは、コールバック メソッドを子コンポーネントの <xref:Microsoft.AspNetCore.Components.EventCallback> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="f0470-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="f0470-170">サンプル アプリの `ChildComponent` (*Components/ChildComponent.razor*) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から <xref:Microsoft.AspNetCore.Components.EventCallback> デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="f0470-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="f0470-171"><xref:Microsoft.AspNetCore.Components.EventCallback> は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="f0470-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="f0470-172">`ParentComponent` では、子の <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="f0470-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="f0470-173">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="f0470-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="f0470-174">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="f0470-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="f0470-175">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="f0470-176">`messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="f0470-177">コールバックのメソッド (`ShowMessage`) 内に、[StateHasChanged](xref:blazor/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f0470-177">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="f0470-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="f0470-179"><xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f0470-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="f0470-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> は厳密に型指定され、特定の引数型を必要とします。</span><span class="sxs-lookup"><span data-stu-id="f0470-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="f0470-181"><xref:Microsoft.AspNetCore.Components.EventCallback> は弱く型指定され、どの引数型でも許されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="f0470-182"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> を使用して <xref:Microsoft.AspNetCore.Components.EventCallback> または <xref:Microsoft.AspNetCore.Components.EventCallback%601> を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="f0470-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="f0470-183">イベント処理とバインド コンポーネントのパラメーターには、<xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0470-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="f0470-184">厳密に型指定された <xref:Microsoft.AspNetCore.Components.EventCallback%601> を <xref:Microsoft.AspNetCore.Components.EventCallback> よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="f0470-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="f0470-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="f0470-186">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="f0470-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="f0470-187">コールバックに渡される値がない場合は、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0470-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="f0470-188">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="f0470-188">Prevent default actions</span></span>

<span data-ttu-id="f0470-189">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0470-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="f0470-190">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f0470-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="f0470-191">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="f0470-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="f0470-192">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="f0470-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="f0470-193">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="f0470-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="f0470-194">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f0470-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="f0470-195">次の例では、`shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="f0470-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="f0470-196">既定のアクションを止めるためにイベント ハンドラーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="f0470-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="f0470-197">イベント ハンドラーと、既定のアクションを止めるシナリオは、独立して使用できます。</span><span class="sxs-lookup"><span data-stu-id="f0470-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="f0470-198">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="f0470-198">Stop event propagation</span></span>

<span data-ttu-id="f0470-199">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0470-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="f0470-200">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="f0470-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
