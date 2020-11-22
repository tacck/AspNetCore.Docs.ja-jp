---
title: ASP.NET Core Blazor テンプレート
author: guardrex
description: ASP.NET Core Blazor アプリ テンプレートと Blazor プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703723"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="125b0-103">ASP.NET Core Blazor テンプレート</span><span class="sxs-lookup"><span data-stu-id="125b0-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="125b0-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="125b0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="125b0-105">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="125b0-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="125b0-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="125b0-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="125b0-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="125b0-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="125b0-108">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="125b0-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="125b0-109">`--help` オプションを [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI コマンドに渡すことで、テンプレート オプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="125b0-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="125b0-110">Blazor プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="125b0-110">Blazor project structure</span></span>

<span data-ttu-id="125b0-111">Blazor プロジェクト テンプレートから生成された Blazor アプリは、次のファイルとフォルダーで構成されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="125b0-112">`Program.cs`:以下を設定するアプリのエントリ ポイント。</span><span class="sxs-lookup"><span data-stu-id="125b0-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="125b0-113">ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="125b0-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="125b0-114">WebAssembly ホスト (Blazor WebAssembly):このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。</span><span class="sxs-lookup"><span data-stu-id="125b0-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="125b0-115">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="125b0-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="125b0-116">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("#app")`) に対する `app` DOM 要素 (`wwwroot/index.html` の `<div id="app">Loading...</div>`) として指定されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="125b0-117">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="125b0-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="125b0-118">`Program.cs`:以下を設定するアプリのエントリ ポイント。</span><span class="sxs-lookup"><span data-stu-id="125b0-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="125b0-119">ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="125b0-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="125b0-120">WebAssembly ホスト (Blazor WebAssembly):このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。</span><span class="sxs-lookup"><span data-stu-id="125b0-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="125b0-121">`App` コンポーネントは、アプリのルート コンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="125b0-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="125b0-122">`App` コンポーネントは、ルート コンポーネント コレクション (`builder.RootComponents.Add<App>("app")`) に対する `app` DOM 要素 (`wwwroot/index.html` の `<app>Loading...</app>`) として指定されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="125b0-123">[サービス](xref:blazor/fundamentals/dependency-injection)が追加され、構成されます (例: `builder.Services.AddSingleton<IMyDependency, MyDependency>()`)。</span><span class="sxs-lookup"><span data-stu-id="125b0-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="125b0-124">`Startup.cs` (Blazor Server): アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="125b0-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="125b0-125">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="125b0-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="125b0-126">`ConfigureServices`:アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="125b0-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="125b0-127">Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="125b0-128">`Configure`:アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="125b0-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="125b0-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="125b0-130">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="125b0-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="125b0-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリ (`Pages/_Host.cshtml`) のルート ページを設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="125b0-132">`wwwroot/index.html` (Blazor WebAssembly): HTML ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="125b0-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="125b0-133">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="125b0-134">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="125b0-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="125b0-135">コンポーネントは `app` DOM 要素 (`<app>...</app>`) の位置に表示されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="125b0-136">`_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。</span><span class="sxs-lookup"><span data-stu-id="125b0-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="125b0-137">.NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。</span><span class="sxs-lookup"><span data-stu-id="125b0-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="125b0-138">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="125b0-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="125b0-139">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="125b0-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="125b0-140"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="125b0-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="125b0-141">`Pages` フォルダー:Blazor アプリと Blazor Server アプリのルート Razor ページを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="125b0-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="125b0-142">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="125b0-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="125b0-143">テンプレートには以下が含まれています。</span><span class="sxs-lookup"><span data-stu-id="125b0-143">The template includes the following:</span></span>
  * <span data-ttu-id="125b0-144">`_Host.cshtml` (Blazor Server): Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="125b0-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="125b0-145">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="125b0-146">ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="125b0-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="125b0-147">[ホスト] ページは、ルート `App` コンポーネント (`App.razor`) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="125b0-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="125b0-148">`Counter` コンポーネント (`Pages/Counter.razor`): カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="125b0-148">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="125b0-149">`Error` コンポーネント (`Error.razor`、Blazor Server アプリのみ): アプリでハンドルされない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="125b0-149">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="125b0-150">`FetchData` コンポーネント (`Pages/FetchData.razor`): フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="125b0-150">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="125b0-151">`Index` コンポーネント (`Pages/Index.razor`): ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="125b0-151">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="125b0-152">`Properties/launchSettings.json`:[開発環境の構成](xref:fundamentals/environments#development-and-launchsettingsjson)を保持します。</span><span class="sxs-lookup"><span data-stu-id="125b0-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="125b0-153">`Shared` フォルダー:アプリで使用する他の UI コンポーネント (`.razor`) を含みます。</span><span class="sxs-lookup"><span data-stu-id="125b0-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="125b0-154">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="125b0-154">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="125b0-155">`MainLayout.razor.css`: アプリのメイン レイアウト用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="125b0-155">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="125b0-156">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="125b0-156">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="125b0-157">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="125b0-157">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="125b0-158"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="125b0-158">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="125b0-159">`NavMenu.razor.css`: アプリのナビゲーション メニュー用のスタイルシート。</span><span class="sxs-lookup"><span data-stu-id="125b0-159">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="125b0-160">`Shared` フォルダー:アプリで使用する他の UI コンポーネント (`.razor`) を含みます。</span><span class="sxs-lookup"><span data-stu-id="125b0-160">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="125b0-161">`MainLayout` コンポーネント (`MainLayout.razor`): アプリの[レイアウト コンポーネント](xref:blazor/layouts)。</span><span class="sxs-lookup"><span data-stu-id="125b0-161">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="125b0-162">`NavMenu` コンポーネント (`NavMenu.razor`): サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="125b0-162">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="125b0-163">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="125b0-163">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="125b0-164"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="125b0-164">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="125b0-165">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="125b0-165">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="125b0-166">`Data` フォルダー (Blazor Server):アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="125b0-166">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="125b0-167">`wwwroot`:アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="125b0-167">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="125b0-168">`appsettings.json`:アプリの[構成設定](xref:blazor/fundamentals/configuration)を保持します。</span><span class="sxs-lookup"><span data-stu-id="125b0-168">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="125b0-169">Blazor WebAssembly アプリの場合、アプリ設定ファイルは `wwwroot` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="125b0-169">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="125b0-170">Blazor Server アプリの場合、アプリ設定ファイルはプロジェクト ルートにあります。</span><span class="sxs-lookup"><span data-stu-id="125b0-170">In a Blazor Server app, the app settings file is located at the project root.</span></span>
