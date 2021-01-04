---
title: ASP.NET Core Blazor プロジェクトの構造
author: guardrex
description: ASP.NET Core Blazor アプリ プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513533"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor プロジェクトの構造

作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)

Blazor プロジェクト テンプレートから生成された Blazor アプリは、次のファイルとフォルダーで構成されます。

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`:以下を設定するアプリのエントリ ポイント。

  * ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly ホスト (Blazor WebAssembly):このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。
    * `App` コンポーネントは、アプリのルート コンポーネントです。 `App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("#app")`) に対する `app` の `id` (`wwwroot/index.html` の `<div id="app">Loading...</div>`) を持つ `div` DOM 要素として指定されます。
    * [サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`:以下を設定するアプリのエントリ ポイント。

  * ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly ホスト (Blazor WebAssembly):このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。
    * `App` コンポーネントは、アプリのルート コンポーネントです。 `App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("app")`) に対する `app` DOM 要素 (`wwwroot/index.html` の `<app>Loading...</app>`) として指定されます。
    * [サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。

::: moniker-end

* `Startup.cs` (Blazor Server): アプリのスタートアップ ロジックを含みます。 `Startup` クラスには、次の 2 つのメソッドがあります。

  * `ConfigureServices`:アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。 Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。
  * `Configure`:アプリの要求処理パイプラインを構成します。
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。 この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリ (`Pages/_Host.cshtml`) のルート ページを設定し、ナビゲーションを有効にするために呼び出されます。

::: moniker range=">= aspnetcore-5.0"

* `wwwroot/index.html` (Blazor WebAssembly): HTML ページとして実装されるアプリのルート ページ。
  * アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。
  * このページは、ルート `App` コンポーネントを表示する場所を指定します。 コンポーネントは、`app` の `id` (`<div id="app">Loading...</div>`) を持つ `div` DOM 要素の位置にレンダリングされます。
  * `_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。
    * .NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。
    * アプリを実行するランタイムの初期化。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot/index.html` (Blazor WebAssembly): HTML ページとして実装されるアプリのルート ページ。
  * アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。
  * このページは、ルート `App` コンポーネントを表示する場所を指定します。 コンポーネントは `app` DOM 要素 (`<app>Loading...</app>`) の位置に表示されます。
  * `_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。
    * .NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。
    * アプリを実行するランタイムの初期化。

::: moniker-end

* `App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。 <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。

* `Pages` フォルダー:Blazor アプリと Blazor Server アプリのルート Razor ページを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。 各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。 テンプレートには以下が含まれています。
  * `_Host.cshtml` (Blazor Server): Razor ページとして実装されるアプリのルート ページ。
    * アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。
    * ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。
    * [ホスト] ページは、ルート `App` コンポーネント (`App.razor`) を表示する場所を指定します。
  * `Counter` コンポーネント (`Pages/Counter.razor`): カウンター ページを実装します。
  * `Error` コンポーネント (`Error.razor`、Blazor Server アプリのみ): アプリでハンドルされない例外が発生したときに表示されます。
  * `FetchData` コンポーネント (`Pages/FetchData.razor`): フェッチ データ ページを実装します。
  * `Index` コンポーネント (`Pages/Index.razor`): ホーム ページを実装します。
  
* `Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。

::: moniker range=">= aspnetcore-5.0"

* `Shared` フォルダー:アプリで使用する他の UI コンポーネント (`.razor`) を含みます。
  * `MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。
  * `MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。
  * `NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。 ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。
  * `NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` フォルダー:アプリで使用する他の UI コンポーネント (`.razor`) を含みます。
  * `MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。
  * `NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。 ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。 <xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。
  
::: moniker-end

* `_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。

* `Data` フォルダー (Blazor Server):アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装が含まれます。

* `wwwroot`:アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。

* `appsettings.json`:アプリの[構成設定](xref:blazor/fundamentals/configuration)を保持します。 Blazor WebAssembly アプリの場合、アプリ設定ファイルは `wwwroot` フォルダーにあります。 Blazor Server アプリの場合、アプリ設定ファイルはプロジェクト ルートにあります。
