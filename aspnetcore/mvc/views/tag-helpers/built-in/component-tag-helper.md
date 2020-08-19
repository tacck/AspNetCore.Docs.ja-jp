---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: ASP.NET Core コンポーネントタグヘルパーを使用して、 Razor ページおよびビューでコンポーネントを表示する方法について説明します。
ms.custom: mvc
ms.date: 04/15/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 1a0422da6bd48049cac73debe7d335da91e311be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633917"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core のコンポーネントタグヘルパー

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。

## <a name="prerequisites"></a>前提条件

記事の「 *ページとビューでコンポーネントを使用するためにアプリを準備する* 」セクションのガイダンスに従って <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps#prepare-the-app> ください。

## <a name="component-tag-helper"></a>コンポーネントタグヘルパー

次のコンポーネントタグヘルパーは、 `Counter` ページまたはビューでコンポーネントをレンダリングします。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

前の例では、 `Counter` コンポーネントがアプリの *Pages* フォルダー内にあることを前提としています。 プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using BlazorSample.Pages`)。

コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。 `ColorfulCheckbox`チェックボックスのラベルの色とサイズを設定する次のコンポーネントについて考えてみます。

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

コンポーネント `Size` `int` タグヘルパーでは、() および `Color` ( `string` ) [コンポーネントパラメーター](xref:blazor/components/index#component-parameters) を設定できます。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

前の例では、 `ColorfulCheckbox` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。 プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名です (例: `@using BlazorSample.Shared`)。

ページまたはビューには、次の HTML が表示されます。

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

前の例のように、引用符で囲まれた文字列を渡すには、 [明示的な Razor 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です `param-Color` 。 Razor型の値の解析動作は、属性 `string` が型であるため、属性には適用されません `param-*` `object` 。

パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。 たとえば、前の例ではとの値を指定できます `Size` `Color` 。これは、 `Size` との型 `Color` がプリミティブ型 (と) であり、 `int` `string` JSON シリアライザーでサポートされているためです。

次の例では、クラスオブジェクトがコンポーネントに渡されます。

*MyClass.cs*:

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

**クラスには、パラメーターなしのパブリックコンストラクターが必要です。**

*Shared/MyComponent*:

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

*Pages/MyPage*:

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

前の例では、 `MyComponent` コンポーネントがアプリの *共有* フォルダーにあることを前提としています。 プレースホルダー `{APP ASSEMBLY}` は、アプリのアセンブリ名 ( `@using BlazorSample` やなど `@using BlazorSample.Shared` ) です。 `MyClass` は、アプリの名前空間にあります。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、コンポーネントに対して以下の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| 表示モード | 説明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

## <a name="additional-resources"></a>その他のリソース

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
