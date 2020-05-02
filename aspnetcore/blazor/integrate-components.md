---
title: ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する
author: guardrex
description: Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 4e2103b7e8b65478808093d7a31e8cfe29b04984
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558920"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

Razor コンポーネントは、Razor Pages と MVC アプリに統合できます。 ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。

[アプリを準備](#prepare-the-app)した後に、アプリの要件に応じて、次のセクションのガイダンスを使用します。

* ルーティング可能なコンポーネント &ndash; ユーザー要求から直接ルーティング可能なコンポーネント。 訪問者が [`@page`](xref:mvc/views/razor#page) ディレクティブを含むコンポーネントのブラウザーで HTTP 要求を行うことができる必要がある場合は、このガイダンスに従ってください。
  * [Razor Pages アプリでルーティング可能なコンポーネントを使用する](#use-routable-components-in-a-razor-pages-app)
  * [MVC アプリでルーティング可能なコンポーネントを使用する](#use-routable-components-in-an-mvc-app)
* [ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view) &ndash; ユーザー要求から直接ルーティング可能なコンポーネント。 アプリによって[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を含む既存のページやビューにコンポーネントが埋め込まれる場合は、このガイダンスに従ってください。

## <a name="prepare-the-app"></a>アプリの準備

既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。

1. アプリのレイアウト ファイル ( *_Layout.cshtml*) で次のことを行います。

   * 次の `<base>` タグを `<head>` 要素に追加します。

     ```html
     <base href="~/" />
     ```

     前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。 アプリがサブアプリケーションになっている場合は、記事 <xref:host-and-deploy/blazor/index#app-base-path> の「*アプリのベース パス*」セクションのガイダンスに従ってください。

     *_Layout.cshtml* ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。

   * *blazor.server.js* スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加します。

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     フレームワークによって *blazor.server.js* スクリプトがアプリに追加されます。 手動でアプリにスクリプトを追加する必要はありません。

1. 次の内容を含む *_Imports.razor* ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. `Startup.ConfigureServices` で、Blazor Server サービスを登録します。

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. コンポーネントを任意のページまたはビューに統合します。 詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages アプリでルーティング可能なコンポーネントを使用する

*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*

Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。

1. 次の内容の *App.razor* ファイルをプロジェクト ルートに追加します。

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 次の内容の *_Host.cshtml* ファイルを *Pages* フォルダーに追加します。

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。

   * ページに事前レンダリングするかどうか。
   * ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

   | 表示モード | 説明 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` コンポーネントを静的 HTML にレンダリングし、Blazor サーバー アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 `App` コンポーネントからの出力は含まれません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` コンポーネントを静的 HTML にレンダリングします。 |

   コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

1. *_Host.cshtml* ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. ルーティング可能なコンポーネントをアプリに追加します。 次に例を示します。

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。

## <a name="use-routable-components-in-an-mvc-app"></a>MVC アプリでルーティング可能なコンポーネントを使用する

*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*

MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。

1. 「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。

1. 次の内容の *App.razor* ファイルを、プロジェクトのルートに追加します。

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 次の内容の *_Host.cshtml* ファイルを *Views/Home* フォルダーに追加します。

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。

   * ページに事前レンダリングするかどうか。
   * ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。

   | 表示モード | 説明 |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` コンポーネントを静的 HTML にレンダリングし、Blazor サーバー アプリのマーカーを含めます。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Server アプリのマーカーをレンダリングします。 `App` コンポーネントからの出力は含まれません。 このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。 |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` コンポーネントを静的 HTML にレンダリングします。 |

   コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

1. Home コントローラーにアクションを追加します。

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `Startup.Configure` 内のエンドポイント構成に *_Host.cshtml* ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *Pages* フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。 次に例を示します。

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。

## <a name="render-components-from-a-page-or-view"></a>ページまたはビューからコンポーネントをレンダリングする

*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*

ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。

### <a name="render-stateful-interactive-components"></a>ステートフル対話型コンポーネントをレンダリングする

Razor ページまたはビューには、ステートフル対話型コンポーネントを追加できます。

ページまたはビューがレンダリングされると、次の処理が行われます。

* ページまたはビューと共にコンポーネントがプリレンダリングされます。
* プリレンダリングに使用された初期のコンポーネント状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネント状態が作成されます。

次の Razor ページには、`Counter` コンポーネントがレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

### <a name="render-noninteractive-components"></a>非対話型コンポーネントをレンダリングする

次の Razor ページには、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。 コンポーネントは静的にレンダリングされるため、コンポーネントは対話型ではありません。

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。

## <a name="component-namespaces"></a>コンポーネントの名前空間

カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、 *_ViewImports.cshtml* ファイルに追加します。 次に例を示します。

* `MyAppNamespace` をアプリの名前空間に変更します。
* コンポーネントを保持するために *Components* という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports.cshtml* ファイルは、Razor Pages アプリの *Pages* フォルダーまたは MVC アプリの *Views* フォルダーにあります。

詳細については、「<xref:blazor/components#import-components>」を参照してください。
