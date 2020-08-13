---
title: ASP.NET Core Blazor データ バインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディング機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
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
ms.openlocfilehash: 6f5ad6b8f225834c92d6e33d8bcf608b56678e67
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014673"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="612f0-103">ASP.NET Core Blazor データ バインディング</span><span class="sxs-lookup"><span data-stu-id="612f0-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="612f0-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="612f0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="612f0-105">Razor コンポーネントはフィールド、プロパティ、または Razor 式の値が含まれる "[`@bind`](xref:mvc/views/razor#bind)" という名前の HTML 要素属性を使用してデータ バインディング機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="612f0-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="612f0-106">次の例では、`CurrentValue` プロパティをテキスト ボックスの値にバインドします。</span><span class="sxs-lookup"><span data-stu-id="612f0-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="612f0-107">テキスト ボックスがフォーカスを失うと、プロパティの値が更新されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="612f0-108">テキスト ボックスは、プロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="612f0-109">コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="612f0-110">`CurrentValue` プロパティで [`@bind`](xref:mvc/views/razor#bind) を使用する (`<input @bind="CurrentValue" />`) ことは、本質的に次の内容と同じです。</span><span class="sxs-lookup"><span data-stu-id="612f0-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="612f0-111">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="612f0-112">ユーザーがテキスト ボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="612f0-113">実際には、[`@bind`](xref:mvc/views/razor#bind) は型変換が行われるケースを扱うため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="612f0-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="612f0-114">原則として、[`@bind`](xref:mvc/views/razor#bind) は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="612f0-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="612f0-115">他のイベントのプロパティまたはフィールドをバインドするには、`event` パラメーターを含めた `@bind:event` 属性を含めます。</span><span class="sxs-lookup"><span data-stu-id="612f0-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="612f0-116">次の例は、`CurrentValue` プロパティを `oninput` イベントにバインドします。</span><span class="sxs-lookup"><span data-stu-id="612f0-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="612f0-117">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキスト ボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="612f0-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="612f0-118">`value` 以外の要素の属性をバインドするには、`@bind-{ATTRIBUTE}:event` 構文で `@bind-{ATTRIBUTE}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="612f0-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="612f0-119">次の例では、`paragraphStyle` の値が変更されると段落のスタイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

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

<span data-ttu-id="612f0-120">属性バインディングでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="612f0-121">`@bind` は有効です。</span><span class="sxs-lookup"><span data-stu-id="612f0-121">`@bind` is valid.</span></span>
* <span data-ttu-id="612f0-122">`@Bind` および `@BIND` は無効です。</span><span class="sxs-lookup"><span data-stu-id="612f0-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="612f0-123">解析不可能値</span><span class="sxs-lookup"><span data-stu-id="612f0-123">Unparsable values</span></span>

<span data-ttu-id="612f0-124">ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="612f0-125">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="612f0-125">Consider the following scenario:</span></span>

* <span data-ttu-id="612f0-126">`<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="612f0-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="612f0-127">ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="612f0-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="612f0-128">上のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="612f0-129">値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。</span><span class="sxs-lookup"><span data-stu-id="612f0-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="612f0-130">既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="612f0-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="612f0-131">別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="612f0-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="612f0-132">`oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="612f0-133">`int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="612f0-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="612f0-134">`.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="612f0-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="612f0-135">`oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。</span><span class="sxs-lookup"><span data-stu-id="612f0-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="612f0-136">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="612f0-136">Alternatives include:</span></span>

* <span data-ttu-id="612f0-137">`oninput` イベントは使用しません。</span><span class="sxs-lookup"><span data-stu-id="612f0-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="612f0-138">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="612f0-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="612f0-139">`int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="612f0-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="612f0-140"><xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> や <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="612f0-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="612f0-141">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="612f0-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="612f0-142">フォーム検証コンポーネントを使うと、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="612f0-142">Form validation components:</span></span>
  * <span data-ttu-id="612f0-143">ユーザーの無効な入力を許可し、関連付けられた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証エラーを受信します。</span><span class="sxs-lookup"><span data-stu-id="612f0-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="612f0-144">ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="612f0-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="612f0-145">書式指定文字列</span><span class="sxs-lookup"><span data-stu-id="612f0-145">Format strings</span></span>

<span data-ttu-id="612f0-146">データ バインディングは、`@bind:format` を使用して <xref:System.DateTime> 形式の文字列を処理します。</span><span class="sxs-lookup"><span data-stu-id="612f0-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="612f0-147">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="612f0-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="612f0-148">上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="612f0-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="612f0-149">`@bind:format` は、次の .NET 型のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="612f0-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="612f0-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="612f0-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="612f0-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="612f0-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="612f0-152">`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="612f0-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="612f0-153">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="612f0-154">Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="612f0-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="612f0-155">この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく動作します。</span><span class="sxs-lookup"><span data-stu-id="612f0-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="612f0-156">コンポーネント パラメーターを使用した親から子へのバインディング</span><span class="sxs-lookup"><span data-stu-id="612f0-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="612f0-157">バインディングは、コンポーネントのパラメーターを認識します。`@bind-{PROPERTY}` は、親コンポーネントのプロパティ値を子コンポーネントにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="612f0-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="612f0-158">子から親へのバインドについては、「[チェーン バインドを使用した子から親へのバインディング](#child-to-parent-binding-with-chained-bind)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="612f0-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="612f0-159">次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="612f0-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="612f0-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601> は、コンポーネント パラメーター名の後に `Changed` サフィックス (`{PARAMETER NAME}Changed`) を付けた名前にする必要があります (前の例では `YearChanged`)。</span><span class="sxs-lookup"><span data-stu-id="612f0-160">The <xref:Microsoft.AspNetCore.Components.EventCallback%601> must be named as the component parameter name followed by the `Changed` suffix (`{PARAMETER NAME}Changed`), `YearChanged` in the preceding example.</span></span> <span data-ttu-id="612f0-161"><xref:Microsoft.AspNetCore.Components.EventCallback%601> の詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="612f0-161">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="612f0-162">次の親コンポーネントは、以下を使用します。</span><span class="sxs-lookup"><span data-stu-id="612f0-162">The following parent component uses:</span></span>

* <span data-ttu-id="612f0-163">`ChildComponent` を使用して親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。</span><span class="sxs-lookup"><span data-stu-id="612f0-163">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="612f0-164">`onclick` イベントは、`ChangeTheYear` メソッドをトリガーするために使用します。</span><span class="sxs-lookup"><span data-stu-id="612f0-164">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="612f0-165">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="612f0-165">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="612f0-166">`ParentComponent` を読み込むと、次のマークアップが生成されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-166">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="612f0-167">`ParentComponent` のボタンを選択して `ParentYear` プロパティの値を変更すると、`ChildComponent` の `Year` プロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-167">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="612f0-168">`Year` の新しい値は、`ParentComponent` が再度レンダリングされたときに UI に表示されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-168">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="612f0-169">`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="612f0-169">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="612f0-170">慣例として、`<ChildComponent @bind-Year="ParentYear" />` は基本的に以下の書き込みと同じです。</span><span class="sxs-lookup"><span data-stu-id="612f0-170">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="612f0-171">一般に、プロパティに `@bind-{PROPRETY}:event` 属性を含めることで、対応するイベント ハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="612f0-171">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="612f0-172">たとえば、プロパティ `MyProp` は、次の 2 つの属性を使用して `MyEventHandler` にバインドできます。</span><span class="sxs-lookup"><span data-stu-id="612f0-172">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="612f0-173">チェーン バインドを使用した子から親へのバインディング</span><span class="sxs-lookup"><span data-stu-id="612f0-173">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="612f0-174">一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="612f0-174">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="612f0-175">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="612f0-175">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="612f0-176">チェーン バインドは、ページの要素で [`@bind`](xref:mvc/views/razor#bind) 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="612f0-176">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="612f0-177">イベント ハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="612f0-177">The event handler and value must be specified separately.</span></span> <span data-ttu-id="612f0-178">ただし、親コンポーネントでは、[`@bind`](xref:mvc/views/razor#bind) 構文をコンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="612f0-178">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="612f0-179">次の `PasswordField` コンポーネント (`PasswordField.razor`) では、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="612f0-179">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="612f0-180">`Password` プロパティに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="612f0-180">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="612f0-181">[`EventCallback`](xref:blazor/components/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="612f0-181">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="612f0-182">`onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="612f0-182">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="612f0-183">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="612f0-183">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="612f0-184">`PasswordField` コンポーネントは、別のコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="612f0-184">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="612f0-185">上の例で、パスワードの確認またはエラーの捕捉を行うには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="612f0-185">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="612f0-186">`Password` のバッキング フィールドを作成します (次のコード例では、`password`)。</span><span class="sxs-lookup"><span data-stu-id="612f0-186">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="612f0-187">`Password` セッターで、確認またはエラーの捕捉を行います。</span><span class="sxs-lookup"><span data-stu-id="612f0-187">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="612f0-188">次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="612f0-188">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="612f0-189">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="612f0-189">Additional resources</span></span>

* [<span data-ttu-id="612f0-190">フォーム内のラジオ ボタンへのバインド</span><span class="sxs-lookup"><span data-stu-id="612f0-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="612f0-191">フォーム内の C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド</span><span class="sxs-lookup"><span data-stu-id="612f0-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
