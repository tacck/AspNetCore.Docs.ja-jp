---
title: ASP.NET Core Blazor データ バインディング
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディング機能について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3f823ca9cf96b7ff439ade59f946db222b7f7e60
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606695"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor データ バインディング

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Steve Sanderson](https://github.com/SteveSandersonMS)

Razor コンポーネントはフィールド、プロパティ、または Razor 式の値が含まれる "[`@bind`](xref:mvc/views/razor#bind)" という名前の HTML 要素属性を使用してデータ バインディング機能を提供します。

次の例では、`<input>` 要素を `currentValue` フィールドにバインドし、`<input>` 要素を `CurrentValue` プロパティにバインドします。

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

いずれかの要素がフォーカスを失うと、バインドされたフィールドまたはプロパティが更新されます。

テキスト ボックスは、フィールドまたはプロパティの値の変更に対する応答としてではなく、コンポーネントがレンダリングされたときにのみ UI が更新されます。 コンポーネントはイベント ハンドラーのコードが実行された後に自身をレンダリングするため、*通常は*、イベント ハンドラーがトリガーされた直後に、フィールドとプロパティの更新が UI に反映されます。

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

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

属性バインディングでは大文字と小文字が区別されます。

* `@bind` は有効です。
* `@Bind` および `@BIND` は無効です。

## <a name="unparsable-values"></a>解析不可能値

ユーザーがデータバインド要素に解析できない値を指定すると、バインド イベントがトリガーされたときに、解析できない値は自動的に前の値に戻されます。

次のシナリオについて検討してください。

* `<input>` 要素は、初期値 `123` を持つ `int` 型にバインドされます。

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* ユーザーは、ページで要素の値を `123.45` に更新し、要素のフォーカスを変更します。

上のシナリオでは、要素の値が `123`に戻されます。 値 `123.45` が拒否されて元の値 `123` が設定された場合、ユーザーはその値が受け入れられなかったことを認識します。

既定では、バインドは要素の `onchange` イベントに適用されます (`@bind="{PROPERTY OR FIELD}"`)。 別のイベントでバインドをトリガーするには、`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` を使用します。 `oninput` イベント (`@bind:event="oninput"`) の場合、解析できない値が入力されたキーストロークの後に元に戻されます。 `int` にバインドされた型の `oninput` イベントを対象とする場合、ユーザーが `.` 文字を入力することはできません。 `.` 文字はすぐに削除されるので、ユーザーは整数のみが許可されるというフィードバックをすぐに受け取ることができます。 `oninput` イベントで値を元に戻すのが理想的ではないシナリオもあります。たとえば、ユーザーが解析できない `<input>` 値をクリアできる必要がある場合などです。 代替手段は次のとおりです。

* `oninput` イベントは使用しません。 既定の `onchange` イベント (`@bind="{PROPERTY OR FIELD}"` のみを指定) を使用します。この場合、要素がフォーカスを失うまで無効な値は元に戻されません。
* `int?` や `string` などの null 許容型にバインドし、無効なエントリを処理するカスタム ロジックを提供します。
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> や <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> などの[フォーム検証コンポーネント](xref:blazor/forms-validation)を使用します。 フォーム検証コンポーネントには、無効な入力を管理するためのサポートが組み込まれています。 詳細については、「<xref:blazor/forms-validation>」を参照してください。 フォーム検証コンポーネントを使うと、次のことができます。
  * ユーザーの無効な入力を許可し、関連付けられた <xref:Microsoft.AspNetCore.Components.Forms.EditContext> で検証エラーを受信します。
  * ユーザーが追加の Web フォーム データを入力することを妨げることなく、UI に検証エラーを表示します。

## <a name="format-strings"></a>書式指定文字列

データ バインディングは、`@bind:format` を使用して <xref:System.DateTime> 形式の文字列を処理します。 通貨形式や数値形式など、その他の書式指定式は現時点では使用できません。

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

上のコードでは、`<input>` 要素のフィールドの種類 (`type`) は既定で `text` に設定されています。 `@bind:format` は、次の .NET 型のバインドをサポートしています。

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 属性には、`<input>` 要素の `value` に適用する日付形式を指定します。 この形式は、`onchange` イベントが発生したときに値を解析するためにも使用されます。

Blazor には日付の書式を設定するためのサポートが組み込まれているため、`date` フィールド型の形式を指定することは推奨されません。 この推奨事項には反しますが、`date` フィールド型で形式を指定する場合は、`yyyy-MM-dd` 日付形式を使用してバインドしたのみ正しく機能します。

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>コンポーネント パラメーターを使用した親から子へのバインディング

コンポーネント パラメーターを使用すると、`@bind-{PROPERTY OR FIELD}` 構文を使用して親コンポーネントのプロパティとフィールドをバインドできます。

次の `Child` コンポーネント (`Shared/Child.razor`) には、`Year` コンポーネント パラメーターと `YearChanged` コールバックがあります。

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

コールバック (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) は、コンポーネント パラメーター名の後に "`Changed`" サフィックスを付けた名前 (`{PARAMETER NAME}Changed`) にする必要があります。 上の例では、コールバックの名前は `YearChanged` です。 <xref:Microsoft.AspNetCore.Components.EventCallback%601> の詳細については、「<xref:blazor/components/event-handling#eventcallback>」を参照してください。

次の `Parent` コンポーネント (`Parent.razor`) では、`year` フィールドが子コンポーネントの `Year` パラメーターにバインドされています。

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

`Year` パラメーターには、`Year` パラメーターの型と一致するコンパニオン `YearChanged` イベントがあるため、バインド可能です。

慣例により、ハンドラーに割り当てられた `@bind-{PROPERTY}:event` 属性を含めることで、プロパティを対応するイベント ハンドラーにバインドできます。 `<Child @bind-Year="year" />` は次のように書く場合と同じです。

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>チェーン バインドを使用した子から親へのバインディング

一般的なシナリオでは、データにバインドされたパラメーターをコンポーネントの出力のページ要素に連結します。 このシナリオは、複数のレベルのバインドが同時に発生するため、*チェーン バインド*と呼ばれます。

チェーン バインドは、子コンポーネントで [`@bind`](xref:mvc/views/razor#bind) 構文を使用して実装することはできません。 イベント ハンドラーと値は、個別に指定する必要があります。 ただし、親コンポーネントでは、[`@bind`](xref:mvc/views/razor#bind) 構文を子コンポーネントのパラメーターと共に使用できます。

次の `PasswordField` コンポーネント (`PasswordField.razor`) では、次を実行します。

* `password` フィールドに `<input>` 要素の値を設定します。
* 子の `password` フィールドの現在の値を引数として渡す [`EventCallback`](xref:blazor/components/event-handling#eventcallback) を使用して、`Password` プロパティの変更を親コンポーネントに公開します。
* `onclick` イベントを使用して、`ToggleShowPassword` メソッドをトリガーします。 詳細については、「<xref:blazor/components/event-handling>」を参照してください。

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField` コンポーネントは、別のコンポーネントで使用されます。

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

バインドのデリゲートを呼び出すメソッドで、チェックを実行またはエラーをトラップします。 次の例では、パスワードの値にスペースが使用されている場合、すぐにユーザーにフィードバックを提供します。

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="bind-across-more-than-two-components"></a>3 つ以上のコンポーネント間でバインドする

入れ子になった任意の数のコンポーネントをバインドできますが、次のような一方向のデータ フローを考慮する必要があります。

* 変更通知は "*階層をフローアップ*" します。
* 新しいパラメーター値は "*階層をフローダウン*" します。

一般的な推奨される方法は、基になるデータを親コンポーネントに格納のみすることであり、これにより、更新する必要がある状態に関する混乱を避けることができます。

次のコンポーネントは、前述の概念を示しています。

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

必ずしも入れ子にはなっていないコンポーネント間でメモリ内のデータを共有するのに適した別の方法については、「ASP.NET Core Blazor 状態管理<xref:blazor/state-management#in-memory-state-container-service>」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

* [フォーム内のラジオ ボタンへのバインド](xref:blazor/forms-validation#radio-buttons)
* [フォーム内の C# オブジェクトの `null` 値への `<select>` 要素オプションのバインド](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
