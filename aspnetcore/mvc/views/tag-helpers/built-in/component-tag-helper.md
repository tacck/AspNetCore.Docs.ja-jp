---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: 'ASP.NET Core コンポーネントタグヘルパーを使用して、 Razor ページおよびビューでコンポーネントを表示する方法について説明します。'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595455"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="8e9a7-103">ASP.NET Core のコンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="8e9a7-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="8e9a7-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8e9a7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8e9a7-105">[前提条件]</span><span class="sxs-lookup"><span data-stu-id="8e9a7-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8e9a7-106">*構成* セクションのガイダンスに従って、次のいずれかの操作を行います。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8e9a7-107">この記事の *構成* セクションのガイダンスに従って <xref:blazor/components/prerendering-and-integration?pivots=server> ください。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="8e9a7-108">コンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="8e9a7-108">Component Tag Helper</span></span>

<span data-ttu-id="8e9a7-109">ページまたはビューからコンポーネントを表示するには、 [コンポーネントタグヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` タグ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="8e9a7-110">Razorホストされ Razor た *Blazor WebAssembly アプリ* でのページおよび MVC アプリへのコンポーネントの統合は、.net 5.0 以降の ASP.NET Core でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="8e9a7-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="8e9a7-112">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="8e9a7-113">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8e9a7-114">Blazor WebAssembly アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="8e9a7-115">表示モード</span><span class="sxs-lookup"><span data-stu-id="8e9a7-115">Render Mode</span></span> | <span data-ttu-id="8e9a7-116">説明</span><span class="sxs-lookup"><span data-stu-id="8e9a7-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="8e9a7-117">Blazor WebAssemblyブラウザーに読み込まれたときに対話型コンポーネントを含めるために使用する、アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="8e9a7-118">コンポーネントが prerendered ではありません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-118">The component isn't prerendered.</span></span> <span data-ttu-id="8e9a7-119">このオプションを使用すると、さまざまなページでさまざまなコンポーネントを簡単に表示 Blazor WebAssembly できます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="8e9a7-120">コンポーネントを静的 HTML に Prerenders し、 Blazor WebAssembly 後でブラウザーに読み込まれたときにコンポーネントを対話型にするために、アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="8e9a7-121">Blazor Server アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="8e9a7-122">表示モード</span><span class="sxs-lookup"><span data-stu-id="8e9a7-122">Render Mode</span></span> | <span data-ttu-id="8e9a7-123">説明</span><span class="sxs-lookup"><span data-stu-id="8e9a7-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="8e9a7-124">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="8e9a7-125">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="8e9a7-126">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="8e9a7-127">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-127">Output from the component isn't included.</span></span> <span data-ttu-id="8e9a7-128">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="8e9a7-129">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8e9a7-130">Blazor Server アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="8e9a7-131">表示モード</span><span class="sxs-lookup"><span data-stu-id="8e9a7-131">Render Mode</span></span> | <span data-ttu-id="8e9a7-132">説明</span><span class="sxs-lookup"><span data-stu-id="8e9a7-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="8e9a7-133">コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="8e9a7-134">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="8e9a7-135">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="8e9a7-136">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-136">Output from the component isn't included.</span></span> <span data-ttu-id="8e9a7-137">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="8e9a7-138">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="8e9a7-139">追加の特性は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-139">Additional characteristics include:</span></span>

* <span data-ttu-id="8e9a7-140">複数のコンポーネントを表示する複数のコンポーネントタグヘルパーが許可されてい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="8e9a7-141">アプリが起動した後に、コンポーネントを動的にレンダリングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="8e9a7-142">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="8e9a7-143">コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="8e9a7-144">コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="8e9a7-145">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="8e9a7-146">次のコンポーネントタグヘルパーは、 `Counter` を使用して、アプリケーションのページまたはビューでコンポーネントをレンダリングし Blazor Server `ServerPrerendered` ます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="8e9a7-147">前の例では、 `Counter` コンポーネントがアプリの *Pages* フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="8e9a7-148">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (たとえば、ホストされているソリューションの場合 `@using BlazorSample.Pages` `@using BlazorSample.Client.Pages` Blazor )。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="8e9a7-149">コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="8e9a7-150">`ColorfulCheckbox`チェックボックスのラベルの色とサイズを設定する次のコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="8e9a7-151">コンポーネント `Size` `int` タグヘルパーでは、() および `Color` ( `string` ) [コンポーネントパラメーター](xref:blazor/components/index#component-parameters) を設定できます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="8e9a7-152">前の例では、 `ColorfulCheckbox` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="8e9a7-153">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using BlazorSample.Shared`)。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="8e9a7-154">ページまたはビューには、次の HTML が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="8e9a7-155">前の例のように、引用符で囲まれた文字列を渡すには、 [明示的な Razor 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です `param-Color` 。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="8e9a7-156">Razor型の値の解析動作は、属性 `string` が型であるため、属性には適用されません `param-*` `object` 。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="8e9a7-157">次の場合を除き、すべての種類のパラメーターがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="8e9a7-158">ジェネリックパラメーター。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-158">Generic parameters.</span></span>
* <span data-ttu-id="8e9a7-159">シリアル化できないパラメーター。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="8e9a7-160">コレクションパラメーターの継承。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="8e9a7-161">型がアプリの外部で定義されているか、遅延読み込みされたアセンブリ内にあるパラメーター Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="8e9a7-162">パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="8e9a7-163">たとえば、前の例ではとの値を指定できます `Size` `Color` 。これは、 `Size` との型 `Color` がプリミティブ型 (と) であり、 `int` `string` JSON シリアライザーでサポートされているためです。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="8e9a7-164">次の例では、クラスオブジェクトがコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="8e9a7-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="8e9a7-165">*MyClass.cs* :</span></span>

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

<span data-ttu-id="8e9a7-166">**クラスには、パラメーターなしのパブリックコンストラクターが必要です。**</span><span class="sxs-lookup"><span data-stu-id="8e9a7-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="8e9a7-167">*Shared/MyComponent* :</span><span class="sxs-lookup"><span data-stu-id="8e9a7-167">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="8e9a7-168">*Pages/MyPage* :</span><span class="sxs-lookup"><span data-stu-id="8e9a7-168">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="8e9a7-169">前の例では、 `MyComponent` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="8e9a7-170">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 ( `@using BlazorSample` やなど `@using BlazorSample.Shared` ) です。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="8e9a7-171">`MyClass` は、アプリの名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="8e9a7-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e9a7-172">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8e9a7-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
