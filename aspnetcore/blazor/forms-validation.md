---
title: ASP.NET Core Blazor のフォームと検証
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: 3e719261315ed3a17833da7751d801d79a11ee6c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014479"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="2fc3b-103">ASP.NET Core Blazor のフォームと検証</span><span class="sxs-lookup"><span data-stu-id="2fc3b-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="2fc3b-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2fc3b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2fc3b-105">フォームと検証は、Blazor で[データ注釈](xref:mvc/models/validation)を使用してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="2fc3b-106">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="2fc3b-107">フォームは、<xref:Microsoft.AspNetCore.Components.Forms.EditForm> コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="2fc3b-108">次のフォームは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="2fc3b-109">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-109">In the preceding example:</span></span>

* <span data-ttu-id="2fc3b-110">フォームは、`ExampleModel` 型で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="2fc3b-111">モデルはコンポーネントの `@code` ブロック内に作成され、プライベート フィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="2fc3b-112">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="2fc3b-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `@bind-Value` は次のようにバインドします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="2fc3b-114">モデル プロパティ (`exampleModel.Name`) を <xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `Value` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="2fc3b-115">プロパティのバインドの詳細については、<xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="2fc3b-116">変更イベント デリゲートを <xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントの `ValueChanged` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="2fc3b-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントは、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="2fc3b-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="2fc3b-119">`HandleValidSubmit` は、フォームが正常に送信される (検証に合格する) とトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="2fc3b-120">組み込みフォームのコンポーネント</span><span class="sxs-lookup"><span data-stu-id="2fc3b-120">Built-in forms components</span></span>

<span data-ttu-id="2fc3b-121">一連の組み込みの入力コンポーネントを、ユーザー入力の受信と検証に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-121">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="2fc3b-122">入力は、それらが変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="2fc3b-123">次の表に、使用できる入力コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-123">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="2fc3b-124">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2fc3b-124">Input component</span></span> | <span data-ttu-id="2fc3b-125">&hellip; とレンダリング</span><span class="sxs-lookup"><span data-stu-id="2fc3b-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="2fc3b-126">すべての入力コンポーネント (<xref:Microsoft.AspNetCore.Components.Forms.EditForm> を含む) で、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-126">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="2fc3b-127">コンポーネント パラメーターに一致しない属性は、レンダリングされる HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-127">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="2fc3b-128">入力コンポーネントは、編集時に検証し、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-128">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="2fc3b-129">一部のコンポーネントには、便利な解析ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-129">Some components include useful parsing logic.</span></span> <span data-ttu-id="2fc3b-130">たとえば、<xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> と <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> では、解析不能な値を検証エラーとして登録することによって、それらを適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-130">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="2fc3b-131">NULL 値を受け入れることができる型では、ターゲット フィールドの NULL 値の許容もサポートしています (`int?` など)。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-131">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="2fc3b-132">次の `Starship` 型では、以前の `ExampleModel` よりも大きなプロパティとデータ注釈のセットを使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-132">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="2fc3b-133">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-133">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="2fc3b-134">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-134">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="2fc3b-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components/cascading-values-and-parameters) として <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を作成します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="2fc3b-136"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、有効な送信と無効な送信用の便利なイベント (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>) も提供しています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-136">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="2fc3b-137">または、<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> を使用して、カスタム検証コードで検証をトリガーし、フィールド値をチェックします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-137">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="2fc3b-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-138">In the following example:</span></span>

* <span data-ttu-id="2fc3b-139">**`Submit`** ボタンが選択されると、`HandleSubmit` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-139">The `HandleSubmit` method runs when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="2fc3b-140">フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を使用して、フォームが検証されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-140">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="2fc3b-141">このフォームをさらに検証するには、サーバーで Web API エンドポイントを呼び出す `ServerValidate` メソッドに <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を渡します (*示されていません*)。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-141">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="2fc3b-142">`isValid` をチェックすることによって、クライアント側とサーバー側の検証の結果に応じて、追加のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-142">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="2fc3b-143">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="2fc3b-143">InputText based on the input event</span></span>

<span data-ttu-id="2fc3b-144">`change` イベントではなく、`input` イベントを使用するカスタム コンポーネントを作成するには、<xref:Microsoft.AspNetCore.Components.Forms.InputText> コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-144">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="2fc3b-145">次の例では、`CustomInputText` コンポーネントでフレームワークの `InputText` コンポーネントが継承され、イベント バインディング (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) が `oninput` イベントに設定されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-145">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="2fc3b-146">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-146">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="2fc3b-147">`CustomInputText` コンポーネントは、<xref:Microsoft.AspNetCore.Components.Forms.InputText> が使用される場所であればどこでも使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-147">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="2fc3b-148">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-148">`Pages/TestForm.razor`:</span></span>

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="2fc3b-149">ラジオ ボタン</span><span class="sxs-lookup"><span data-stu-id="2fc3b-149">Radio buttons</span></span>

<span data-ttu-id="2fc3b-150">フォームでオプション ボタンを使用する場合、オプション ボタンはグループとして評価されるため、データ バインディングが他の要素と異なる方法で処理されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-150">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="2fc3b-151">各オプション ボタンの値は固定ですが、オプション ボタン グループの値は、選択されたオプション ボタンの値です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-151">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="2fc3b-152">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-152">The following example shows how to:</span></span>

* <span data-ttu-id="2fc3b-153">オプション ボタン グループのデータバインディングを処理する。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-153">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="2fc3b-154">カスタム `InputRadio` コンポーネントを使用した検証をサポートする。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-154">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="2fc3b-155">次の <xref:Microsoft.AspNetCore.Components.Forms.EditForm> では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証しています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-155">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="2fc3b-156">C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド</span><span class="sxs-lookup"><span data-stu-id="2fc3b-156">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="2fc3b-157">次の理由により、`<select>` 要素のオプション値を C# オブジェクトの `null` 値として表すよい方法はありません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-157">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="2fc3b-158">HTML 属性には `null` 値を設定できません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-158">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="2fc3b-159">HTML で `null` に最も近いのは、`<option>` 要素から HTML の `value` 属性を除去することです。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-159">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="2fc3b-160">`value` 属性のない `<option>` を選択すると、ブラウザーではその `<option>` の要素の "*テキスト コンテンツ*" として値が扱われます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-160">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="2fc3b-161">Blazor フレームワークでは、次の処理が必要になるため、既定の動作の抑制は試みられません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-161">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="2fc3b-162">フレームワークでの特殊なケースの回避策のチェーンの作成。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-162">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="2fc3b-163">現在のフレームワークの動作に対する破壊的変更。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-163">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2fc3b-164">HTML で `null` に最も近いと思われるものは、"*空の文字列*" の `value` です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-164">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="2fc3b-165">Blazor フレームワークでは、`<select>` の値への双方向バインドに対する空の文字列変換として `null` が処理されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-165">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="2fc3b-166">Blazor フレームワークでは、`<select>` の値への双方向バインドを試みるときに、`null` は空の文字列変換として自動的に処理されません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-166">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="2fc3b-167">詳細については、「[null 値への `<select>` のバインドの修正 (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-167">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="2fc3b-168">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="2fc3b-168">Validation support</span></span>

<span data-ttu-id="2fc3b-169"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントは、データ注釈を使用した検証サポートをカスケードされた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-169">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="2fc3b-170">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-170">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="2fc3b-171">データ注釈と異なる検証システムを使用するには、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-171">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="2fc3b-172">参照ソース [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) での検査に、ASP.NET Core 実装を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-172">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="2fc3b-173">上記の参照ソースへのリンクからは、リポジトリの `master` ブランチが提供されます。このブランチは、ASP.NET Core の次回リリースのための製品単位の現行開発を表します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-173">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="2fc3b-174">別のリリースのブランチを選択するには、GitHub ブランチ セレクターを使用します (`release/3.1` など)。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-174">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="2fc3b-175">Blazor は 2 種類の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-175">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="2fc3b-176">*フィールド検証* は、ユーザーがタブでフィールドを離れたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-176">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="2fc3b-177">フィールドの検証時に、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-177">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="2fc3b-178">*モデル検証*は、ユーザーがフォームを送信したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-178">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="2fc3b-179">モデルの検証時に、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントは、検証結果で報告されたメンバー名に基づいてフィールドを判断しようとします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-179">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="2fc3b-180">個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-180">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="2fc3b-181">検証概要コンポーネントと検証メッセージ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2fc3b-181">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="2fc3b-182"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントは、すべての検証メッセージを要約します。これは[検証概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)と似ています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-182">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="2fc3b-183">`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-183">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="2fc3b-184"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> コンポーネントは、特定のフィールドの検証メッセージを表示します。これは、[検証メッセージ タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似ています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-184">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="2fc3b-185">`For` 属性と、モデル プロパティに名前を付けるラムダ式で、検証するフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-185">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="2fc3b-186"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> コンポーネントと <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントでは、任意の属性をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-186">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="2fc3b-187">コンポーネント パラメーターに一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-187">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="2fc3b-188">アプリのスタイルシート (`wwwroot/css/app.css` または `wwwroot/css/site.css`) での検証メッセージのスタイルを制御します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-188">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="2fc3b-189">既定の `validation-message` クラスでは、検証メッセージのテキストの色が赤に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-189">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="2fc3b-190">カスタム検証属性</span><span class="sxs-lookup"><span data-stu-id="2fc3b-190">Custom validation attributes</span></span>

<span data-ttu-id="2fc3b-191">[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに、検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult> の作成時に検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-191">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="2fc3b-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> が `null`です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-192"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="2fc3b-193">`IsValid` メソッドでの検証に対する挿入サービスはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-193">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="2fc3b-194">Blazor データ注釈検証パッケージ</span><span class="sxs-lookup"><span data-stu-id="2fc3b-194">Blazor data annotations validation package</span></span>

<span data-ttu-id="2fc3b-195">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) は、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントを使用して検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-195">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="2fc3b-196">パッケージは現在、*試験段階*です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-196">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="2fc3b-197">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="2fc3b-197">[CompareProperty] attribute</span></span>

<span data-ttu-id="2fc3b-198"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果を特定のメンバーに関連付けないため、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> コンポーネントで正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-198">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="2fc3b-199">これにより、フィールドレベルの検証と、送信時のモデル全体が検証されたときの動作に一貫性がなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-199">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="2fc3b-200">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) "*試験的*" パッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-200">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="2fc3b-201">Blazor アプリでは、`[CompareProperty]` は [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) 属性の直接の代わりとなるものです。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-201">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="2fc3b-202">入れ子になったモデル、コレクション型、および複合型</span><span class="sxs-lookup"><span data-stu-id="2fc3b-202">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="2fc3b-203">Blazor では、組み込みの <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> によるデータ注釈を使用したフォーム入力の検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-203">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="2fc3b-204">ただし、<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>で は、コレクション型または複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみが検証されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-204">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="2fc3b-205">コレクション型と複合型のプロパティを含む、バインドされたモデルのオブジェクト グラフ全体を検証するには、"*試験的*" [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) パッケージによって提供される `ObjectGraphDataAnnotationsValidator` を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-205">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="2fc3b-206">`[ValidateComplexType]` でモデルのプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-206">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="2fc3b-207">次のモデル クラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-207">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="2fc3b-208">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-208">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="2fc3b-209">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-209">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="2fc3b-210">フォームの検証に基づいて送信ボタンを有効にする</span><span class="sxs-lookup"><span data-stu-id="2fc3b-210">Enable the submit button based on form validation</span></span>

<span data-ttu-id="2fc3b-211">フォームの検証に基づいて送信ボタンを有効または無効にするには:</span><span class="sxs-lookup"><span data-stu-id="2fc3b-211">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="2fc3b-212">コンポーネントを初期化するときに、フォームの <xref:Microsoft.AspNetCore.Components.Forms.EditContext> を使用してモデルを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-212">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="2fc3b-213">コンテキストの <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> コールバックでフォームを検証して、送信ボタンを有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-213">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="2fc3b-214">`Dispose` メソッドでイベント ハンドラーをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-214">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="2fc3b-215">詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-215">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="2fc3b-216">前の例では、次の場合に `formInvalid` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-216">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="2fc3b-217">フォームには、有効な既定値が事前に読み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-217">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="2fc3b-218">フォームが読み込まれるときに、送信ボタンを有効にしたいと考えます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-218">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="2fc3b-219">上記の方法の副作用として、ユーザーがいずれかのフィールドを操作した後に、<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントに無効なフィールドが設定されます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-219">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="2fc3b-220">このシナリオは、次のいずれかの方法で対処できます。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-220">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="2fc3b-221">フォームでは <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-221">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="2fc3b-222">送信ボタンが選択された (たとえば、`HandleValidSubmit` メソッドで) ときに <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> コンポーネントを表示します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-222">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="2fc3b-223">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2fc3b-223">Troubleshoot</span></span>

> <span data-ttu-id="2fc3b-224">InvalidOperationException:EditForm には、Model パラメーターまたは EditContext パラメーター (両方ではなく) が必要です。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-224">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="2fc3b-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> に <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> または <xref:Microsoft.AspNetCore.Components.Forms.EditContext> が指定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-225">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="2fc3b-226">フォームに <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> を割り当てるときは、次の例に示すように、そのモデルの種類がインスタンス化されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fc3b-226">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
