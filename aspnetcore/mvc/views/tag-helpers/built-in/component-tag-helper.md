---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: 'ASP.NET Core コンポーネントタグヘルパーを使用して、 :::no-loc(Razor)::: ページおよびビューでコンポーネントを表示する方法について説明します。'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431044"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="bfbd3-103">ASP.NET Core のコンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="bfbd3-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="bfbd3-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bfbd3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bfbd3-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="bfbd3-105">Prerequisites</span></span>

<span data-ttu-id="bfbd3-106">*構成* セクションのガイダンスに従って、次のいずれかの操作を行います。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="bfbd3-107">コンポーネントタグヘルパー</span><span class="sxs-lookup"><span data-stu-id="bfbd3-107">Component Tag Helper</span></span>

<span data-ttu-id="bfbd3-108">ページまたはビューからコンポーネントを表示するには、 [コンポーネントタグヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` タグ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="bfbd3-109">:::no-loc(Razor):::ホストされ :::no-loc(Razor)::: た *:::no-loc(Blazor WebAssembly)::: アプリ* でのページおよび MVC アプリへのコンポーネントの統合は、.net 5.0 以降の ASP.NET Core でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-109">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="bfbd3-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="bfbd3-111">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="bfbd3-112">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから :::no-loc(Blazor)::: アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bfbd3-113">:::no-loc(Blazor WebAssembly)::: アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-113">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="bfbd3-114">表示モード</span><span class="sxs-lookup"><span data-stu-id="bfbd3-114">Render Mode</span></span> | <span data-ttu-id="bfbd3-115">説明</span><span class="sxs-lookup"><span data-stu-id="bfbd3-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="bfbd3-116">:::no-loc(Blazor WebAssembly):::ブラウザーに読み込まれたときに対話型コンポーネントを含めるために使用する、アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-116">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="bfbd3-117">コンポーネントが prerendered ではありません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-117">The component isn't prerendered.</span></span> <span data-ttu-id="bfbd3-118">このオプションを使用すると、さまざまなページでさまざまなコンポーネントを簡単に表示 :::no-loc(Blazor WebAssembly)::: できます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-118">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="bfbd3-119">コンポーネントを静的 HTML に Prerenders し、 :::no-loc(Blazor WebAssembly)::: 後でブラウザーに読み込まれたときにコンポーネントを対話型にするために、アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-119">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="bfbd3-120">:::no-loc(Blazor Server)::: アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-120">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="bfbd3-121">表示モード</span><span class="sxs-lookup"><span data-stu-id="bfbd3-121">Render Mode</span></span> | <span data-ttu-id="bfbd3-122">説明</span><span class="sxs-lookup"><span data-stu-id="bfbd3-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bfbd3-123">コンポーネントを静的 HTML にレンダリングし、:::no-loc(Blazor Server)::: アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-123">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bfbd3-124">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-124">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bfbd3-125">:::no-loc(Blazor Server)::: アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-125">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bfbd3-126">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-126">Output from the component isn't included.</span></span> <span data-ttu-id="bfbd3-127">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-127">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bfbd3-128">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bfbd3-129">:::no-loc(Blazor Server)::: アプリのレンダリングモードを次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-129">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="bfbd3-130">表示モード</span><span class="sxs-lookup"><span data-stu-id="bfbd3-130">Render Mode</span></span> | <span data-ttu-id="bfbd3-131">説明</span><span class="sxs-lookup"><span data-stu-id="bfbd3-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="bfbd3-132">コンポーネントを静的 HTML にレンダリングし、:::no-loc(Blazor Server)::: アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-132">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bfbd3-133">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-133">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="bfbd3-134">:::no-loc(Blazor Server)::: アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-134">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="bfbd3-135">コンポーネントからの出力は含められません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-135">Output from the component isn't included.</span></span> <span data-ttu-id="bfbd3-136">このマーカーは、ユーザー エージェントの起動時に :::no-loc(Blazor)::: アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="bfbd3-137">コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="bfbd3-138">追加の特性は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-138">Additional characteristics include:</span></span>

* <span data-ttu-id="bfbd3-139">複数のコンポーネントを表示する複数のコンポーネントタグヘルパーが許可されてい :::no-loc(Razor)::: ます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-139">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="bfbd3-140">アプリが起動した後に、コンポーネントを動的にレンダリングすることはできません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="bfbd3-141">ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="bfbd3-142">コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="bfbd3-143">コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="bfbd3-144">静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="bfbd3-145">次のコンポーネントタグヘルパーは、 `Counter` を使用して、アプリケーションのページまたはビューでコンポーネントをレンダリングし :::no-loc(Blazor Server)::: `ServerPrerendered` ます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-145">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="bfbd3-146">前の例では、 `Counter` コンポーネントがアプリの *Pages* フォルダー内にあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="bfbd3-147">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (たとえば、ホストされているソリューションの場合 `@using :::no-loc(Blazor):::Sample.Pages` `@using :::no-loc(Blazor):::Sample.Client.Pages` :::no-loc(Blazor)::: )。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="bfbd3-148">コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="bfbd3-149">`ColorfulCheckbox`チェックボックスのラベルの色とサイズを設定する次のコンポーネントについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="bfbd3-150">コンポーネント `Size` `int` タグヘルパーでは、() および `Color` ( `string` ) [コンポーネントパラメーター](xref:blazor/components/index#component-parameters) を設定できます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="bfbd3-151">前の例では、 `ColorfulCheckbox` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="bfbd3-152">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using :::no-loc(Blazor):::Sample.Shared`)。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="bfbd3-153">ページまたはビューには、次の HTML が表示されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="bfbd3-154">前の例のように、引用符で囲まれた文字列を渡すには、 [明示的な :::no-loc(Razor)::: 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です `param-Color` 。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-154">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="bfbd3-155">:::no-loc(Razor):::型の値の解析動作は、属性 `string` が型であるため、属性には適用されません `param-*` `object` 。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-155">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="bfbd3-156">次の場合を除き、すべての種類のパラメーターがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="bfbd3-157">ジェネリックパラメーター。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-157">Generic parameters.</span></span>
* <span data-ttu-id="bfbd3-158">シリアル化できないパラメーター。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="bfbd3-159">コレクションパラメーターの継承。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="bfbd3-160">型がアプリの外部で定義されているか、遅延読み込みされたアセンブリ内にあるパラメーター :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-160">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="bfbd3-161">パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="bfbd3-162">たとえば、前の例ではとの値を指定できます `Size` `Color` 。これは、 `Size` との型 `Color` がプリミティブ型 (と) であり、 `int` `string` JSON シリアライザーでサポートされているためです。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="bfbd3-163">次の例では、クラスオブジェクトがコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="bfbd3-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="bfbd3-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="bfbd3-165">**クラスには、パラメーターなしのパブリックコンストラクターが必要です。**</span><span class="sxs-lookup"><span data-stu-id="bfbd3-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="bfbd3-166">*Shared/MyComponent* :</span><span class="sxs-lookup"><span data-stu-id="bfbd3-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="bfbd3-167">*Pages/MyPage* :</span><span class="sxs-lookup"><span data-stu-id="bfbd3-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="bfbd3-168">前の例では、 `MyComponent` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="bfbd3-169">プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 ( `@using :::no-loc(Blazor):::Sample` やなど `@using :::no-loc(Blazor):::Sample.Shared` ) です。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="bfbd3-170">`MyClass` は、アプリの名前空間にあります。</span><span class="sxs-lookup"><span data-stu-id="bfbd3-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bfbd3-171">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="bfbd3-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
