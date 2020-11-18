---
title: ASP.NET Core Blazor のルーティング
author: guardrex
description: アプリで要求をルーティングする方法と、NavLink コンポーネントについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
no-loc:
- appsettings.json
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 8f0aa80d092b6678131a2b7152f21ecb8e168257
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430992"
---
# <a name="aspnet-core-no-locblazor-routing"></a>ASP.NET Core Blazor のルーティング

作成者: [Luke Latham](https://github.com/guardrex)

Blazor アプリで、要求をルーティングする方法と、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用してナビゲーション リンクを作成する方法について説明します。

## <a name="aspnet-core-endpoint-routing-integration"></a>ASP.NET Core エンドポイントのルーティングの統合

Blazor Server は [ASP.NET Core エンドポイントのルーティング](xref:fundamentals/routing)に統合されています。 ASP.NET Core アプリは、`Startup.Configure` で <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> を使用して、対話型コンポーネントの着信接続を受け入れるように構成します。

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

最も一般的な構成は、すべての要求を Razor ページにルーティングすることです。これは、Blazor Server アプリのサーバー側部分のホストとして機能します。 通常、*ホスト* ページは、`_Host.cshtml` という名前になります。 ホスト ファイルに指定されるルートは、ルート照合で低い優先順位で動作するため、*フォールバック ルート* と呼ばれます。 フォールバック ルートは、他のルートが一致しない場合に考慮されます。 これにより、Blazor Server アプリと干渉することなく、他のコントローラーやページをアプリで使用できます。

ルート以外の URL のサーバー ホスト用に <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> を構成する方法の詳細については、<xref:blazor/host-and-deploy/index#app-base-path> をご覧ください。

## <a name="route-templates"></a>ルート テンプレート

<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントでは、指定されたルートによる各コンポーネントへのルーティングが可能になります。 <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは `App.razor` ファイルに表示されます。

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

`@page` ディレクティブを含む `.razor` ファイルがコンパイルされると、生成されたクラスに、ルート テンプレートを指定する <xref:Microsoft.AspNetCore.Components.RouteAttribute> が指定されます。

実行時に、<xref:Microsoft.AspNetCore.Components.RouteView> コンポーネントは、

* <xref:Microsoft.AspNetCore.Components.Routing.Router> から、必要なパラメーターと共に <xref:Microsoft.AspNetCore.Components.RouteData> を受け取ります。
* 指定されたパラメーターを使用して、指定されたコンポーネントを、そのレイアウト (または任意の既定のレイアウト) でレンダリングします。

必要に応じて、レイアウト クラスで <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> パラメーターを指定して、レイアウトを指定しないコンポーネントに使用できます。 既定の Blazor テンプレートでは、`MainLayout` コンポーネントを指定しています。 `MainLayout.razor` はテンプレート プロジェクトの `Shared` フォルダーにあります。 レイアウトの詳細については、「<xref:blazor/layouts>」を参照してください。

コンポーネントには、複数のルート テンプレートを適用できます。 次のコンポーネントは、`/BlazorRoute` と `/DifferentBlazorRoute` に対する要求に応答します。

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> URL が正しく解決されるように、アプリでは、`href` 属性に指定されているアプリのベース パス (`<base href="/">`) を使用して、その `wwwroot/index.html` ファイル (Blazor WebAssembly) または `Pages/_Host.cshtml` ファイル (Blazor Server) に `<base>` タグを含める必要があります。 詳細については、「<xref:blazor/host-and-deploy/index#app-base-path>」を参照してください。

## <a name="provide-custom-content-when-content-isnt-found"></a>コンテンツが見つからないときにカスタム コンテンツを提供する

<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用すると、要求されたルートでコンテンツが見つからない場合に、アプリでカスタム コンテンツを指定できます。

`App.razor` ファイルで、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントの <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> テンプレート パラメーターにカスタム コンテンツを設定します。

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

`<NotFound>` タグのコンテンツには、他の対話型コンポーネントなど、任意の項目を含めることができます。 <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> コンテンツに既定のレイアウトを適用するには、「<xref:blazor/layouts>」を参照してください。

## <a name="route-to-components-from-multiple-assemblies"></a>複数のアセンブリからコンポーネントにルーティングする

<xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> パラメーターを使用して、<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントで、ルーティング可能なコンポーネントを検索するときに考慮する追加のアセンブリを指定します。 指定されたアセンブリは、`AppAssembly` に指定されたアセンブリに加えて考慮されます。 次の例では、`Component1` は、参照されているクラス ライブラリに定義されているルーティング可能なコンポーネントです。 次の <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> の例では、`Component1` のルーティング サポートの結果を示しています。

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>ルート パラメーター

ルーターは、ルート パラメーターを使用して、同じ名前の対応するコンポーネント パラメーターを設定します (大文字と小文字は区別されません)。

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

省略可能なパラメーターはサポートされていません。 前の例では、2 つの `@page` ディレクティブが適用されています。 1 つ目は、パラメーターを指定せずにコンポーネントへの移動を許可します。 2 番目の `@page` ディレクティブは、`{text}` ルート パラメーターを受け取り、その値を `Text` プロパティに割り当てます。

## <a name="route-constraints"></a>ルート制約

ルート制約は、コンポーネントへのルート セグメントに型の一致を適用します。

次の例で、`Users` コンポーネントへのルートは、次の場合にのみ一致します。

* 要求 URL に `Id` ルート セグメントが存在する。
* `Id` セグメントは整数 (`int`) である。

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

次の表に示すルート制約を使用できます。 インバリアント カルチャと一致するルート制約については、表の下の警告で詳細を確認してください。

| 制約 | 例           | 一致の例                                                                  | インバリアント<br>カルチャ<br>一致 |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | いいえ                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | はい                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | はい                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | はい                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | はい                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | いいえ                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | はい                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | はい                              |

> [!WARNING]
> URL の妥当性を検証し、CLR 型 (`int` や <xref:System.DateTime> など) に変換されるルート制約では、常にインバリアント カルチャが使用されます。 これらの制約では、URL がローカライズ不可であることが前提となります。

### <a name="routing-with-urls-that-contain-dots"></a>ドットを含む URL によるルーティング

ホストされている Blazor WebAssembly および Blazor Server アプリの場合、サーバー側の既定のルート テンプレートでは、ファイルが要求されているドット (`.`) が要求 URL の最後のセグメントに含まれていると想定されます (例: `https://localhost.com:5001/example/some.thing`)。 追加の構成がなければ、コンポーネントへのルーティングが意図されている場合は、アプリによって "*404 - 見つかりません*" が返されます。 ドットが含まれる 1 つ以上のパラメーターを指定してルートを使用するには、アプリでカスタム テンプレートを使用してルートを構成する必要があります。

URL の最後のセグメントからルート パラメーターを受け取ることができる次の `Example` コンポーネントについて考えてみます。

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

ホストされている Blazor WebAssembly ソリューションの "*サーバー*" アプリで、`param` パラメーターのドットを使用して要求をルーティングできるようにするには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ファイル ルート テンプレートを追加します。

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

`param` パラメーターのドットを使用して要求をルーティングするように Blazor Server アプリを構成するには、`Startup.Configure` (`Startup.cs`) で省略可能なパラメーターを使用して、フォールバック ページ ルート テンプレートを追加します。

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

詳細については、「<xref:fundamentals/routing>」を参照してください。

## <a name="catch-all-route-parameters"></a>キャッチオールのルート パラメーター

::: moniker range=">= aspnetcore-5.0"

"*このセクションは、.NET 5 リリース候補 1 (RC1) 以降の ASP.NET Core に適用されます。* "

複数のフォルダー境界にまたがるパスをキャプチャするキャッチオール ルート パラメーターが、コンポーネントでサポートされます。 キャッチオール ルート パラメーターは次のとおりであることが必要です。

* ルート セグメント名と一致する名前が付けられている。 名前付けで大文字と小文字は区別されない。
* `string` 型。 フレームワークによって自動キャストは提供されません。
* URL の末尾。

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

ルート テンプレートが `/page/{*pageRoute}` の URL `/page/this/is/a/test` の場合、`PageRoute` の値は `this/is/a/test` に設定されます。

キャプチャされたパスのスラッシュとセグメントはデコードされます。 `/page/{*pageRoute}` のルート テンプレートでは、URL `/page/this/is/a%2Ftest%2A` から `this/is/a/test*` が生成されます。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

キャッチオール ルート パラメーターは、ASP.NET Core 5.0 以降でサポートされます。

::: moniker-end

## <a name="navlink-component"></a>NavLink コンポーネント

ナビゲーション リンクを作成するときは、HTML ハイパーリンク要素 (`<a>`) の代わりに <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントを使用します。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは `<a>` 要素のように動作しますが、`href` が現在の URL と一致するかどうかに基づいて `active` CSS クラスを切り替える点が異なります。 `active` クラスは、表示されているナビゲーション リンクの中でどのページがアクティブ ページであるかをユーザーが理解するのに役立ちます。 必要に応じて、CSS クラス名を <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> に割り当てて、現在のルートが `href` と一致したときに、レンダリングされるリンクにカスタム CSS クラスを適用します。

次の `NavMenu` コンポーネントでは、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントの使用方法を示す [`Bootstrap`](https://getbootstrap.com/docs/) ナビゲーション バーを作成しています。

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

`<NavLink>` 要素の `Match` 属性に割り当てられる 2 つの <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> オプションがあります。

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>:<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL 全体に一致する場合にアクティブになります。
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*既定値*):<xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、現在の URL の任意のプレフィックスに一致する場合にアクティブになります。

前の例では、ホーム <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` はホーム URL と一致し、アプリの既定のベース パス URL (`https://localhost:5001/` など) でのみ `active` CSS クラスを受け取ります。 2 番目の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> は、ユーザーが `MyComponent` プレフィックスを含む任意の URL (`https://localhost:5001/MyComponent` や `https://localhost:5001/MyComponent/AnotherSegment` など) にアクセスしたときに、`active` クラスを受け取ります。

追加の <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネント属性は、レンダリングされるアンカー タグに渡されます。 次の例では、<xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントに `target` 属性が含まれています。

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

次の HTML マークアップがレンダリングされます。

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> Blazor による子コンテンツのレンダリング方法により、`for` ループ内の `NavLink` コンポーネントのレンダリングでは、インクリメントするループ変数が `NavLink` (子) コンポーネントのコンテンツ内で使用されている場合、ローカル インデックス変数が必要になります。
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> このシナリオでのインデックス変数の使用は、`NavLink` コンポーネントだけでなく、[子コンテンツ](xref:blazor/components/index#child-content)でループ変数を使用する **すべての** 子コンポーネントで必須です。
>
> または、<xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> と共に `foreach` ループを使用します。
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="uri-and-navigation-state-helpers"></a>URI およびナビゲーション状態ヘルパー

C# コード内の URI とナビゲーションを操作するには、<xref:Microsoft.AspNetCore.Components.NavigationManager> を使用します。 <xref:Microsoft.AspNetCore.Components.NavigationManager> には、次の表に示すイベントとメソッドがあります。

| メンバー | 説明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | 現在の絶対 URI を取得します。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | 絶対 URI を生成するために、相対 URI パスの前に付加できるベース URI (末尾のスラッシュを含む) を取得します。 通常、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> は `wwwroot/index.html` (Blazor WebAssembly) または `Pages/_Host.cshtml` (Blazor Server) 内のドキュメントの `<base>` 要素の `href` 属性に対応します。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | 指定された URI に移動します。 `forceLoad` が `true` の場合:<ul><li>クライアント側のルーティングはバイパスされます。</li><li>URI が通常クライアント側ルーターによって処理されるかどうかにかかわらず、ブラウザーでは、強制的にサーバーから新しいページが読み込まれます。</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | ナビゲーションの場所が変更されたときに発生するイベントです。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | 相対 URI を絶対 URI に変換します。 |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | ベース URI (たとえば、<xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> によって以前に返された URI) が与えられると、絶対 URI を、ベース URI プレフィックスに相対的な URI に変換します。 |

次のコンポーネントは、ボタンが選択されたときに、アプリの `Counter` コンポーネントに移動します。

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

次のコンポーネントでは、<xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> をサブスクライブすることで、場所の変更イベントが処理されます。 `HandleLocationChanged` メソッドは、`Dispose` がフレームワークによって呼び出されると、アンフックになります。 このメソッドをアンフックすることで、コンポーネントのガベージ コレクションが許可されます。

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> は、イベントに関する次の情報を提供します。

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>:新しい場所の URL。
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>:`true` の場合、Blazor によってブラウザーからナビゲーションがインターセプトされました。 `false` の場合、<xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> によってナビゲーションが発生しました。

コンポーネントの破棄の詳細については、「<xref:blazor/components/lifecycle#component-disposal-with-idisposable>」を参照してください。

## <a name="query-string-and-parse-parameters"></a>クエリ文字列とパラメーターの解析

要求のクエリ文字列は、<xref:Microsoft.AspNetCore.Components.NavigationManager> の <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> プロパティから取得できます。

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

クエリ文字列のパラメーターを解析するには:

* [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) のパッケージ参照を追加します。
* <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> を使用してクエリ文字列を解析した後に値を取得します。

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

前の例のプレースホルダー `{KEY}` は、クエリ文字列パラメーターのキーです。 たとえば、URL のキーと値のペア `?ship=Tardis` では、キー `ship` が使用されます。
