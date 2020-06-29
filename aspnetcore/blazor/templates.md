---
title: ASP.NET Core Blazor テンプレート
author: guardrex
description: ASP.NET Core Blazor アプリ テンプレートと Blazor プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243214"
---
# <a name="aspnet-core-blazor-templates"></a><span data-ttu-id="69de3-103">ASP.NET Core Blazor テンプレート</span><span class="sxs-lookup"><span data-stu-id="69de3-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="69de3-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="69de3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="69de3-105">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="69de3-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="69de3-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="69de3-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="69de3-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="69de3-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="69de3-108">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69de3-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="69de3-109">テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="69de3-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

<span data-ttu-id="69de3-110">`--help` オプションを [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI コマンドに渡すことで、テンプレート オプションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="69de3-110">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazor<span data-ttu-id="69de3-111"> プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="69de3-111"> project structure</span></span>

<span data-ttu-id="69de3-112">次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="69de3-112">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="69de3-113">`Program.cs`:以下を設定するアプリのエントリ ポイント。</span><span class="sxs-lookup"><span data-stu-id="69de3-113">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="69de3-114">ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="69de3-114">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="69de3-115">WebAssembly ホスト (Blazor WebAssembly):このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。</span><span class="sxs-lookup"><span data-stu-id="69de3-115">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="69de3-116">`App` コンポーネント (アプリのルート コンポーネント) は、`Add` メソッドの `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-116">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="69de3-117">サービスは、ホスト ビルダーの `ConfigureServices` メソッド (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="69de3-117">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="69de3-118">構成は、ホスト ビルダー (`builder.Configuration`) を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="69de3-118">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="69de3-119">`Startup.cs` (Blazor Server):アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="69de3-119">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="69de3-120">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="69de3-120">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="69de3-121">`ConfigureServices`:アプリの[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="69de3-121">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="69de3-122">Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-122">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="69de3-123">`Configure`:アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="69de3-123">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="69de3-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-124"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="69de3-125">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="69de3-125">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="69de3-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリ (`Pages/_Host.cshtml`) のルート ページを設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-126">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="69de3-127">`wwwroot/index.html` (Blazor WebAssembly):HTML ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="69de3-127">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="69de3-128">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-128">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="69de3-129">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="69de3-129">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="69de3-130">`App` コンポーネント (`App.razor`) は、`Startup.Configure` の `AddComponent` メソッドに `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-130">The `App` component (`App.razor`) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="69de3-131">`_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。</span><span class="sxs-lookup"><span data-stu-id="69de3-131">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="69de3-132">.NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。</span><span class="sxs-lookup"><span data-stu-id="69de3-132">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="69de3-133">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="69de3-133">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="69de3-134">`App.razor`:<xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="69de3-134">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="69de3-135"><xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="69de3-135">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="69de3-136">`Pages` フォルダー:Blazor アプリと Blazor サーバー アプリのルート Razor ページを構成するルーティング可能なコンポーネントまたはページ (`.razor`) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="69de3-136">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="69de3-137">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="69de3-137">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="69de3-138">テンプレートには以下が含まれています。</span><span class="sxs-lookup"><span data-stu-id="69de3-138">The template includes the following:</span></span>
  * <span data-ttu-id="69de3-139">`_Host.cshtml` (Blazor Server):Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="69de3-139">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="69de3-140">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-140">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="69de3-141">ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="69de3-141">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="69de3-142">[ホスト] ページは、ルート `App` コンポーネント (`App.razor`) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="69de3-142">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="69de3-143">`Counter` (`Pages/Counter.razor`):カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="69de3-143">`Counter` (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="69de3-144">`Error` (`Error.razor`、Blazor Server アプリのみ):アプリでハンドルされない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="69de3-144">`Error` (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="69de3-145">`FetchData` (`Pages/FetchData.razor`):フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="69de3-145">`FetchData` (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="69de3-146">`Index` (`Pages/Index.razor`):ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="69de3-146">`Index` (`Pages/Index.razor`): Implements the Home page.</span></span>

* <span data-ttu-id="69de3-147">`Shared` フォルダー:アプリで使用する他の UI コンポーネント (`.razor`) を含みます。</span><span class="sxs-lookup"><span data-stu-id="69de3-147">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="69de3-148">`MainLayout` (`MainLayout.razor`):アプリのレイアウト コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="69de3-148">`MainLayout` (`MainLayout.razor`): The app's layout component.</span></span>
  * <span data-ttu-id="69de3-149">`NavMenu` (`NavMenu.razor`):サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="69de3-149">`NavMenu` (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="69de3-150">ナビゲーション リンクを他の Razor コンポーネントに表示する [`NavLink` コンポーネント](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="69de3-150">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="69de3-151"><xref:Microsoft.AspNetCore.Components.Routing.NavLink> コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="69de3-151">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="69de3-152">`_Imports.razor`:名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント (`.razor`) に含める一般的な Razor ディレクティブが含まれます。</span><span class="sxs-lookup"><span data-stu-id="69de3-152">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="69de3-153">`Data` フォルダー (Blazor Server):アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装が含まれます。</span><span class="sxs-lookup"><span data-stu-id="69de3-153">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="69de3-154">`wwwroot`:アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="69de3-154">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="69de3-155">`appsettings.json` (Blazor Server):アプリの構成設定。</span><span class="sxs-lookup"><span data-stu-id="69de3-155">`appsettings.json` (Blazor Server): Configuration settings for the app.</span></span>
