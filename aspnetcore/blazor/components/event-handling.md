---
title: 'ASP.NET Core :::no-loc(Blazor)::: のイベント処理'
author: guardrex
description: 'イベント引数の型、イベントのコールバック、既定のブラウザー イベントの管理など、:::no-loc(Blazor)::: のイベント処理機能について説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/components/event-handling
ms.openlocfilehash: e8c3d6a9f2c6b50fc18da59b8e0b5475360673c7
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491465"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="9c79b-103">ASP.NET Core :::no-loc(Blazor)::: のイベント処理</span><span class="sxs-lookup"><span data-stu-id="9c79b-103">ASP.NET Core :::no-loc(Blazor)::: event handling</span></span>

<span data-ttu-id="9c79b-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9c79b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9c79b-105">:::no-loc(Razor)::: コンポーネントでは、イベント処理機能が提供されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-105">:::no-loc(Razor)::: components provide event handling features.</span></span> <span data-ttu-id="9c79b-106">[`@on{EVENT}`](xref:mvc/views/razor#onevent) という名前の HTML 要素属性 (`@onclick` など) でデリゲート型の値がある場合、:::no-loc(Razor)::: コンポーネントでは、属性の値がイベント ハンドラーとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a :::no-loc(Razor)::: component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="9c79b-107">次のコードでは、UI でボタンが選択されたときに `UpdateHeading` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="9c79b-108">次のコードでは、UI でチェック ボックスが変更されたときに `CheckChanged` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="9c79b-109">イベント ハンドラーは、非同期にして <xref:System.Threading.Tasks.Task> を返すこともできます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="9c79b-110">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) を手動で呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9c79b-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="9c79b-111">例外は、発生時にログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="9c79b-112">次の例では、ボタンが選択されると `UpdateHeading` が非同期に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="9c79b-113">イベント引数の型</span><span class="sxs-lookup"><span data-stu-id="9c79b-113">Event argument types</span></span>

<span data-ttu-id="9c79b-114">イベントによっては、イベント引数の型を選択できます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="9c79b-115">イベント メソッド定義にイベント パラメーターを指定することは任意であり、そのイベントの種類がメソッドで使用されている場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="9c79b-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="9c79b-116">次の例では、`MouseEventArgs` イベント引数がメッセージ テキストを設定する目的で `ShowMessage` メソッドで使用されています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="9c79b-117">サポートされている <xref:System.EventArgs> を次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="9c79b-118">event</span><span class="sxs-lookup"><span data-stu-id="9c79b-118">Event</span></span>            | <span data-ttu-id="9c79b-119">クラス</span><span class="sxs-lookup"><span data-stu-id="9c79b-119">Class</span></span>  | <span data-ttu-id="9c79b-120">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="9c79b-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="9c79b-121">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="9c79b-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="9c79b-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="9c79b-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="9c79b-123">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="9c79b-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="9c79b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="9c79b-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="9c79b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> および <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="9c79b-126">[HTML ドラッグ アンド ドロップ API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API) と共に [JS 相互運用](xref:blazor/call-javascript-from-dotnet)を使用し、:::no-loc(Blazor)::: アプリにドラッグ アンド ドロップを実装します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-126">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="9c79b-127">Error</span><span class="sxs-lookup"><span data-stu-id="9c79b-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="9c79b-128">event</span><span class="sxs-lookup"><span data-stu-id="9c79b-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="9c79b-129">*全般*</span><span class="sxs-lookup"><span data-stu-id="9c79b-129">*General*</span></span><br><span data-ttu-id="9c79b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="9c79b-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="9c79b-131">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="9c79b-131">*Clipboard*</span></span><br><span data-ttu-id="9c79b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="9c79b-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="9c79b-133">*入力*</span><span class="sxs-lookup"><span data-stu-id="9c79b-133">*Input*</span></span><br><span data-ttu-id="9c79b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="9c79b-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="9c79b-135">*メディア*</span><span class="sxs-lookup"><span data-stu-id="9c79b-135">*Media*</span></span><br><span data-ttu-id="9c79b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="9c79b-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="9c79b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> は、イベント名とイベント引数の型の間のマッピングを構成する属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="9c79b-138">フォーカス</span><span class="sxs-lookup"><span data-stu-id="9c79b-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="9c79b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="9c79b-140">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="9c79b-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="9c79b-141">入力</span><span class="sxs-lookup"><span data-stu-id="9c79b-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="9c79b-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="9c79b-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="9c79b-143">キーボード</span><span class="sxs-lookup"><span data-stu-id="9c79b-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="9c79b-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="9c79b-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="9c79b-145">マウス</span><span class="sxs-lookup"><span data-stu-id="9c79b-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="9c79b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="9c79b-147">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="9c79b-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="9c79b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="9c79b-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="9c79b-149">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="9c79b-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="9c79b-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="9c79b-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="9c79b-151">進行状況</span><span class="sxs-lookup"><span data-stu-id="9c79b-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="9c79b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="9c79b-153">タッチ</span><span class="sxs-lookup"><span data-stu-id="9c79b-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="9c79b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="9c79b-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="9c79b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="9c79b-156">event</span><span class="sxs-lookup"><span data-stu-id="9c79b-156">Event</span></span>            | <span data-ttu-id="9c79b-157">クラス</span><span class="sxs-lookup"><span data-stu-id="9c79b-157">Class</span></span> | <span data-ttu-id="9c79b-158">DOM のイベントとメモ</span><span class="sxs-lookup"><span data-stu-id="9c79b-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="9c79b-159">クリップボードのトピック</span><span class="sxs-lookup"><span data-stu-id="9c79b-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="9c79b-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="9c79b-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="9c79b-161">ドラッグ</span><span class="sxs-lookup"><span data-stu-id="9c79b-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="9c79b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="9c79b-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="9c79b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> および <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> では、ドラッグされた項目データを保持します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="9c79b-164">[HTML ドラッグ アンド ドロップ API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API) と共に [JS 相互運用](xref:blazor/call-javascript-from-dotnet)を使用し、:::no-loc(Blazor)::: アプリにドラッグ アンド ドロップを実装します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-164">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="9c79b-165">Error</span><span class="sxs-lookup"><span data-stu-id="9c79b-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="9c79b-166">event</span><span class="sxs-lookup"><span data-stu-id="9c79b-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="9c79b-167">*全般*</span><span class="sxs-lookup"><span data-stu-id="9c79b-167">*General*</span></span><br><span data-ttu-id="9c79b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="9c79b-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="9c79b-169">*クリップボード*</span><span class="sxs-lookup"><span data-stu-id="9c79b-169">*Clipboard*</span></span><br><span data-ttu-id="9c79b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="9c79b-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="9c79b-171">*入力*</span><span class="sxs-lookup"><span data-stu-id="9c79b-171">*Input*</span></span><br><span data-ttu-id="9c79b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="9c79b-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="9c79b-173">*メディア*</span><span class="sxs-lookup"><span data-stu-id="9c79b-173">*Media*</span></span><br><span data-ttu-id="9c79b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="9c79b-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="9c79b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> は、イベント名とイベント引数の型の間のマッピングを構成する属性を保持します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="9c79b-176">フォーカス</span><span class="sxs-lookup"><span data-stu-id="9c79b-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="9c79b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="9c79b-178">`relatedTarget` のサポートは含まれません。</span><span class="sxs-lookup"><span data-stu-id="9c79b-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="9c79b-179">入力</span><span class="sxs-lookup"><span data-stu-id="9c79b-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="9c79b-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="9c79b-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="9c79b-181">キーボード</span><span class="sxs-lookup"><span data-stu-id="9c79b-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="9c79b-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="9c79b-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="9c79b-183">マウス</span><span class="sxs-lookup"><span data-stu-id="9c79b-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="9c79b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="9c79b-185">マウス ポインター</span><span class="sxs-lookup"><span data-stu-id="9c79b-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="9c79b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="9c79b-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="9c79b-187">マウス ホイール</span><span class="sxs-lookup"><span data-stu-id="9c79b-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="9c79b-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="9c79b-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="9c79b-189">進行状況</span><span class="sxs-lookup"><span data-stu-id="9c79b-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="9c79b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="9c79b-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="9c79b-191">タッチ</span><span class="sxs-lookup"><span data-stu-id="9c79b-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="9c79b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="9c79b-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="9c79b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> は、タッチを検知するデバイス上の単一接触点を表します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="9c79b-194">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c79b-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="9c79b-195">[`EventArgs`ASP.NET Core 参照ソースのクラス (dotnet/aspnetcore `master` ブランチ)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web)。</span><span class="sxs-lookup"><span data-stu-id="9c79b-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="9c79b-196">`master` ブランチは、" *次の* " ASP.NET Core リリース用に開発される API を表します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="9c79b-197">現在のリリースでは、適切な GitHub リポジトリ ブランチ (たとえば、`release/3.1`) を選択します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="9c79b-198">[MDN Web ドキュメント:GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers):各 DOM イベントをサポートする HTML 要素に関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="9c79b-199">ラムダ式</span><span class="sxs-lookup"><span data-stu-id="9c79b-199">Lambda expressions</span></span>

<span data-ttu-id="9c79b-200">[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)も使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="9c79b-201">要素のセットを反復処理するときなど、追加の値に集中すると便利な場合がよくあります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="9c79b-202">次の例では 3 つのボタンを作成しています。各ボタンは、UI で選択されたときにイベント引数 (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) とそのボタン番号 (`buttonNumber`) を渡す `UpdateHeading` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="9c79b-203">前の `for` ループの例の `i` などのラムダ式内で、ループ変数を直接使用し **ない** でください。</span><span class="sxs-lookup"><span data-stu-id="9c79b-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="9c79b-204">そうしないと、すべてのラムダ式で同じ変数が使用され、すべてのラムダで同じ値が使用されることになります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="9c79b-205">常にローカル変数の変数値をキャプチャしてから使用してください。</span><span class="sxs-lookup"><span data-stu-id="9c79b-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="9c79b-206">上の例では、ループ変数の `i` は `buttonNumber` に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="9c79b-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="9c79b-207">EventCallback</span></span>

<span data-ttu-id="9c79b-208">入れ子になったコンポーネントがある一般的なシナリオでは、子コンポーネントのイベントが発生したときに親コンポーネントのメソッドを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="9c79b-209">子コンポーネントで発生する `onclick` イベントが、一般的なユース ケースです。</span><span class="sxs-lookup"><span data-stu-id="9c79b-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="9c79b-210">コンポーネント間にわたってイベントを公開するには、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="9c79b-211">親コンポーネントでは、コールバック メソッドを子コンポーネントの <xref:Microsoft.AspNetCore.Components.EventCallback> に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="9c79b-212">サンプル アプリの `ChildComponent` (`Components/ChildComponent.razor`) は、ボタンの `onclick` ハンドラーがどのように、サンプルの `ParentComponent` から <xref:Microsoft.AspNetCore.Components.EventCallback> デリゲートを受け取るように設定されているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="9c79b-213"><xref:Microsoft.AspNetCore.Components.EventCallback> は `MouseEventArgs` によって型指定されます。これは、周辺機器の `onclick` イベントに適しています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="9c79b-214">`ParentComponent` では、子の <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) を `ShowMessage` メソッドに設定しています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="9c79b-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="9c79b-215">`Pages/ParentComponent.razor`:</span></span>

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
        messageText = $"Blaze a new trail with :::no-loc(Blazor):::! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="9c79b-216">`ChildComponent` でボタンが選択されると:</span><span class="sxs-lookup"><span data-stu-id="9c79b-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="9c79b-217">`ParentComponent` の `ShowMessage` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="9c79b-218">`messageText` が更新されて、`ParentComponent` に表示されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="9c79b-219">コールバックのメソッド (`ShowMessage`) 内に、[`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) の呼び出しは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9c79b-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="9c79b-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> は、子イベントが子の中で実行されるイベント ハンドラーでコンポーネントのレンダリングをトリガーするのと同様に、`ParentComponent` を再レンダリングするために自動的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="9c79b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> では非同期デリゲートを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="9c79b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> は弱く型指定されており、`InvokeAsync(Object)` では任意の型の引数を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="9c79b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> は厳密に型指定されており、`InvokeAsync(T)` では `TValue` に代入可能な `T` 引数を渡す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="9c79b-224"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> を使用して <xref:Microsoft.AspNetCore.Components.EventCallback> または <xref:Microsoft.AspNetCore.Components.EventCallback%601> を呼び出して、<xref:System.Threading.Tasks.Task> を待機します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="9c79b-225">イベント処理とバインド コンポーネントのパラメーターには、<xref:Microsoft.AspNetCore.Components.EventCallback> と <xref:Microsoft.AspNetCore.Components.EventCallback%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="9c79b-226">厳密に型指定された <xref:Microsoft.AspNetCore.Components.EventCallback%601> を <xref:Microsoft.AspNetCore.Components.EventCallback> よりも優先します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="9c79b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> では、コンポーネントのユーザーに、より適切なエラー フィードバックが提供されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="9c79b-228">他の UI イベント ハンドラーと同様に、このイベント パラメーターの指定は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="9c79b-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="9c79b-229">コールバックに渡される値がない場合は、<xref:Microsoft.AspNetCore.Components.EventCallback> を使用します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="9c79b-230">既定のアクションを止める</span><span class="sxs-lookup"><span data-stu-id="9c79b-230">Prevent default actions</span></span>

<span data-ttu-id="9c79b-231">イベントに対する既定のアクションを止めるには、[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="9c79b-232">入力デバイスでキーが選択され、要素のフォーカスがテキスト ボックス上にあるときは、通常、ブラウザーによってテキスト ボックスにキーの文字が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="9c79b-233">次の例では、`@onkeypress:preventDefault` ディレクティブ属性を指定することで、既定の動作が止められています。</span><span class="sxs-lookup"><span data-stu-id="9c79b-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="9c79b-234">カウンターはインクリメントされて、 **+** キーは `<input>` 要素の値にキャプチャされません。</span><span class="sxs-lookup"><span data-stu-id="9c79b-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="9c79b-235">値なしで `@on{EVENT}:preventDefault` 属性を指定することは、`@on{EVENT}:preventDefault="true"` と同じことになります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="9c79b-236">属性の値は、式にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="9c79b-237">次の例では、`shouldPreventDefault` は `true` または `false` のいずれかに設定される `bool` フィールドです。</span><span class="sxs-lookup"><span data-stu-id="9c79b-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="9c79b-238">イベント伝達を停止する</span><span class="sxs-lookup"><span data-stu-id="9c79b-238">Stop event propagation</span></span>

<span data-ttu-id="9c79b-239">イベント伝達を停止するには、[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) ディレクティブ属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="9c79b-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="9c79b-240">次の例では、チェック ボックスをオンにすると、2 番目の子 `<div>` からのクリック イベントが親の `<div>` に伝達されなくなります。</span><span class="sxs-lookup"><span data-stu-id="9c79b-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="9c79b-241">要素にフォーカスを合わせる</span><span class="sxs-lookup"><span data-stu-id="9c79b-241">Focus an element</span></span>

<span data-ttu-id="9c79b-242">[要素参照](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)で `FocusAsync` を呼び出し、コード内の要素にフォーカスを合わせます。</span><span class="sxs-lookup"><span data-stu-id="9c79b-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
