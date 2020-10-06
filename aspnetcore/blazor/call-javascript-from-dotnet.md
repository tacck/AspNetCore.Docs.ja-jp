---
title: ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す
author: guardrex
description: Blazor アプリで .NET メソッドから JavaScript 関数を呼び出す方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: da4ce8a2610fc07d22153f66831d693ae66e0fe5
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424153"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="c9435-103">ASP.NET Core Blazor で .NET メソッドから JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="c9435-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="c9435-104">作成者: [Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9435-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c9435-105">Blazor アプリでは、.NET メソッドから JavaScript 関数を呼び出すことも、JavaScript 関数から .NET メソッドを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="c9435-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="c9435-106">これらのシナリオは、"*JavaScript 相互運用*" ("*JS 相互運用*") と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="c9435-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="c9435-107">この記事では、.NET から JavaScript 関数を呼び出す方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9435-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="c9435-108">JavaScript から .NET メソッドを呼び出す方法については、「<xref:blazor/call-dotnet-from-javascript>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9435-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="c9435-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c9435-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c9435-110">.NET から JavaScript を呼び出すには、<xref:Microsoft.JSInterop.IJSRuntime> 抽象化を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="c9435-111">JS 相互運用呼び出しを発行するには、コンポーネントに <xref:Microsoft.JSInterop.IJSRuntime> 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9435-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="c9435-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> は、JSON シリアル化可能な任意の数の引数と共に呼び出す JavaScript 関数の識別子を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c9435-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="c9435-113">関数の識別子は、グローバル スコープ (`window`) に関連しています。</span><span class="sxs-lookup"><span data-stu-id="c9435-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="c9435-114">`window.someScope.someFunction` を呼び出す場合、識別子は `someScope.someFunction` です。</span><span class="sxs-lookup"><span data-stu-id="c9435-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="c9435-115">関数は、呼び出す前に登録する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="c9435-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="c9435-116">また、戻り値の型 `T` も JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="c9435-117">`T` は、返される JSON 型に最適にマップされる .NET 型と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="c9435-118">[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) を返す JavaScript 関数は、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> と共に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="c9435-119">`InvokeAsync` は Promise のラップを解除し、Promise によって待機された値を返します。</span><span class="sxs-lookup"><span data-stu-id="c9435-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="c9435-120">Blazor Server アプリでプリレンダリングが有効になっている場合、最初のプリレンダリング中に JavaScript を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="c9435-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="c9435-121">JavaScript 相互運用呼び出しは、ブラウザーとの接続が確立されるまで遅延させる必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="c9435-122">詳細については、「[Blazor Server アプリがプリレンダリングされていることを検出する](#detect-when-a-blazor-server-app-is-prerendering)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9435-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="c9435-123">次の例は、JavaScript ベースのデコーダーである [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) に基づいています。</span><span class="sxs-lookup"><span data-stu-id="c9435-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="c9435-124">この例では、開発者コードから既存の JavaScript API に要件をオフロードする C# メソッドから JavaScript 関数を呼び出す方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c9435-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="c9435-125">JavaScript 関数は、C# メソッドからバイト配列を受け取り、配列をデコードし、テキストをコンポーネントに返して表示できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c9435-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="c9435-126">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`TextDecoder` を使用して、渡された配列をデコードし、デコードした値を返す JavaScript 関数を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9435-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="c9435-127">JavaScript コードでは、前の例で示したコードのように、スクリプト ファイルへの参照を使用して JavaScript ファイル (`.js`) から読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="c9435-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="c9435-128">次のコンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c9435-128">The following component:</span></span>

* <span data-ttu-id="c9435-129">コンポーネント ボタン ( **`Convert Array`** ) が選択された場合、`JSRuntime` を使用して `convertArray` JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9435-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="c9435-130">JavaScript 関数が呼び出されると、渡された配列が文字列に変換されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="c9435-131">文字列は、表示できるようにコンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="c9435-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="c9435-132">IJSRuntime</span></span>

<span data-ttu-id="c9435-133"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化を使用するには、次のいずれかの方法を採用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="c9435-134">Razor コンポーネント (`.razor`) に <xref:Microsoft.JSInterop.IJSRuntime> 抽象化を挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9435-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="c9435-135">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="c9435-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="c9435-136">関数は <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を指定して呼び出され、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="c9435-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="c9435-137"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化をクラス (`.cs`) に挿入します。</span><span class="sxs-lookup"><span data-stu-id="c9435-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="c9435-138">`wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) の `<head>` 要素内で、`handleTickerChanged` JavaScript 関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="c9435-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="c9435-139">関数は `JSRuntime.InvokeAsync` を指定して呼び出され、次の値を返します。</span><span class="sxs-lookup"><span data-stu-id="c9435-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="c9435-140">[BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) でコンテンツを動的に生成するには、`[Inject]` 属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="c9435-141">このトピックに添付されているクライアント側のサンプル アプリでは、ユーザー入力を受け取り、ウェルカム メッセージを表示するために、DOM とやりとりする 2 つの JavaScript 関数をアプリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9435-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="c9435-142">`showPrompt`:ユーザー入力 (ユーザーの名前) を受け入れるプロンプトを生成し、名前を呼び出し元に返します。</span><span class="sxs-lookup"><span data-stu-id="c9435-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="c9435-143">`displayWelcome`:`welcome` の `id` を持つ DOM オブジェクトに、呼び出し元からのウェルカム メッセージを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c9435-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="c9435-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="c9435-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="c9435-145">JavaScript ファイルを参照する `<script>` タグを `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に配置します。</span><span class="sxs-lookup"><span data-stu-id="c9435-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="c9435-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="c9435-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="c9435-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="c9435-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="c9435-148">`<script>` タグを動的に更新できないため、`<script>` タグをコンポーネント ファイル内に配置しないでください。</span><span class="sxs-lookup"><span data-stu-id="c9435-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="c9435-149">.NET メソッドは、<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> を呼び出して、`exampleJsInterop.js` ファイル内の JavaScript 関数と相互運用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="c9435-150"><xref:Microsoft.JSInterop.IJSRuntime> 抽象化は、Blazor Server のシナリオを可能にするために非同期です。</span><span class="sxs-lookup"><span data-stu-id="c9435-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="c9435-151">アプリが Blazor WebAssembly アプリであり、JavaScript 関数を同期的に呼び出す必要がある場合は、<xref:Microsoft.JSInterop.IJSInProcessRuntime> にダウンキャストし、代わりに <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9435-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="c9435-152">ほとんどの JS 相互運用ライブラリでは、確実にすべてのシナリオでライブラリを使用できるように、非同期 API を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c9435-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="c9435-153">標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離を有効にするには、「[Blazor JavaScript の分離とオブジェクト参照](#blazor-javascript-isolation-and-object-references)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9435-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="c9435-154">サンプル アプリには、JS 相互運用を示すコンポーネントが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c9435-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="c9435-155">コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="c9435-155">The component:</span></span>

* <span data-ttu-id="c9435-156">JavaScript プロンプトを介してユーザー入力を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c9435-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="c9435-157">テキストをコンポーネントに返して処理します。</span><span class="sxs-lookup"><span data-stu-id="c9435-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="c9435-158">DOM とやりとりしてウェルカム メッセージを表示する 2 番目の JavaScript 関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9435-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="c9435-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="c9435-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="c9435-160">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="c9435-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="c9435-161">コンポーネントの **`Trigger JavaScript Prompt`** ボタンを選択して `TriggerJsPrompt` を実行すると、`wwwroot/exampleJsInterop.js` ファイル内に指定した JavaScript `showPrompt` 関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="c9435-162">`showPrompt` 関数は、ユーザー入力 (ユーザーの名前) を受け取ります。これは、HTML エンコードされ、コンポーネントに返されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="c9435-163">コンポーネントにより、ユーザーの名前がローカル変数 `name` に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="c9435-164">`name` に格納された文字列は、ウェルカム メッセージに組み込まれます。このメッセージが JavaScript 関数 `displayWelcome` に渡され、ウェルカム メッセージが見出しタグにレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="c9435-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="c9435-165">void JavaScript 関数を呼び出す</span><span class="sxs-lookup"><span data-stu-id="c9435-165">Call a void JavaScript function</span></span>

<span data-ttu-id="c9435-166">[void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) または [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) を返す JavaScript 関数は、<xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を指定して呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9435-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="c9435-167">Blazor Server アプリがプリレンダリングされていることを検出する</span><span class="sxs-lookup"><span data-stu-id="c9435-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="c9435-168">要素への参照をキャプチャする</span><span class="sxs-lookup"><span data-stu-id="c9435-168">Capture references to elements</span></span>

<span data-ttu-id="c9435-169">一部の JS 相互運用シナリオでは、HTML 要素への参照が必要です。</span><span class="sxs-lookup"><span data-stu-id="c9435-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="c9435-170">たとえば、UI ライブラリで初期化のための要素参照が必要な場合、`focus` や `play` などの要素でコマンドのような API の呼び出し必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="c9435-171">次の方法を使用して、コンポーネント内の HTML 要素への参照をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="c9435-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="c9435-172">`@ref` 属性を HTML 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="c9435-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="c9435-173">名前が `@ref` 属性の値に一致する <xref:Microsoft.AspNetCore.Components.ElementReference> 型のフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="c9435-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="c9435-174">次の例は、`username` `<input>` 要素への参照をキャプチャする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c9435-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="c9435-175">Blazor とやりとりしない空の要素のコンテンツを変化させるには、要素参照のみを使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="c9435-176">このシナリオは、サードパーティの API から要素にコンテンツが提供される場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c9435-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="c9435-177">Blazor は要素とやりとりしないため、Blazor の要素表現と DOM との間に競合が発生する可能性がありません。</span><span class="sxs-lookup"><span data-stu-id="c9435-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="c9435-178">次の例では、Blazor が DOM とやりとりしてこの要素のリスト項目 (`<li>`) を設定するため、順序なしリスト (`ul`) のコンテンツを変化させるのは "*危険*" です。</span><span class="sxs-lookup"><span data-stu-id="c9435-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="c9435-179">JS 相互運用により要素 `MyList` のコンテンツが変更され、Blazor でその要素に差分を適用しようとした場合、差分は DOM と一致しません。</span><span class="sxs-lookup"><span data-stu-id="c9435-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="c9435-180">.NET コードに関しては、<xref:Microsoft.AspNetCore.Components.ElementReference> は不透明なハンドルです。</span><span class="sxs-lookup"><span data-stu-id="c9435-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="c9435-181"><xref:Microsoft.AspNetCore.Components.ElementReference> を使用して実行できるのは、JS 相互運用を介して JavaScript コードに渡すこと "*のみ*" です。</span><span class="sxs-lookup"><span data-stu-id="c9435-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="c9435-182">これを行うと、JavaScript 側のコードが `HTMLElement` インスタンスを受け取り、通常の DOM API で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9435-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="c9435-183">たとえば、次のコードでは、要素にフォーカスを設定できるようにする .NET 拡張メソッドを定義しています。</span><span class="sxs-lookup"><span data-stu-id="c9435-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="c9435-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="c9435-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="c9435-185">値を返さない JavaScript 関数を呼び出すには、<xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c9435-186">次のコードは、キャプチャされた <xref:Microsoft.AspNetCore.Components.ElementReference> で前述の JavaScript 関数を呼び出して、ユーザー名入力にフォーカスを設定します。</span><span class="sxs-lookup"><span data-stu-id="c9435-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="c9435-187">拡張メソッドを使用するには、<xref:Microsoft.JSInterop.IJSRuntime> インスタンスを受け取る静的拡張メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9435-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="c9435-188">`Focus` メソッドは、オブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="c9435-189">次の例では、`Focus` メソッドが `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="c9435-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="c9435-190">`username` 変数は、コンポーネントがレンダリングされた後にのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="c9435-191">未入力の <xref:Microsoft.AspNetCore.Components.ElementReference> が JavaScript コードに渡された場合、JavaScript コードは `null` の値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="c9435-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="c9435-192">コンポーネントのレンダリングが完了した後に要素参照を操作する (要素に初期フォーカスを設定する) には、[`OnAfterRenderAsync` または `OnAfterRender` コンポーネント ライフサイクル メソッド](xref:blazor/components/lifecycle#after-component-render)を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="c9435-193">ジェネリック型を操作して値を返す場合は、<xref:System.Threading.Tasks.ValueTask%601> を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="c9435-194">`GenericMethod` は、型を持つオブジェクトで直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="c9435-195">次の例では、`GenericMethod` が `JsInteropClasses` 名前空間から使用できることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="c9435-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="c9435-196">コンポーネント間で要素を参照する</span><span class="sxs-lookup"><span data-stu-id="c9435-196">Reference elements across components</span></span>

<span data-ttu-id="c9435-197"><xref:Microsoft.AspNetCore.Components.ElementReference> は、コンポーネントの <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> メソッドでのみ有効であることが保証されます (要素参照は `struct` です)。そのため、コンポーネント間で要素参照を渡すことはできません。</span><span class="sxs-lookup"><span data-stu-id="c9435-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="c9435-198">親コンポーネントが要素参照を他のコンポーネントで使用できるようにするために、親コンポーネントは次のことを実行できます。</span><span class="sxs-lookup"><span data-stu-id="c9435-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="c9435-199">子コンポーネントがコールバックを登録できるようにします。</span><span class="sxs-lookup"><span data-stu-id="c9435-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="c9435-200">渡された要素参照を使用して、登録されたコールバックを<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> イベント中に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c9435-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="c9435-201">間接的には、この方法により、子コンポーネントが親の要素参照とやりとりできるようになります。</span><span class="sxs-lookup"><span data-stu-id="c9435-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="c9435-202">次の Blazor WebAssembly の例でこのアプローチを示します。</span><span class="sxs-lookup"><span data-stu-id="c9435-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="c9435-203">`wwwroot/index.html` の `<head>` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c9435-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="c9435-204">`wwwroot/index.html` の `<body>` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="c9435-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="c9435-205">`Pages/Index.razor` (親コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="c9435-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="c9435-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="c9435-206">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="c9435-207">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="c9435-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="c9435-208">`Shared/SurveyPrompt.razor` (子コンポーネント):</span><span class="sxs-lookup"><span data-stu-id="c9435-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="c9435-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="c9435-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="c9435-210">プレースホルダー `{APP ASSEMBLY}` は、アプリのアプリ アセンブリ名です (例: `BlazorSample`)。</span><span class="sxs-lookup"><span data-stu-id="c9435-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="c9435-211">JS 相互運用呼び出しの強化</span><span class="sxs-lookup"><span data-stu-id="c9435-211">Harden JS interop calls</span></span>

<span data-ttu-id="c9435-212">JS 相互運用は、ネットワーク エラーにより失敗する可能性があるため、信頼性の低いものとして扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="c9435-213">既定では、Blazor Server アプリでは、サーバー上の JS 相互運用の呼び出しは 1 分後にタイムアウトします。</span><span class="sxs-lookup"><span data-stu-id="c9435-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="c9435-214">アプリでより積極的なタイムアウトが許容される場合は、次のいずれかの方法を使用してタイムアウトを設定します。</span><span class="sxs-lookup"><span data-stu-id="c9435-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="c9435-215">`Startup.ConfigureServices` でグローバルに、タイムアウトを指定します。</span><span class="sxs-lookup"><span data-stu-id="c9435-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="c9435-216">コンポーネント コードでの呼び出しごとに、1 回の呼び出しでタイムアウトを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c9435-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="c9435-217">リソース枯渇の詳細については、「<xref:blazor/security/server/threat-mitigation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9435-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="c9435-218">循環オブジェクト参照の回避</span><span class="sxs-lookup"><span data-stu-id="c9435-218">Avoid circular object references</span></span>

<span data-ttu-id="c9435-219">循環参照を含むオブジェクトは、次のいずれに対しても、クライアントでシリアル化することはできません。</span><span class="sxs-lookup"><span data-stu-id="c9435-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="c9435-220">.NET メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="c9435-220">.NET method calls.</span></span>
* <span data-ttu-id="c9435-221">戻り値の型に循環参照がある場合の、C# からの JavaScript メソッドの呼び出し。</span><span class="sxs-lookup"><span data-stu-id="c9435-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="c9435-222">詳細については、次のイシューを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9435-222">For more information, see the following issues:</span></span>

* <span data-ttu-id="c9435-223">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525) (循環参照はサポートされていません、テイク 2 (dotnet/aspnetcore #20525))</span><span class="sxs-lookup"><span data-stu-id="c9435-223">[Circular references are not supported, take two (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)</span></span>
* <span data-ttu-id="c9435-224">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820) (提案: シリアル化するときに循環参照を処理するメカニズムを追加する (dotnet/runtime #30820))</span><span class="sxs-lookup"><span data-stu-id="c9435-224">[Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="c9435-225">Blazor JavaScript の分離とオブジェクト参照</span><span class="sxs-lookup"><span data-stu-id="c9435-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="c9435-226">Blazor により、標準 [JavaScript モジュール](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)で JavaScript の分離が有効にされます。</span><span class="sxs-lookup"><span data-stu-id="c9435-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="c9435-227">JavaScript の分離には、次のような利点があります。</span><span class="sxs-lookup"><span data-stu-id="c9435-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="c9435-228">インポートされる JavaScript によって、グローバル名前空間が汚染されなくなります。</span><span class="sxs-lookup"><span data-stu-id="c9435-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="c9435-229">ライブラリおよびコンポーネントのコンシューマーは、関連する JavaScript をインポートする必要がありません。</span><span class="sxs-lookup"><span data-stu-id="c9435-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="c9435-230">たとえば、次の JavaScript モジュールにより、ブラウザー プロンプトを表示する JavaScript 関数がエクスポートされます。</span><span class="sxs-lookup"><span data-stu-id="c9435-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="c9435-231">前の JavaScript モジュールを静的 Web アセット (`wwwroot/exampleJsInterop.js`) として .NET ライブラリに追加し、<xref:Microsoft.JSInterop.IJSRuntime> サービスを使用してそのモジュールを .NET コードにインポートします。</span><span class="sxs-lookup"><span data-stu-id="c9435-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="c9435-232">サービスは、次の例では `jsRuntime` (表示はなし) として挿入されます。</span><span class="sxs-lookup"><span data-stu-id="c9435-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="c9435-233">前の例の `import` 識別子は、JavaScript モジュールをインポートするために特別に使用される特殊な識別子です。</span><span class="sxs-lookup"><span data-stu-id="c9435-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="c9435-234">安定した静的な Web アセット パスを使用してモジュールを指定します: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`。</span><span class="sxs-lookup"><span data-stu-id="c9435-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="c9435-235">プレースホルダー `{LIBRARY NAME}` は、ライブラリの名前です。</span><span class="sxs-lookup"><span data-stu-id="c9435-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="c9435-236">プレースホルダー `{PATH UNDER WWWROOT}` は、`wwwroot` の下にあるスクリプトへのパスです。</span><span class="sxs-lookup"><span data-stu-id="c9435-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="c9435-237"><xref:Microsoft.JSInterop.IJSRuntime> により、モジュールが `JSObjectReference` としてインポートされます。これは、.NET コードから JavaScript オブジェクトへの参照を表します。</span><span class="sxs-lookup"><span data-stu-id="c9435-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="c9435-238">モジュールからエクスポートされた JavaScript 関数を呼び出すには、`JSObjectReference` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9435-238">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c9435-239">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c9435-239">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="c9435-240">InteropComponent.razor の例 (dotnet/AspNetCore GitHub リポジトリ、3.1 リリース ブランチ)</span><span class="sxs-lookup"><span data-stu-id="c9435-240">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="c9435-241">Blazor Server アプリで大規模なデータ転送を実行する</span><span class="sxs-lookup"><span data-stu-id="c9435-241">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
