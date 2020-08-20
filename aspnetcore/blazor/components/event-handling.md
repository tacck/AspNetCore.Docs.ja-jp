---
title: ASP.NET Core Blazor のイベント処理
author: guardrex
description: イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、Blazor のイベント処理機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/event-handling
ms.openlocfilehash: 57eff50370219324037756f4605f7f9708b012fa
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628522"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="38488-103">ASP.NET Core Blazor のイベント処理</span><span class="sxs-lookup"><span data-stu-id="38488-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="38488-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="38488-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="38488-105">Razor コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="38488-105">Razor components provide event handling features.</span></span> <span data-ttu-id="38488-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、Razor コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="38488-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="38488-107">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38488-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="38488-108">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38488-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="38488-109">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="38488-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="38488-110">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="38488-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="38488-111">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="38488-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="38488-112">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="38488-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="38488-113">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="38488-113">Event argument types</span></span>

<span data-ttu-id="38488-114">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="38488-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="38488-115">イベント メソッド定義にイベント パラメーターを指定することは任意であり、そのイベントの種類がメソッドで使用されている場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="38488-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="38488-116">次の例では、`MouseEventArgs` イベント引数がメッセージ テキストを設定する目的で `ShowMessage` メソッドで使用されています。</span><span class="sxs-lookup"><span data-stu-id="38488-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="38488-117">サポートされている <xref:System.EventArgs> を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="38488-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="38488-118">event</span><span class="sxs-lookup"><span data-stu-id="38488-118">Event</span></span>            | <span data-ttu-id="38488-119">クラス</span><span class="sxs-lookup"><span data-stu-id="38488-119">Class</span></span>                | <span data-ttu-id="38488-120">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="38488-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="38488-121">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="38488-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="38488-122">`oncut`、`oncopy`、`onpaste`</span><span class="sxs-lookup"><span data-stu-id="38488-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="38488-123">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="38488-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="38488-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="38488-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="38488-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> および <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="38488-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="38488-126">Error</span><span class="sxs-lookup"><span data-stu-id="38488-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="38488-127">event</span><span class="sxs-lookup"><span data-stu-id="38488-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="38488-128">*全般*</span><span class="sxs-lookup"><span data-stu-id="38488-128">*General*</span></span><br><span data-ttu-id="38488-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="38488-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="38488-130">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="38488-130">*Clipboard*</span></span><br><span data-ttu-id="38488-131">`onbeforecut`、`onbeforecopy`、`onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="38488-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="38488-132">*入力*</span><span class="sxs-lookup"><span data-stu-id="38488-132">*Input*</span></span><br><span data-ttu-id="38488-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="38488-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="38488-134">*メディア*</span><span class="sxs-lookup"><span data-stu-id="38488-134">*Media*</span></span><br><span data-ttu-id="38488-135">`oncanplay`、`oncanplaythrough`、`oncuechange`、`ondurationchange`、`onemptied`、`onended`、`onpause`、`onplay`、`onplaying`、`onratechange`、`onseeked`、`onseeking`、`onstalled`、`onstop`、`onsuspend`、`ontimeupdate`、`onvolumechange`、`onwaiting`</span><span class="sxs-lookup"><span data-stu-id="38488-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="38488-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> は、イベント名とイベント引数の型の間のマッピングを構成する属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="38488-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="38488-137">フォーカス</span><span class="sxs-lookup"><span data-stu-id="38488-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="38488-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="38488-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="38488-139">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="38488-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="38488-140">入力</span><span class="sxs-lookup"><span data-stu-id="38488-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="38488-141">`onchange`、`oninput`</span><span class="sxs-lookup"><span data-stu-id="38488-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="38488-142">キーボード</span><span class="sxs-lookup"><span data-stu-id="38488-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="38488-143">`onkeydown`、`onkeypress`、`onkeyup`</span><span class="sxs-lookup"><span data-stu-id="38488-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="38488-144">マウス</span><span class="sxs-lookup"><span data-stu-id="38488-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="38488-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="38488-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="38488-146">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="38488-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="38488-147">`onpointerdown`、`onpointerup`、`onpointercancel`、`onpointermove`、`onpointerover`、`onpointerout`、`onpointerenter`、`onpointerleave`、`ongotpointercapture`、`onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="38488-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="38488-148">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="38488-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="38488-149">`onwheel`、`onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="38488-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="38488-150">進行状況</span><span class="sxs-lookup"><span data-stu-id="38488-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="38488-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="38488-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="38488-152">タッチ</span><span class="sxs-lookup"><span data-stu-id="38488-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="38488-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="38488-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="38488-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="38488-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="38488-155">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="38488-155">For more information, see the following resources:</span></span>

* <span data-ttu-id="38488-156">[ASP.NET Core 参照ソースの `EventArgs` クラス (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="38488-156">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="38488-157">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers):各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="38488-157">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="38488-158">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="38488-158">Lambda expressions</span></span>

<span data-ttu-id="38488-159">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="38488-159">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="38488-160">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="38488-160">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="38488-161">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="38488-161">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="38488-162">前の `for` ループの例の `i` などのラムダ式内で、ループ変数を直接使用し**ない**でください。</span><span class="sxs-lookup"><span data-stu-id="38488-162">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="38488-163">そうしないと、すべてのラムダ式で同じ変数が使用され、すべてのラムダで同じ値が使用されることになります。</span><span class="sxs-lookup"><span data-stu-id="38488-163">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="38488-164">常にローカル変数の変数値をキャプチャしてから使用してください。</span><span class="sxs-lookup"><span data-stu-id="38488-164">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="38488-165">上の例では、ループ変数の `i` は `buttonNumber` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="38488-165">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="38488-166">EventCallback</span><span class="sxs-lookup"><span data-stu-id="38488-166">EventCallback</span></span>

<span data-ttu-id="38488-167">入れ子になったコンポーネントがある一般的なシナリオでは、子コンポーネントのイベントが発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="38488-167">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="38488-168">子コンポーネントで発生する `onclick` イベントが、一般的なユース ケースです。</span><span class="sxs-lookup"><span data-stu-id="38488-168">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="38488-169">コンポーネント間にわたってイベントを公開するには、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="38488-169">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="38488-170">親コンポーネントでは、コールバック メソッドを子コンポーネントの <xref:Microsoft.AspNetCore.Components.EventCallback> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="38488-170">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="38488-171">サンプル アプリの `ChildComponent` (`Components/ChildComponent.razor`) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から <xref:Microsoft.AspNetCore.Components.EventCallback> デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="38488-171">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="38488-172"><xref:Microsoft.AspNetCore.Components.EventCallback> は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="38488-172">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="38488-173">`ParentComponent` では、子の <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="38488-173">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="38488-174">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="38488-174">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="38488-175">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="38488-175">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="38488-176">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="38488-176">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="38488-177">`messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="38488-177">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="38488-178">コールバックのメソッド (`ShowMessage`) 内に、[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="38488-178">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="38488-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="38488-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="38488-180"><xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="38488-180"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="38488-181"><xref:Microsoft.AspNetCore.Components.EventCallback> は弱く型指定されており、`InvokeAsync(Object)` では任意の型の引数を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="38488-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="38488-182"><xref:Microsoft.AspNetCore.Components.EventCallback%601> は厳密に型指定されており、`InvokeAsync(T)` では `TValue` に代入可能な `T` 引数を渡す必要があります。</span><span class="sxs-lookup"><span data-stu-id="38488-182"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="38488-183"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> を使用して <xref:Microsoft.AspNetCore.Components.EventCallback> または <xref:Microsoft.AspNetCore.Components.EventCallback%601> を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="38488-183">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="38488-184">イベント処理とバインド コンポーネントのパラメーターには、<xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="38488-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="38488-185">厳密に型指定された <xref:Microsoft.AspNetCore.Components.EventCallback%601> を <xref:Microsoft.AspNetCore.Components.EventCallback> よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="38488-185">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="38488-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="38488-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="38488-187">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="38488-187">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="38488-188">コールバックに渡される値がない場合は、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="38488-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="38488-189">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="38488-189">Prevent default actions</span></span>

<span data-ttu-id="38488-190">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="38488-190">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="38488-191">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="38488-191">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="38488-192">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="38488-192">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="38488-193">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="38488-193">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="38488-194">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="38488-194">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="38488-195">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="38488-195">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="38488-196">次の例では、`shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="38488-196">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="38488-197">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="38488-197">Stop event propagation</span></span>

<span data-ttu-id="38488-198">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="38488-198">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="38488-199">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="38488-199">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
