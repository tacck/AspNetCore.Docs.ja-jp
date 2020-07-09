---
title: ASP.NET Core Blazor の値とパラメーターのカスケード
author: guardrex
description: 先祖のコンポーネントから子孫のコンポーネントにデータをフローさせる方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c426be21b520520c6745ada95be35816f7365c21
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059930"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor の値とパラメーターのカスケード

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

一部のシナリオでは、特に複数のコンポーネント レイヤーがある場合に、[コンポーネント パラメーター](xref:blazor/components/index#component-parameters)を使用して、先祖コンポーネントから子孫コンポーネントにデータをフローすることが不便な場合があります。 値とパラメーターのカスケードによって、先祖コンポーネントがそのすべての子孫コンポーネントに値を提供するための便利な方法を用意することで、この問題が解決されます。 値とパラメーターのカスケードによって、コンポーネントで調整する方法も得られます。

### <a name="theme-example"></a>テーマの例

次のサンプル アプリの例では、`ThemeInfo` クラスで、コンポーネント階層の下位に伝達されるテーマ情報を指定して、アプリの特定の部分にあるすべてのボタンが同じスタイルを共有するようにしています。

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

先祖コンポーネントでは、Cascading Value コンポーネントを使用してカスケード値を指定できます。 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントは、コンポーネント階層のサブツリーをラップし、そのサブツリー内のすべてのコンポーネントに単一の値を指定します。

たとえば、サンプル アプリでは、アプリのレイアウトの 1 つに、`@Body` プロパティのレイアウト本体を構成するすべてのコンポーネントのカスケード パラメーターとして、テーマ情報 (`ThemeInfo`) を指定しています。 `ButtonClass` には、レイアウト コンポーネント内の `btn-success` の値が割り当てられます。 すべての子孫コンポーネントで、`ThemeInfo` カスケード オブジェクトからこのプロパティを使用できます。

`CascadingValuesParametersLayout` コンポーネント:

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

カスケード値を使用するには、コンポーネントで [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性を使用してカスケード パラメーターを宣言します。 カスケード値は、型によってカスケード パラメーターにバインドされます。

サンプル アプリでは、`CascadingValuesParametersTheme` コンポーネントによって `ThemeInfo` カスケード値をカスケード パラメーターにバインドしています。 パラメーターは、コンポーネントによって表示されるボタンの 1 つに CSS クラスを設定するために使用されます。

`CascadingValuesParametersTheme` コンポーネント:

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

同じサブツリー内で同じ型の複数の値をカスケードするには、各 <xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントとその対応する [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) 属性に一意の <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> 文字列を指定します。 次の例では、2 つの <xref:Microsoft.AspNetCore.Components.CascadingValue%601> コンポーネントで、名前によって `MyCascadingType` の異なるインスタンスをカスケードしています。

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

子孫コンポーネントでは、カスケードされたパラメーターは、それらの値を、名前によって、先祖コンポーネント内の対応するカスケードされた値から受け取ります。

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet の例

パラメーターのカスケードによって、コンポーネント階層全体でコンポーネントを連携させることもできます。 たとえば、サンプル アプリの次の `TabSet` の例を考えてみましょう。

このサンプル アプリには、タブに実装されている `ITab` インターフェイスがあります。

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` コンポーネントでは、いくつかの `Tab` コンポーネントを含む `TabSet` コンポーネントを使用します。

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

子 `Tab` コンポーネントは、パラメーターとして `TabSet` に明示的に渡されません。 代わりに、子 `Tab` コンポーネントは、`TabSet` の子コンテンツに含まれます。 ただし、`TabSet` は、ヘッダーとアクティブなタブをレンダリングできるように、各 `Tab` コンポーネントについて認識している必要があります。追加のコードを必要とせずにこの調整を可能にするために、`TabSet` コンポーネントでは、*それ自体をカスケード値として指定し*、その後に子孫 `Tab` コンポーネントによって取得できるようにします。

`TabSet` コンポーネント:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子孫の `Tab` コンポーネントでは、含まれている `TabSet` をカスケード パラメーターとしてキャプチャするため、`Tab` コンポーネントはそれ自体を `TabSet` に追加し、どのタブをアクティブにするかを調整します。

`Tab` コンポーネント:

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
