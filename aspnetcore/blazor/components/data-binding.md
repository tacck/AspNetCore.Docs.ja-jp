---
title: ASP.NET Core Blazor データ バインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディング機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628561"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="4dfc3-103">ASP.NET Core Blazor データ バインディング</span><span class="sxs-lookup"><span data-stu-id="4dfc3-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="4dfc3-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4dfc3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4dfc3-105">Razor コンポーネントはフィールド、プロパティ、または Razor 式の値が含まれる "[`@bind`](xref:mvc/views/razor#bind)" という名前の HTML 要素属性を使用してデータ バインディング機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="4dfc3-106">次の例では、`<input>` 要素を `currentValue` フィールドにバインドし、`<input>` 要素を `CurrentValue` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4dfc3-107">いずれかの要素がフォーカスを失うと、バインドされたフィールドまたはプロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="4dfc3-108">テキスト ボックスは、フィールドまたはプロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="4dfc3-109">コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、フィールドとプロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="4dfc3-110">`CurrentValue` プロパティで [`@bind`](xref:mvc/views/razor#bind) を使用する (`<input @bind="CurrentValue" />`) ことは、本質的に次の内容と同じです。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4dfc3-111">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="4dfc3-112">ユーザーがテキスト ボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="4dfc3-113">実際には、[`@bind`](xref:mvc/views/razor#bind) は型変換が行われるケースを扱うため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="4dfc3-114">原則として、[`@bind`](xref:mvc/views/razor#bind) は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="4dfc3-115">他のイベントのプロパティまたはフィールドをバインドするには、`event` パラメーターを含めた `@bind:event` 属性を含めます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="4dfc3-116">次の例は、`CurrentValue` プロパティを `oninput` イベントにバインドします。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4dfc3-117">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキスト ボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="4dfc3-118">`value` 以外の要素の属性をバインドするには、`@bind-{ATTRIBUTE}:event` 構文で `@bind-{ATTRIBUTE}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="4dfc3-119">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-119">In the following example:</span></span>

* <span data-ttu-id="4dfc3-120">コンポーネントが読み込まれると、段落のスタイルは **red** になります (`style="color:red"`)。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="4dfc3-121">ユーザーはボックスの値を変更して別の CSS カラー スタイルを反映させ、ページの要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="4dfc3-122">たとえば、ユーザーはボックスの値を `color:blue` に変更し、キーボードの <kbd>Tab</kbd> キーを押します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="4dfc3-123">要素のフォーカスが変更された場合:</span><span class="sxs-lookup"><span data-stu-id="4dfc3-123">When the element focus changes:</span></span>
  * <span data-ttu-id="4dfc3-124">`paragraphStyle` の値は、`<input>` 要素の値から割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="4dfc3-125">段落スタイルが更新され、`paragraphStyle` の新しいスタイルが反映されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="4dfc3-126">スタイルが `color:blue` に更新されると、テキストの色は **blue** に変わります。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="4dfc3-127">属性バインディングでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="4dfc3-128">`@bind` は有効です。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-128">`@bind` is valid.</span></span>
* <span data-ttu-id="4dfc3-129">`@Bind` および `@BIND` は無効です。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="4dfc3-130">解析不可能値</span><span class="sxs-lookup"><span data-stu-id="4dfc3-130">Unparsable values</span></span>

<span data-ttu-id="4dfc3-131">ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="4dfc3-132">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-132">Consider the following scenario:</span></span>

* <span data-ttu-id="4dfc3-133">`<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="4dfc3-134">ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="4dfc3-135">上のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="4dfc3-136">値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="4dfc3-137">既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="4dfc3-138">別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="4dfc3-139">`oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="4dfc3-140">`int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="4dfc3-141">`.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="4dfc3-142">`oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="4dfc3-143">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-143">Alternatives include:</span></span>

* <span data-ttu-id="4dfc3-144">`oninput` イベントは使用しません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="4dfc3-145">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="4dfc3-146">`int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="4dfc3-147"><xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> や <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="4dfc3-148">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="4dfc3-149">詳細については、「<xref:blazor/forms-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="4dfc3-150">フォーム検証コンポーネントを使うと、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-150">Form validation components:</span></span>
  * <span data-ttu-id="4dfc3-151">ユーザーの無効な入力を許可し、関連付けられた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証エラーを受信します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="4dfc3-152">ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="4dfc3-153">書式指定文字列</span><span class="sxs-lookup"><span data-stu-id="4dfc3-153">Format strings</span></span>

<span data-ttu-id="4dfc3-154">データ バインディングは、`@bind:format` を使用して <xref:System.DateTime> 形式の文字列を処理します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="4dfc3-155">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="4dfc3-156">上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="4dfc3-157">`@bind:format` は、次の .NET 型のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="4dfc3-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4dfc3-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="4dfc3-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4dfc3-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="4dfc3-160">`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="4dfc3-161">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="4dfc3-162">Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="4dfc3-163">この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく機能します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="4dfc3-164">コンポーネント パラメーターを使用した親から子へのバインディング</span><span class="sxs-lookup"><span data-stu-id="4dfc3-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="4dfc3-165">コンポーネント パラメーターを使用すると、`@bind-{PROPERTY OR FIELD}` 構文を使用して親コンポーネントのプロパティとフィールドをバインドできます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="4dfc3-166">次の `Child` コンポーネント (`Child.razor`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="4dfc3-167">コールバック (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) は、コンポーネント パラメーター名の後に "`Changed`" サフィックスを付けた名前 (`{PARAMETER NAME}Changed`) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="4dfc3-168">上の例では、コールバックの名前は `YearChanged` です。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="4dfc3-169"><xref:Microsoft.AspNetCore.Components.EventCallback%601> の詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="4dfc3-170">次の `Parent` コンポーネント (`Parent.razor`) では、`year` フィールドが子コンポーネントの `Year` パラメーターにバインドされています。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="4dfc3-171">`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="4dfc3-172">慣例により、ハンドラーに割り当てられた `@bind-{PROPERTY}:event` 属性を含めることで、プロパティを対応するイベント ハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="4dfc3-173">`<Child @bind-Year="year" />` は次のように書く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="4dfc3-174">チェーン バインドを使用した子から親へのバインディング</span><span class="sxs-lookup"><span data-stu-id="4dfc3-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="4dfc3-175">一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="4dfc3-176">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="4dfc3-177">チェーン バインドは、子コンポーネントで [`@bind`](xref:mvc/views/razor#bind) 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="4dfc3-178">イベント ハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="4dfc3-179">ただし、親コンポーネントでは、[`@bind`](xref:mvc/views/razor#bind) 構文を子コンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="4dfc3-180">次の `PasswordField` コンポーネント (`PasswordField.razor`) では、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="4dfc3-181">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="4dfc3-182">[`EventCallback`](xref:blazor/components/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="4dfc3-183">`onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="4dfc3-184">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="4dfc3-185">`PasswordField` コンポーネントは、別のコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="4dfc3-186">上の例で、パスワードの確認またはエラーの捕捉を行うには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="4dfc3-187">`Password` のバッキング フィールドを作成します (次のコード例では、`password`)。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="4dfc3-188">`Password` セッターで、確認またはエラーの捕捉を行います。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="4dfc3-189">次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="4dfc3-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="4dfc3-190">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4dfc3-190">Additional resources</span></span>

* [<span data-ttu-id="4dfc3-191">フォーム内のラジオ ボタンへのバインド</span><span class="sxs-lookup"><span data-stu-id="4dfc3-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="4dfc3-192">フォーム内の C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド</span><span class="sxs-lookup"><span data-stu-id="4dfc3-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
