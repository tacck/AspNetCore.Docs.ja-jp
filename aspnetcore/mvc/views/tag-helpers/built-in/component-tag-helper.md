---
title: ASP.NET Core のコンポーネントタグヘルパー
author: guardrex
ms.author: riande
description: ASP.NET Core コンポーネントタグヘルパーを使用して、ページおよびRazorビューでコンポーネントを表示する方法について説明します。
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773930"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core のコンポーネントタグヘルパー

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。

## <a name="prerequisites"></a>前提条件

記事の「*ページとビューでコンポーネントを使用するためにアプリを準備する*」セクションのガイダンスに従ってください。 <xref:blazor/integrate-components#prepare-the-app>

## <a name="component-tag-helper"></a>コンポーネントタグヘルパー

次のコンポーネントタグヘルパーは、 `Counter`ページまたはビューでコンポーネントをレンダリングします。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

前の例では、 `Counter`コンポーネントがアプリの*Pages*フォルダー内にあることを前提としています。

コンポーネントタグヘルパーは、コンポーネントにパラメーターを渡すこともできます。 チェックボックスの`ColorfulCheckbox`ラベルの色とサイズを設定する次のコンポーネントについて考えてみます。

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

コンポーネント`Size`タグ`int`ヘルパーで`Color`は`string`、() および ()[コンポーネントパラメーター](xref:blazor/components#component-parameters)を設定できます。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

前の例では、 `ColorfulCheckbox`コンポーネントがアプリの*共有*フォルダーにあることを前提としています。

ページまたはビューには、次の HTML が表示されます。

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

前の`param-Color`例のように、引用符で囲まれた文字列を渡すには、[明示的な Razor 式](xref:mvc/views/razor#explicit-razor-expressions)が必要です。 `string`型の値に対する Razor 解析動作は、属性が`param-*` `object`型であるため、属性には適用されません。

パラメーターの型は、JSON シリアル化可能である必要があります。これは通常、型が既定のコンストラクターと設定可能なプロパティを持つ必要があることを意味します。 たとえば、前の例では`Size`と`Color`の値を指定できます。これは、 `Size`と`Color`の型がプリミティブ`int`型`string`(と) であり、JSON シリアライザーでサポートされているためです。

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

前の例では、 `MyComponent`コンポーネントがアプリの*共有*フォルダーにあることを前提としています。 `MyClass`は、アプリの名前空間 (`{APP ASSEMBLY}`) にあります。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> により、コンポーネントに対して次の構成が行われます。

* ページに事前レンダリングするかどうか。
* ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

| 表示モード | 説明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 コンポーネントからの出力は含められません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆の場合は真実ではありません。 コンポーネントでは、ビューおよびページ固有の機能 (部分ビューやセクションなど) を使用できません。 コンポーネントの部分ビューのロジックを使用するには、部分ビューのロジックをコンポーネントにします。

静的 HTML ページからのサーバー コンポーネントのレンダリングは、サポートされていません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
