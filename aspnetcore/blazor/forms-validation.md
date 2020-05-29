---
title: ASP.NET Core Blazor のフォームと検証
author: guardrex
description: Blazor でフォームとフィールドの検証シナリオを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: d7182594fbc22d056caff0864a053a0a92fa4e84
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438890"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="bd2f5-103">ASP.NET Core Blazor のフォームと検証</span><span class="sxs-lookup"><span data-stu-id="bd2f5-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="bd2f5-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd2f5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd2f5-105">フォームと検証は、Blazor で[データ注釈](xref:mvc/models/validation)を使用してサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="bd2f5-106">次の `ExampleModel` 型は、データ注釈を使用して検証ロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="bd2f5-107">フォームは、`EditForm` コンポーネントを使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="bd2f5-108">次のフォームでは、一般的な要素、コンポーネント、および Razor コードを示しています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="bd2f5-109">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="bd2f5-109">In the preceding example:</span></span>

* <span data-ttu-id="bd2f5-110">フォームは、`ExampleModel` 型で定義されている検証を使用して、`name` フィールドのユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="bd2f5-111">モデルはコンポーネントの `@code` ブロック内に作成され、プライベート フィールド (`exampleModel`) に保持されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="bd2f5-112">フィールドは、`<EditForm>` 要素の `Model` 属性に割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="bd2f5-113">`InputText` コンポーネントの `@bind-Value` は次のようにバインドします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="bd2f5-114">モデル プロパティ (`exampleModel.Name`) を `InputText` コンポーネントの `Value` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-114">The model property (`exampleModel.Name`) to the `InputText` component's `Value` property.</span></span> <span data-ttu-id="bd2f5-115">プロパティのバインドの詳細については、<xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-115">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="bd2f5-116">変更イベント デリゲートを `InputText` コンポーネントの `ValueChanged` プロパティへ。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-116">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="bd2f5-117">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用して検証サポートをアタッチします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-117">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="bd2f5-118">`ValidationSummary` コンポーネントは、検証メッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-118">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="bd2f5-119">`HandleValidSubmit` は、フォームが正常に送信される (検証に合格する) とトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="bd2f5-120">一連の組み込みの入力コンポーネントを、ユーザー入力の受信と検証に使用できます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="bd2f5-121">入力は、それらが変更されたときとフォームが送信されたときに検証されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="bd2f5-122">次の表に、使用できる入力コンポーネントを示しています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="bd2f5-123">入力コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bd2f5-123">Input component</span></span> | <span data-ttu-id="bd2f5-124">&hellip; とレンダリング</span><span class="sxs-lookup"><span data-stu-id="bd2f5-124">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="bd2f5-125">すべての入力コンポーネント (`EditForm` を含む) で、任意の属性がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-125">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="bd2f5-126">コンポーネント パラメーターに一致しない属性は、レンダリングされる HTML 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="bd2f5-127">入力コンポーネントは、編集時に検証し、フィールドの状態を反映するように CSS クラスを変更するための既定の動作を提供します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="bd2f5-128">一部のコンポーネントには、便利な解析ロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="bd2f5-129">たとえば、`InputDate` と `InputNumber` では、解析不能な値を検証エラーとして登録することによって、それらを適切に処理します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-129">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="bd2f5-130">NULL 値を受け入れることができる型では、ターゲット フィールドの NULL 値の許容もサポートしています (`int?` など)。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="bd2f5-131">次の `Starship` 型では、以前の `ExampleModel` よりも大きなプロパティとデータ注釈のセットを使用して検証ロジックを定義しています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="bd2f5-132">前の例では、データ注釈が存在しないため、`Description` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="bd2f5-133">次のフォームでは、`Starship` モデルで定義されている検証を使用してユーザー入力を検証します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="bd2f5-134">`EditForm` では、変更されたフィールドと現在の検証メッセージを含む、編集プロセスに関するメタデータを追跡する[カスケード値](xref:blazor/components#cascading-values-and-parameters) として `EditContext` を作成します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-134">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="bd2f5-135">`EditForm` では、有効な送信と無効な送信用の便利なイベント (`OnValidSubmit`、`OnInvalidSubmit`) も提供しています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-135">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="bd2f5-136">または、`OnSubmit` を使用して、カスタム検証コードで検証をトリガーし、フィールド値をチェックします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-136">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="bd2f5-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-137">In the following example:</span></span>

* <span data-ttu-id="bd2f5-138">**[送信]** ボタンが選択されると、`HandleSubmit` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-138">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="bd2f5-139">フォームの `EditContext` を使用して、フォームが検証されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-139">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="bd2f5-140">このフォームをさらに検証するには、サーバーで Web API エンドポイントを呼び出す `ServerValidate` メソッドに `EditContext` を渡します (*示されていません*)。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-140">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="bd2f5-141">`isValid` をチェックすることによって、クライアント側とサーバー側の検証の結果に応じて、追加のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="bd2f5-142">入力イベントに基づく InputText</span><span class="sxs-lookup"><span data-stu-id="bd2f5-142">InputText based on the input event</span></span>

<span data-ttu-id="bd2f5-143">`change` イベントではなく、`input` イベントを使用するカスタム コンポーネントを作成するには、`InputText` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-143">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="bd2f5-144">次のマークアップでコンポーネントを作成し、`InputText` を使用する場合と同様に、コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-144">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="bd2f5-145">オプション ボタンの操作</span><span class="sxs-lookup"><span data-stu-id="bd2f5-145">Work with radio buttons</span></span>

<span data-ttu-id="bd2f5-146">フォームでオプション ボタンを使用する場合、オプション ボタンはグループとして評価されるため、データ バインディングが他の要素と異なる方法で処理されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-146">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="bd2f5-147">各オプション ボタンの値は固定ですが、オプション ボタン グループの値は、選択されたオプション ボタンの値です。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-147">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="bd2f5-148">以下の例では、次のことを行っています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-148">The following example shows how to:</span></span>

* <span data-ttu-id="bd2f5-149">オプション ボタン グループのデータバインディングを処理する。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-149">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="bd2f5-150">カスタム `InputRadio` コンポーネントを使用した検証をサポートする。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-150">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="bd2f5-151">次の `EditForm` では、前の `InputRadio` コンポーネントを使用して、ユーザーから評価を取得して検証しています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-151">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="bd2f5-152">検証のサポート</span><span class="sxs-lookup"><span data-stu-id="bd2f5-152">Validation support</span></span>

<span data-ttu-id="bd2f5-153">`DataAnnotationsValidator` コンポーネントは、データ注釈を使用した検証サポートをカスケードされた `EditContext` にアタッチします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-153">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="bd2f5-154">データ注釈を使用した検証のサポートを有効にするには、この明示的なジェスチャが必要です。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-154">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="bd2f5-155">データ注釈と異なる検証システムを使用するには、`DataAnnotationsValidator` をカスタム実装に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-155">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="bd2f5-156">参照ソースでの検査に、ASP.NET Core 実装を使用できます。[DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="bd2f5-156">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="bd2f5-157"> は 2 種類の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-157"> performs two types of validation:</span></span>

* <span data-ttu-id="bd2f5-158">*フィールド検証* は、ユーザーがタブでフィールドを離れたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-158">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="bd2f5-159">フィールドの検証時に、`DataAnnotationsValidator` コンポーネントによって、報告されたすべての検証結果がフィールドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-159">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="bd2f5-160">*モデル検証*は、ユーザーがフォームを送信したときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-160">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="bd2f5-161">モデルの検証時に、`DataAnnotationsValidator` コンポーネントは、検証結果で報告されたメンバー名に基づいてフィールドを判断しようとします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-161">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="bd2f5-162">個々のメンバーに関連付けられていない検証結果は、フィールドではなくモデルに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-162">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="bd2f5-163">検証概要コンポーネントと検証メッセージ コンポーネント</span><span class="sxs-lookup"><span data-stu-id="bd2f5-163">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="bd2f5-164">`ValidationSummary` コンポーネントは、すべての検証メッセージを要約します。これは[検証概要タグヘルパー](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)と似ています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-164">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="bd2f5-165">`Model` パラメーターを使用して、特定のモデルの検証メッセージを出力します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-165">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="bd2f5-166">`ValidationMessage` コンポーネントは、特定のフィールドの検証メッセージを表示します。これは、[検証メッセージ タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)に似ています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-166">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="bd2f5-167">`For` 属性と、モデル プロパティに名前を付けるラムダ式で、検証するフィールドを指定します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-167">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="bd2f5-168">`ValidationMessage` コンポーネントと `ValidationSummary` コンポーネントでは、任意の属性をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-168">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="bd2f5-169">コンポーネント パラメーターに一致しない属性は、生成された `<div>` 要素または `<ul>` 要素に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-169">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="bd2f5-170">カスタム検証属性</span><span class="sxs-lookup"><span data-stu-id="bd2f5-170">Custom validation attributes</span></span>

<span data-ttu-id="bd2f5-171">[カスタム検証属性](xref:mvc/models/validation#custom-attributes)を使用するときに、検証結果がフィールドに正しく関連付けられるようにするには、<xref:System.ComponentModel.DataAnnotations.ValidationResult> の作成時に検証コンテキストの <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> を渡します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-171">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="bd2f5-172"> データ注釈検証パッケージ</span><span class="sxs-lookup"><span data-stu-id="bd2f5-172"> data annotations validation package</span></span>

<span data-ttu-id="bd2f5-173">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) は、`DataAnnotationsValidator` コンポーネントを使用して、検証エクスペリエンスのギャップを埋めるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-173">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="bd2f5-174">パッケージは現在、*試験段階*です。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-174">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="bd2f5-175">[CompareProperty] 属性</span><span class="sxs-lookup"><span data-stu-id="bd2f5-175">[CompareProperty] attribute</span></span>

<span data-ttu-id="bd2f5-176"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> は、検証結果を特定のメンバーに関連付けないため、`DataAnnotationsValidator` コンポーネントで正しく機能しません。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-176">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="bd2f5-177">これにより、フィールドレベルの検証と、送信時のモデル全体が検証されたときの動作に一貫性がなくなることがあります。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-177">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="bd2f5-178">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) の "*試験的*" パッケージでは、これらの制限を回避する追加の検証属性 `ComparePropertyAttribute` が導入されています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-178">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="bd2f5-179">Blazor アプリでは、`[CompareProperty]` は `[Compare]` 属性の直接の代わりとなるものです。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-179">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="bd2f5-180">入れ子になったモデル、コレクション型、および複合型</span><span class="sxs-lookup"><span data-stu-id="bd2f5-180">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="bd2f5-181"> では、組み込みの `DataAnnotationsValidator` によるデータ注釈を使用したフォーム入力の検証をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-181"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="bd2f5-182">ただし、`DataAnnotationsValidator`で は、コレクション型または複合型のプロパティではないフォームにバインドされているモデルの最上位レベルのプロパティのみが検証されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-182">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="bd2f5-183">コレクション型と複合型のプロパティを含む、バインドされたモデルのオブジェクト グラフ全体を検証するには、"*試験的*" な [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) パッケージによって提供される `ObjectGraphDataAnnotationsValidator` を使用します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-183">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="bd2f5-184">`[ValidateComplexType]` でモデルのプロパティに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-184">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="bd2f5-185">次のモデル クラスでは、`ShipDescription` クラスに、モデルがフォームにバインドされたときに検証する追加のデータ注釈が含まれています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-185">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="bd2f5-186">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="bd2f5-186">*Starship.cs*:</span></span>

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

<span data-ttu-id="bd2f5-187">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="bd2f5-187">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="bd2f5-188">フォームの検証に基づいて送信ボタンを有効にする</span><span class="sxs-lookup"><span data-stu-id="bd2f5-188">Enable the submit button based on form validation</span></span>

<span data-ttu-id="bd2f5-189">フォームの検証に基づいて送信ボタンを有効または無効にするには:</span><span class="sxs-lookup"><span data-stu-id="bd2f5-189">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="bd2f5-190">コンポーネントを初期化するときに、フォームの `EditContext` を使用してモデルを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-190">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="bd2f5-191">コンテキストの `OnFieldChanged` コールバックでフォームを検証して、送信ボタンを有効または無効にします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-191">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="bd2f5-192">`Dispose` メソッドでイベント ハンドラーをアンフックします。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-192">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="bd2f5-193">詳細については、「<xref:blazor/lifecycle#component-disposal-with-idisposable>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-193">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="bd2f5-194">前の例では、次の場合に `formInvalid` を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-194">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="bd2f5-195">フォームには、有効な既定値が事前に読み込まれています。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-195">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="bd2f5-196">フォームが読み込まれるときに、送信ボタンを有効にしたいと考えます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-196">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="bd2f5-197">上記の方法の副作用として、ユーザーがいずれかのフィールドを操作した後に、`ValidationSummary` コンポーネントに無効なフィールドが設定されます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-197">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="bd2f5-198">このシナリオは、次のいずれかの方法で対処できます。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-198">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="bd2f5-199">フォームでは `ValidationSummary` コンポーネントを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-199">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="bd2f5-200">送信ボタンが選択された (たとえば、`HandleValidSubmit` メソッドで) ときに `ValidationSummary` コンポーネントを表示します。</span><span class="sxs-lookup"><span data-stu-id="bd2f5-200">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
