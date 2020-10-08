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
ms.openlocfilehash: 0884b0bedd9ed31b8c85790c6950c7c5d63bdf44
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653907"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="3e9e2-103">ASP.NET Core Blazor データ バインディング</span><span class="sxs-lookup"><span data-stu-id="3e9e2-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="3e9e2-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="3e9e2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="3e9e2-105">Razor コンポーネントはフィールド、プロパティ、または Razor 式の値が含まれる "[`@bind`](xref:mvc/views/razor#bind)" という名前の HTML 要素属性を使用してデータ バインディング機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="3e9e2-106">次の例では、`<input>` 要素を `currentValue` フィールドにバインドし、`<input>` 要素を `CurrentValue` プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="3e9e2-107">いずれかの要素がフォーカスを失うと、バインドされたフィールドまたはプロパティが更新されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="3e9e2-108">テキスト ボックスは、フィールドまたはプロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="3e9e2-109">コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、フィールドとプロパティの更新が UI に反映されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="3e9e2-110">`CurrentValue` プロパティで [`@bind`](xref:mvc/views/razor#bind) を使用する (`<input @bind="CurrentValue" />`) ことは、本質的に次の内容と同じです。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="3e9e2-111">コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="3e9e2-112">ユーザーがテキスト ボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="3e9e2-113">実際には、[`@bind`](xref:mvc/views/razor#bind) は型変換が行われるケースを扱うため、コード生成はより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="3e9e2-114">原則として、[`@bind`](xref:mvc/views/razor#bind) は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="3e9e2-115">他のイベントのプロパティまたはフィールドをバインドするには、`event` パラメーターを含めた `@bind:event` 属性を含めます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="3e9e2-116">次の例は、`CurrentValue` プロパティを `oninput` イベントにバインドします。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="3e9e2-117">要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキスト ボックスの値が変更されたときに `oninput` が発生します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="3e9e2-118">属性バインディングでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="3e9e2-119">`@bind` は有効です。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-119">`@bind` is valid.</span></span>
* <span data-ttu-id="3e9e2-120">`@Bind` および `@BIND` は無効です。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="3e9e2-121">解析不可能値</span><span class="sxs-lookup"><span data-stu-id="3e9e2-121">Unparsable values</span></span>

<span data-ttu-id="3e9e2-122">ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="3e9e2-123">次のシナリオについて検討してください。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-123">Consider the following scenario:</span></span>

* <span data-ttu-id="3e9e2-124">`<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="3e9e2-125">ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="3e9e2-126">上のシナリオでは、要素の値が `123`に戻されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="3e9e2-127">値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="3e9e2-128">既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="3e9e2-129">別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="3e9e2-130">`oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="3e9e2-131">`int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="3e9e2-132">`.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="3e9e2-133">`oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="3e9e2-134">代替手段は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-134">Alternatives include:</span></span>

* <span data-ttu-id="3e9e2-135">`oninput` イベントは使用しません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="3e9e2-136">既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="3e9e2-137">`int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="3e9e2-138"><xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> や <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="3e9e2-139">フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="3e9e2-140">詳細については、「<xref:blazor/forms-validation>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="3e9e2-141">フォーム検証コンポーネントを使うと、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-141">Form validation components:</span></span>
  * <span data-ttu-id="3e9e2-142">ユーザーの無効な入力を許可し、関連付けられた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証エラーを受信します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="3e9e2-143">ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="3e9e2-144">書式指定文字列</span><span class="sxs-lookup"><span data-stu-id="3e9e2-144">Format strings</span></span>

<span data-ttu-id="3e9e2-145">データ バインディングは、`@bind:format` を使用して <xref:System.DateTime> 形式の文字列を処理します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="3e9e2-146">通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="3e9e2-147">上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="3e9e2-148">`@bind:format` は、次の .NET 型のバインドをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="3e9e2-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="3e9e2-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="3e9e2-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="3e9e2-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="3e9e2-151">`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="3e9e2-152">この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="3e9e2-153">Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="3e9e2-154">この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく機能します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="3e9e2-155">コンポーネント パラメーターを使用した親から子へのバインディング</span><span class="sxs-lookup"><span data-stu-id="3e9e2-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="3e9e2-156">コンポーネント パラメーターを使用すると、`@bind-{PROPERTY OR FIELD}` 構文を使用して親コンポーネントのプロパティとフィールドをバインドできます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="3e9e2-157">次の `Child` コンポーネント (`Shared/Child.razor`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-157">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="3e9e2-158">コールバック (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) は、コンポーネント パラメーター名の後に "`Changed`" サフィックスを付けた名前 (`{PARAMETER NAME}Changed`) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="3e9e2-159">上の例では、コールバックの名前は `YearChanged` です。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="3e9e2-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601> の詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="3e9e2-161">次の `Parent` コンポーネント (`Parent.razor`) では、`year` フィールドが子コンポーネントの `Year` パラメーターにバインドされています。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="3e9e2-162">`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="3e9e2-163">慣例により、ハンドラーに割り当てられた `@bind-{PROPERTY}:event` 属性を含めることで、プロパティを対応するイベント ハンドラーにバインドできます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="3e9e2-164">`<Child @bind-Year="year" />` は次のように書く場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="3e9e2-165">チェーン バインドを使用した子から親へのバインディング</span><span class="sxs-lookup"><span data-stu-id="3e9e2-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="3e9e2-166">一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="3e9e2-167">このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="3e9e2-168">チェーン バインドは、子コンポーネントで [`@bind`](xref:mvc/views/razor#bind) 構文を使用して実装することはできません。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="3e9e2-169">イベント ハンドラーと値は、個別に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="3e9e2-170">ただし、親コンポーネントでは、[`@bind`](xref:mvc/views/razor#bind) 構文を子コンポーネントのパラメーターと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="3e9e2-171">次の `PasswordField` コンポーネント (`PasswordField.razor`) では、次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="3e9e2-172">`password` フィールドに `<input>` 要素の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-172">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="3e9e2-173">子の `password` フィールドの現在の値を引数として渡す [`EventCallback`](xref:blazor/components/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-173">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="3e9e2-174">`onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="3e9e2-175">詳細については、「<xref:blazor/components/event-handling>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="3e9e2-176">`PasswordField` コンポーネントは、別のコンポーネントで使用されます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="3e9e2-177">バインドのデリゲートを呼び出すメソッドで、チェックを実行またはエラーをトラップします。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-177">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="3e9e2-178">次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-178">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();
        
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="3e9e2-179">3 つ以上のコンポーネント間でバインドする</span><span class="sxs-lookup"><span data-stu-id="3e9e2-179">Bind across more than two components</span></span>

<span data-ttu-id="3e9e2-180">入れ子になった任意の数のコンポーネントをバインドできますが、次のような一方向のデータ フローを考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="3e9e2-181">変更通知は "*階層をフローアップ*" します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="3e9e2-182">新しいパラメーター値は "*階層をフローダウン*" します。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="3e9e2-183">一般的な推奨される方法は、基になるデータを親コンポーネントに格納のみすることであり、これにより、更新する必要がある状態に関する混乱を避けることができます。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="3e9e2-184">次のコンポーネントは、前述の概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="3e9e2-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="3e9e2-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="3e9e2-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="3e9e2-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="3e9e2-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="3e9e2-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="3e9e2-188">必ずしも入れ子にはなっていないコンポーネント間でメモリ内のデータを共有するのに適した別の方法については、「ASP.NET Core Blazor 状態管理<xref:blazor/state-management#in-memory-state-container-service>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3e9e2-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e9e2-189">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3e9e2-189">Additional resources</span></span>

* [<span data-ttu-id="3e9e2-190">フォーム内のラジオ ボタンへのバインド</span><span class="sxs-lookup"><span data-stu-id="3e9e2-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="3e9e2-191">フォーム内の C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド</span><span class="sxs-lookup"><span data-stu-id="3e9e2-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
