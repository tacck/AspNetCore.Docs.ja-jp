---
title: ASP.NETコアのコンポーネント タグ ヘルパー
author: guardrex
ms.author: riande
description: ASP.NET コア コンポーネント タグ ヘルパーを使用して、Razor コンポーネントをページとビューにレンダリングする方法について説明します。
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123430"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NETコアのコンポーネント タグ ヘルパー

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

ページまたはビューからコンポーネントをレンダリングするには、 コンポーネント[タグ ヘルパー](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)を使用します。

## <a name="prerequisites"></a>前提条件

「*ページとビューでコンポーネントを使用するようにアプリを準備する」* セクションのガイダンスに<xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views>従ってください。

## <a name="component-tag-helper"></a>コンポーネント タグ ヘルパー

次のコンポーネント タグ ヘルパーは`Counter`、コンポーネントをページまたはビューにレンダリングします。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

前の例では、`Counter`コンポーネントがアプリの*Pages*フォルダー内にあることを前提としています。

コンポーネント タグ ヘルパーは、コンポーネントにパラメータを渡すこともできます。 チェック ボックス`ColorfulCheckbox`ラベルの色とサイズを設定する次のコンポーネントを検討してください。

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

( `Size` `int`)`Color`および`string`( )[コンポーネント パラメータ](xref:blazor/components#component-parameters)は、コンポーネント タグ ヘルパーで設定できます。

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

前の例では、`ColorfulCheckbox`コンポーネントがアプリの*共有*フォルダー内にあることを前提としています。

ページまたはビューに次の HTML が表示されます。

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

引用符で囲まれた文字列を渡す場合は、前の`param-Color`例に示すように[、明示的](xref:mvc/views/razor#explicit-razor-expressions)な Razor 式が必要です。 型値の`string`Razor 解析動作は、属性が型であるため`param-*`、属性には`object`適用されません。

パラメーター型は JSON シリアル化可能である必要があります。 たとえば、前`Size`の例`Color`では、および の型`Size``Color`が JSON シリアライザーでサポートされるプリミティブ型 (`int` `string`および ) であるため、値を指定できます。

次の例では、クラス オブジェクトがコンポーネントに渡されます。

*MyClass.cs:*

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

**クラスには、パブリックなパラメーターなしのコンストラクターが必要です。**

*共有/マイコンポーネント.カミソリ*:

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

*ページ/マイページ.cshtml*:

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

前の例では、`MyComponent`コンポーネントがアプリの*共有*フォルダー内にあることを前提としています。 `MyClass`は、アプリの名前空間 (`{APP ASSEMBLY}`) にあります。

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>コンポーネントを構成します。

* ページにプリレンダリングされます。
* ページ上で静的 HTML としてレンダリングされるか、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合。

| レンダリング モード | 説明 |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | コンポーネントを静的 HTML にレンダリングし、サーバー アプリケーションBlazorのマーカーを含みます。 ユーザー エージェントが起動すると、このマーカーを使用してアプリをBlazorブートストラップします。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | サーバー アプリのマーカーをBlazorレンダリングします。 コンポーネントからの出力は含まれません。 ユーザー エージェントが起動すると、このマーカーを使用してアプリをBlazorブートストラップします。 |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | コンポーネントを静的 HTML にレンダリングします。 |

ページとビューはコンポーネントを使用できますが、逆は当てはまりません。 コンポーネントは、部分的なビューやセクションなどのビュー固有の機能やページ固有の機能を使用できません。 コンポーネントの部分ビューからロジックを使用するには、部分ビュー ロジックをコンポーネントに組み込みます。

静的 HTML ページからのサーバー コンポーネントのレンダリングはサポートされていません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
