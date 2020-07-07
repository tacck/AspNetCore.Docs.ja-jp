---
title: ASP.NET Core Blazor データ バインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディング機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 35873e57171b4d86affcb475ad2d55aef443d3b5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399180"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor データ バインディング

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントはフィールド、プロパティ、または Razor 式の値が含まれる "[`@bind`](xref:mvc/views/razor#bind)" という名前の HTML 要素属性を使用してデータ バインディング機能を提供します。

次の例では、`CurrentValue` プロパティをテキスト ボックスの値にバインドします。

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

テキスト ボックスがフォーカスを失うと、プロパティの値が更新されます。

テキスト ボックスは、プロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。 コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、プロパティの更新が UI に反映されます。

`CurrentValue` プロパティで [`@bind`](xref:mvc/views/razor#bind) を使用する (`<input @bind="CurrentValue" />`) ことは、本質的に次の内容と同じです。

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

コンポーネントがレンダリングされると、入力要素の `value` は `CurrentValue` プロパティから取得されます。 ユーザーがテキスト ボックスに入力し、要素のフォーカスを変更すると、`onchange` イベントが発生し、`CurrentValue` プロパティが変更された値に設定されます。 実際には、[`@bind`](xref:mvc/views/razor#bind) は型変換が行われるケースを扱うため、コード生成はより複雑になります。 原則として、[`@bind`](xref:mvc/views/razor#bind) は、式の現在の値を `value` 属性と関連付け、登録されたハンドラーを使用して変更を処理します。

他のイベントのプロパティまたはフィールドをバインドするには、`event` パラメーターを含めた `@bind:event` 属性を含めます。 次の例は、`CurrentValue` プロパティを `oninput` イベントにバインドします。

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

要素がフォーカスを失ったときに発生する `onchange`とは異なり、テキスト ボックスの値が変更されたときに `oninput` が発生します。

`value` 以外の要素の属性をバインドするには、`@bind-{ATTRIBUTE}:event` 構文で `@bind-{ATTRIBUTE}` を使用します。 次の例では、`paragraphStyle` の値が変更されると段落のスタイルが更新されます。

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

属性バインディングでは大文字と小文字が区別されます。

* `@bind` は有効です。
* `@Bind` および `@BIND` は無効です。

## <a name="unparsable-values"></a>解析不可能値

ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。

次のシナリオについて検討してください。

* `<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。

上のシナリオでは、要素の値が `123`に戻されます。 値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。

既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。 別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。 `oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。 `int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。 `.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。 `oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。 代替手段は次のとおりです。

* `oninput` イベントは使用しません。 既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。
* `int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> や <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。 フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。 フォーム検証コンポーネントを使うと、次のことができます。
  * ユーザーの無効な入力を許可し、関連付けられた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証エラーを受信します。
  * ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。

## <a name="format-strings"></a>書式指定文字列

データ バインディングは、`@bind:format` を使用して <xref:System.DateTime> 形式の文字列を処理します。 通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。 `@bind:format` は、次の .NET 型のバインドをサポートしています。

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。 この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。

Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。 この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく動作します。

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>コンポーネント パラメーターを使用した親から子へのバインディング

バインディングは、コンポーネントのパラメーターを認識します。`@bind-{PROPERTY}` は、親コンポーネントのプロパティ値を子コンポーネントにバインドできます。 子から親へのバインドについては、「[チェーン バインドを使用した子から親へのバインディング](#child-to-parent-binding-with-chained-bind)」セクションを参照してください。

次の子コンポーネント (`ChildComponent`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<xref:Microsoft.AspNetCore.Components.EventCallback%601> についての説明は、<xref:blazor/components/event-handling#eventcallback>にあります。

次の親コンポーネントは、以下を使用します。

* `ChildComponent` を使用して親の `ParentYear` パラメーターを子コンポーネントの `Year` パラメーターにバインドします。
* `onclick` イベントは、`ChangeTheYear` メソッドをトリガーするために使用します。 詳細については、「<xref:blazor/components/event-handling>」を参照してください。

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

`ParentComponent` を読み込むと、次のマークアップが生成されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentComponent` のボタンを選択して `ParentYear` プロパティの値を変更すると、`ChildComponent` の `Year` プロパティが更新されます。 `Year` の新しい値は、`ParentComponent` が再度レンダリングされたときに UI に表示されます。

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。

慣例として、`<ChildComponent @bind-Year="ParentYear" />` は基本的に以下の書き込みと同じです。

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

一般に、プロパティに `@bind-{PROPRETY}:event` 属性を含めることで、対応するイベント ハンドラーにバインドできます。 たとえば、プロパティ `MyProp` は、次の 2 つの属性を使用して `MyEventHandler` にバインドできます。

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>チェーン バインドを使用した子から親へのバインディング

一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。 このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。

チェーン バインドは、ページの要素で [`@bind`](xref:mvc/views/razor#bind) 構文を使用して実装することはできません。 イベント ハンドラーと値は、個別に指定する必要があります。 ただし、親コンポーネントでは、[`@bind`](xref:mvc/views/razor#bind) 構文をコンポーネントのパラメーターと共に使用できます。

次の `PasswordField` コンポーネント (`PasswordField.razor`) では、次を実行します。

* `Password` プロパティに `<input>` 要素の値を設定します。
* [`EventCallback`](xref:blazor/components/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。
* `onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。 詳細については、「<xref:blazor/components/event-handling>」を参照してください。

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField` コンポーネントは、別のコンポーネントで使用されます。

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

上の例で、パスワードの確認またはエラーの捕捉を行うには、次の手順を実行します。

* `Password` のバッキング フィールドを作成します (次のコード例では、`password`)。
* `Password` セッターで、確認またはエラーの捕捉を行います。

次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a>ラジオ ボタン

フォームのラジオ ボタンへのバインドの詳細については、「<xref:blazor/forms-validation#work-with-radio-buttons>」を参照してください。
