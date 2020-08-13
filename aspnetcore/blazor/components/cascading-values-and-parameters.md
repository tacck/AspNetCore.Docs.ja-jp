---
title: ASP.NET Core Blazor の値とパラメーターのカスケード
author: guardrex
description: 先祖のコンポーネントから子孫のコンポーネントにデータをフローさせる方法について説明します。
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 1ee30a84e0869f6b9ee937648eb87bd41e8702e3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014712"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a><span data-ttu-id="f6f21-103">ASP.NET Core Blazor の値とパラメーターのカスケード</span><span class="sxs-lookup"><span data-stu-id="f6f21-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="f6f21-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="f6f21-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="f6f21-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f6f21-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f6f21-106">一部のシナリオでは、特に複数のコンポーネント レイヤーがある場合に、[コンポーネント パラメーター](xref:blazor/components/index#component-parameters)を使用して、先祖コンポーネントから子孫コンポーネントにデータをフローすることが不便な場合があります。</span><span class="sxs-lookup"><span data-stu-id="f6f21-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="f6f21-107">値とパラメーターのカスケードによって、先祖コンポーネントがそのすべての子孫コンポーネントに値を提供するための便利な方法を用意することで、この問題が解決されます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="f6f21-108">値とパラメーターのカスケードによって、コンポーネントで調整する方法も得られます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="f6f21-109">テーマの例</span><span class="sxs-lookup"><span data-stu-id="f6f21-109">Theme example</span></span>

<span data-ttu-id="f6f21-110">次のサンプル アプリの例では、`ThemeInfo` クラスで、コンポーネント階層の下位に伝達されるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。</span><span class="sxs-lookup"><span data-stu-id="f6f21-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="f6f21-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="f6f21-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="f6f21-112">先祖コンポーネントでは、Cascading Value コンポーネントを使用してカスケード値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="f6f21-113"><xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を指定します。</span><span class="sxs-lookup"><span data-stu-id="f6f21-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="f6f21-114">たとえば、サンプル アプリでは、アプリのレイアウトの 1 つに、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード パラメーターとして、テーマ情報 (`ThemeInfo`) を指定しています。</span><span class="sxs-lookup"><span data-stu-id="f6f21-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="f6f21-115">`ButtonClass` には、レイアウト コンポーネント内の `btn-success` の値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="f6f21-116">すべての子孫コンポーネントで、`ThemeInfo` カスケード オブジェクトからこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="f6f21-117">`CascadingValuesParametersLayout` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f6f21-117">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="f6f21-118">カスケード値を使用するには、コンポーネントで [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用してカスケード パラメーターを宣言します。</span><span class="sxs-lookup"><span data-stu-id="f6f21-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="f6f21-119">カスケード値は、型によってカスケード パラメーターにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="f6f21-120">サンプル アプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値をカスケード パラメーターにバインドしています。</span><span class="sxs-lookup"><span data-stu-id="f6f21-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="f6f21-121">パラメーターは、コンポーネントによって表示されるボタンの 1 つに CSS クラスを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="f6f21-122">`CascadingValuesParametersTheme` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f6f21-122">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="f6f21-123">同じサブツリー内で同じ型の複数の値をカスケードするには、各 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントとその対応する [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性に一意の <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="f6f21-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="f6f21-124">次の例では、2 つの <xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントで、名前によって `MyCascadingType` の異なるインスタンスをカスケードしています。</span><span class="sxs-lookup"><span data-stu-id="f6f21-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="f6f21-125">子孫コンポーネントでは、カスケードされたパラメーターは、それらの値を、名前によって、先祖コンポーネント内の対応するカスケードされた値から受け取ります。</span><span class="sxs-lookup"><span data-stu-id="f6f21-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="f6f21-126">TabSet の例</span><span class="sxs-lookup"><span data-stu-id="f6f21-126">TabSet example</span></span>

<span data-ttu-id="f6f21-127">パラメーターのカスケードによって、コンポーネント階層全体でコンポーネントを連携させることもできます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="f6f21-128">たとえば、サンプル アプリの次の `TabSet` の例を考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="f6f21-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="f6f21-129">このサンプル アプリには、タブに実装されている `ITab` インターフェイスがあります。</span><span class="sxs-lookup"><span data-stu-id="f6f21-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="f6f21-130">`CascadingValuesParametersTabSet` コンポーネントでは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。</span><span class="sxs-lookup"><span data-stu-id="f6f21-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

<span data-ttu-id="f6f21-131">子 `Tab` コンポーネントは、パラメーターとして `TabSet` に明示的に渡されません。</span><span class="sxs-lookup"><span data-stu-id="f6f21-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="f6f21-132">代わりに、子 `Tab` コンポーネントは、`TabSet` の子コンテンツに含まれます。</span><span class="sxs-lookup"><span data-stu-id="f6f21-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="f6f21-133">ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントでは、*それ自体をカスケード値として指定し*、その後に子孫 `Tab` コンポーネントによって取得できるようにします。</span><span class="sxs-lookup"><span data-stu-id="f6f21-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="f6f21-134">`TabSet` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f6f21-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="f6f21-135">子孫の `Tab` コンポーネントでは、含まれている `TabSet` をカスケード パラメーターとしてキャプチャするため、`Tab` コンポーネントはそれ自体を `TabSet` に追加し、どのタブをアクティブにするかを調整します。</span><span class="sxs-lookup"><span data-stu-id="f6f21-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="f6f21-136">`Tab` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="f6f21-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
