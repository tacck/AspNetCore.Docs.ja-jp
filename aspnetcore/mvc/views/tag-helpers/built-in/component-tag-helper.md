---
title: ASP.NETコアのコンポーネント タグ ヘルパー
author: guardrex
ms.author: riande
description: ASP.NET コア コンポーネント タグ ヘルパーを使用して、Razor コンポーネントをページとビューにレンダリングする方法について説明します。
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440962"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="f1090-103">ASP.NETコアのコンポーネント タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="f1090-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f1090-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f1090-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f1090-105">ページまたはビューからコンポーネントをレンダリングするには、 コンポーネント[タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1090-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1090-106">前提条件</span><span class="sxs-lookup"><span data-stu-id="f1090-106">Prerequisites</span></span>

<span data-ttu-id="f1090-107">「*ページとビューでコンポーネントを使用するようにアプリを準備する」* セクションのガイダンスに<xref:blazor/integrate-components#prepare-the-app>従ってください。</span><span class="sxs-lookup"><span data-stu-id="f1090-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="f1090-108">コンポーネント タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="f1090-108">Component Tag Helper</span></span>

<span data-ttu-id="f1090-109">次のコンポーネント タグ ヘルパーは`Counter`、コンポーネントをページまたはビューにレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f1090-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="f1090-110">前の例では、`Counter`コンポーネントがアプリの*Pages*フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f1090-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="f1090-111">コンポーネント タグ ヘルパーは、コンポーネントにパラメータを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="f1090-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="f1090-112">チェック ボックス`ColorfulCheckbox`ラベルの色とサイズを設定する次のコンポーネントを検討してください。</span><span class="sxs-lookup"><span data-stu-id="f1090-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="f1090-113">( `Size` `int`)`Color`および`string`( )[コンポーネント パラメータ](xref:blazor/components#component-parameters)は、コンポーネント タグ ヘルパーで設定できます。</span><span class="sxs-lookup"><span data-stu-id="f1090-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="f1090-114">前の例では、`ColorfulCheckbox`コンポーネントがアプリの*共有*フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f1090-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="f1090-115">ページまたはビューに次の HTML が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1090-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="f1090-116">引用符で囲まれた文字列を渡す場合は、前の`param-Color`例に示すように[、明示的](xref:mvc/views/razor#explicit-razor-expressions)な Razor 式が必要です。</span><span class="sxs-lookup"><span data-stu-id="f1090-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="f1090-117">型値の`string`Razor 解析動作は、属性が型であるため`param-*`、属性には`object`適用されません。</span><span class="sxs-lookup"><span data-stu-id="f1090-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="f1090-118">パラメーター型は JSON シリアル化可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1090-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="f1090-119">たとえば、前`Size`の例`Color`では、および の型`Size``Color`が JSON シリアライザーでサポートされるプリミティブ型 (`int` `string`および ) であるため、値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1090-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="f1090-120">次の例では、クラス オブジェクトがコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="f1090-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="f1090-121">*MyClass.cs:*</span><span class="sxs-lookup"><span data-stu-id="f1090-121">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="f1090-122">**クラスには、パブリックなパラメーターなしのコンストラクターが必要です。**</span><span class="sxs-lookup"><span data-stu-id="f1090-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="f1090-123">*共有/マイコンポーネント.カミソリ*:</span><span class="sxs-lookup"><span data-stu-id="f1090-123">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="f1090-124">*ページ/マイページ.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f1090-124">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="f1090-125">前の例では、`MyComponent`コンポーネントがアプリの*共有*フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f1090-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="f1090-126">`MyClass`は、アプリの名前空間 (`{APP ASSEMBLY}`) にあります。</span><span class="sxs-lookup"><span data-stu-id="f1090-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="f1090-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>コンポーネントを構成します。</span><span class="sxs-lookup"><span data-stu-id="f1090-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="f1090-128">ページにプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="f1090-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="f1090-129">ページ上で静的 HTML としてレンダリングされるか、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合。</span><span class="sxs-lookup"><span data-stu-id="f1090-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="f1090-130">レンダリング モード</span><span class="sxs-lookup"><span data-stu-id="f1090-130">Render Mode</span></span> | <span data-ttu-id="f1090-131">説明</span><span class="sxs-lookup"><span data-stu-id="f1090-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="f1090-132">コンポーネントを静的 HTML にレンダリングし、サーバー アプリケーションBlazorのマーカーを含みます。</span><span class="sxs-lookup"><span data-stu-id="f1090-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="f1090-133">ユーザー エージェントが起動すると、このマーカーを使用してアプリをBlazorブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="f1090-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="f1090-134">サーバー アプリのマーカーをBlazorレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f1090-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="f1090-135">コンポーネントからの出力は含まれません。</span><span class="sxs-lookup"><span data-stu-id="f1090-135">Output from the component isn't included.</span></span> <span data-ttu-id="f1090-136">ユーザー エージェントが起動すると、このマーカーを使用してアプリをBlazorブートストラップします。</span><span class="sxs-lookup"><span data-stu-id="f1090-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="f1090-137">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="f1090-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="f1090-138">ページとビューはコンポーネントを使用できますが、逆は当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="f1090-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="f1090-139">コンポーネントは、部分的なビューやセクションなどのビュー固有の機能やページ固有の機能を使用できません。</span><span class="sxs-lookup"><span data-stu-id="f1090-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="f1090-140">コンポーネントの部分ビューからロジックを使用するには、部分ビュー ロジックをコンポーネントに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="f1090-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="f1090-141">静的 HTML ページからのサーバー コンポーネントのレンダリングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="f1090-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f1090-142">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="f1090-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
