---
title: ASP.NET Core Blazor テンプレート
author: guardrex
description: ASP.NET Core Blazor アプリ テンプレートと Blazor プロジェクトの構造について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 71a9d9eee8637dda0b3cecac82ff96a0c3bfedb5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320977"
---
# <a name="aspnet-core-opno-locblazor-templates"></a><span data-ttu-id="53cbc-103">ASP.NET Core Blazor テンプレート</span><span class="sxs-lookup"><span data-stu-id="53cbc-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="53cbc-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="53cbc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="53cbc-105">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対応するアプリを開発するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="53cbc-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* Blazor<span data-ttu-id="53cbc-106"> WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="53cbc-106"> WebAssembly (`blazorwasm`)</span></span>
* Blazor<span data-ttu-id="53cbc-107"> Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="53cbc-107"> Server (`blazorserver`)</span></span>

<span data-ttu-id="53cbc-108">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53cbc-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="53cbc-109">テンプレートから Blazor アプリを作成する手順の詳細については、「<xref:blazor/get-started>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="53cbc-109">For step-by-step instructions on creating a Blazor app from a template, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-project-structure"></a>Blazor<span data-ttu-id="53cbc-110"> プロジェクトの構造</span><span class="sxs-lookup"><span data-stu-id="53cbc-110"> project structure</span></span>

<span data-ttu-id="53cbc-111">次のファイルとフォルダーは、Blazor テンプレートから生成された Blazor アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-111">The following files and folders make up a Blazor app generated from a Blazor template:</span></span>

* <span data-ttu-id="53cbc-112">*Program.cs* &ndash; 以下を設定するアプリのエントリポイント。</span><span class="sxs-lookup"><span data-stu-id="53cbc-112">*Program.cs* &ndash; The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="53cbc-113">ASP.NET Core [ホスト](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="53cbc-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="53cbc-114">WebAssembly ホスト (Blazor WebAssembly) &ndash; このファイルのコードは、Blazor WebAssembly テンプレート (`blazorwasm`) から作成されたアプリに固有です。</span><span class="sxs-lookup"><span data-stu-id="53cbc-114">WebAssembly host (Blazor WebAssembly) &ndash; The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="53cbc-115">`App` コンポーネント (アプリのルート コンポーネント) は、`Add` メソッドの `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-115">The `App` component, which is the root component of the app, is specified as the `app` DOM element to the `Add` method.</span></span>
    * <span data-ttu-id="53cbc-116">サービスは、ホスト ビルダーの `ConfigureServices` メソッド (`builder.Services.AddSingleton<IMyDependency, MyDependency>();`など) を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-116">Services can be configured with the `ConfigureServices` method on the host builder (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>();`).</span></span>
    * <span data-ttu-id="53cbc-117">構成は、ホスト ビルダー (`builder.Configuration`) を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-117">Configuration can be supplied via the host builder (`builder.Configuration`).</span></span>

* <span data-ttu-id="53cbc-118">*Startup.cs* (Blazor Server) &ndash; アプリのスタートアップ ロジックを含みます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-118">*Startup.cs* (Blazor Server) &ndash; Contains the app's startup logic.</span></span> <span data-ttu-id="53cbc-119">`Startup` クラスには、次の 2 つのメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="53cbc-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="53cbc-120">`ConfigureServices` &ndash; アプリの [ 依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービスを構成します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-120">`ConfigureServices` &ndash; Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="53cbc-121">Blazor Server アプリでは、<xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*> を呼び出すことによってサービスが追加されます。`WeatherForecastService` は、サンプルの `FetchData` コンポーネントが使用するためにサービス コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor*>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="53cbc-122">`Configure` &ndash; アプリの要求処理パイプラインを構成します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-122">`Configure` &ndash; Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="53cbc-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> は、ブラウザーとのリアルタイム接続用のエンドポイントを設定するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="53cbc-124">この接続は [SignalR](xref:signalr/introduction) で作成されます。これは、アプリにリアルタイム Web 機能を追加するためのフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="53cbc-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="53cbc-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) は、アプリのルート ページ (*Pages/_Host.cshtml*) を設定し、ナビゲーションを有効にするために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-125">[MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (*Pages/_Host.cshtml*) and enable navigation.</span></span>

* <span data-ttu-id="53cbc-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; HTML ページとして実装されているアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="53cbc-126">*wwwroot/index.html* (Blazor WebAssembly) &ndash; The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="53cbc-127">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="53cbc-128">このページは、ルート `App` コンポーネントを表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="53cbc-129">`App` コンポーネント (*App.razor*) は、`Startup.Configure` の `AddComponent` メソッドに `app` DOM 要素として指定されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-129">The `App` component (*App.razor*) is specified as the `app` DOM element to the `AddComponent` method in `Startup.Configure`.</span></span>
  * <span data-ttu-id="53cbc-130">`_framework/blazor.webassembly.js` JavaScript ファイルが読み込まれます。これは以下のことを行います。</span><span class="sxs-lookup"><span data-stu-id="53cbc-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="53cbc-131">.NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。</span><span class="sxs-lookup"><span data-stu-id="53cbc-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="53cbc-132">アプリを実行するランタイムの初期化。</span><span class="sxs-lookup"><span data-stu-id="53cbc-132">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="53cbc-133">*App.razor* &ndash; <xref:Microsoft.AspNetCore.Components.Routing.Router> コンポーネントを使用してクライアント側のルーティングを設定するアプリのルート コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="53cbc-133">*App.razor* &ndash; The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="53cbc-134">`Router` コンポーネントは、ブラウザーのナビゲーションをインターセプトし、要求されたアドレスに一致するページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="53cbc-134">The `Router` component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="53cbc-135">*Pages* フォルダー &ndash; Blazor アプリと Blazor サーバー アプリのルート Razor ページを構成するルーティング可能なコンポーネント/ページ ( *.razor*) が含まれています。</span><span class="sxs-lookup"><span data-stu-id="53cbc-135">*Pages* folder &ndash; Contains the routable components/pages (*.razor*) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="53cbc-136">各ページのルートは、[`@page`](xref:mvc/views/razor#page) ディレクティブを使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-136">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="53cbc-137">テンプレートには以下が含まれています。</span><span class="sxs-lookup"><span data-stu-id="53cbc-137">The template includes the following:</span></span>
  * <span data-ttu-id="53cbc-138">*_Host.cshtml* (Blazor Server) &ndash; Razor ページとして実装されるアプリのルート ページ。</span><span class="sxs-lookup"><span data-stu-id="53cbc-138">*_Host.cshtml* (Blazor Server) &ndash; The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="53cbc-139">アプリのいずれかのページが最初に要求されると、このページが表示されて応答として返されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-139">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="53cbc-140">ブラウザーとサーバーの間のリアルタイム SignalR 接続を設定する `_framework/blazor.server.js` JavaScript ファイルが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-140">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="53cbc-141">このホスト ページは、ルート `App` コンポーネント (*App.razor*) を表示する場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-141">The Host page specifies where the root `App` component (*App.razor*) is rendered.</span></span>
  * <span data-ttu-id="53cbc-142">`Counter` (*Counter.razor*) &ndash; カウンター ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-142">`Counter` (*Counter.razor*) &ndash; Implements the Counter page.</span></span>
  * <span data-ttu-id="53cbc-143">`Error` (*Error.razor*、Blazor Server アプリのみ) &ndash; アプリで処理されない例外が発生したときに表示されます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-143">`Error` (*Error.razor*, Blazor Server app only) &ndash; Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="53cbc-144">`FetchData` (*FetchData.razor*) &ndash; フェッチ データ ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-144">`FetchData` (*FetchData.razor*) &ndash; Implements the Fetch data page.</span></span>
  * <span data-ttu-id="53cbc-145">`Index` (*Index.razor*) &ndash; ホーム ページを実装します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-145">`Index` (*Index.razor*) &ndash; Implements the Home page.</span></span>

* <span data-ttu-id="53cbc-146">*Shared* フォルダー &ndash; アプリで使用する他の UI コンポーネント ( *.razor*) を含みます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-146">*Shared* folder &ndash; Contains other UI components (*.razor*) used by the app:</span></span>
  * <span data-ttu-id="53cbc-147">`MainLayout` (*MainLayout.razor*) &ndash; アプリのレイアウト コンポーネント。</span><span class="sxs-lookup"><span data-stu-id="53cbc-147">`MainLayout` (*MainLayout.razor*) &ndash; The app's layout component.</span></span>
  * <span data-ttu-id="53cbc-148">`NavMenu` (*NavMenu.razor*) &ndash; サイドバー ナビゲーションを実装します。</span><span class="sxs-lookup"><span data-stu-id="53cbc-148">`NavMenu` (*NavMenu.razor*) &ndash; Implements sidebar navigation.</span></span> <span data-ttu-id="53cbc-149">ナビゲーション リンクを他の Razor コンポーネントに表示する [NavLink コンポーネント](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) を含みます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-149">Includes the [NavLink component](xref:blazor/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="53cbc-150">`NavLink` コンポーネントは、そのコンポーネントが読み込まれると、自動的に選択された状態を示します。これは、ユーザーが現在どのコンポーネントが表示されているかを理解するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-150">The `NavLink` component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>

* <span data-ttu-id="53cbc-151">*_Imports razor* &ndash; 名前空間の [`@using`](xref:mvc/views/razor#using) ディレクティブなど、アプリのコンポーネント ( *.razor*) に含める一般的な Razor ディレクティブを含みます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-151">*_Imports.razor* &ndash; Includes common Razor directives to include in the app's components (*.razor*), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="53cbc-152">*Data* フォルダー (Blazor Server) &ndash; には、アプリの `FetchData` コンポーネントに気象データの例を提供する、`WeatherForecast` クラスと `WeatherForecastService` の実装を含みます。</span><span class="sxs-lookup"><span data-stu-id="53cbc-152">*Data* folder (Blazor Server) &ndash; Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="53cbc-153">*wwwroot* &ndash; アプリのパブリックな静的アセットを含むアプリの [Web ルート](xref:fundamentals/index#web-root) フォルダー。</span><span class="sxs-lookup"><span data-stu-id="53cbc-153">*wwwroot* &ndash; The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="53cbc-154">*appsettings.json* (Blazor Server) &ndash; アプリの構成設定。</span><span class="sxs-lookup"><span data-stu-id="53cbc-154">*appsettings.json* (Blazor Server) &ndash; Configuration settings for the app.</span></span>
