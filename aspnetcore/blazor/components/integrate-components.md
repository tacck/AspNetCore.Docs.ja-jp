---
title: 'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する'
author: guardrex
description: 'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056258"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="ea5f9-103">ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="ea5f9-103">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="ea5f9-104">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ea5f9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ea5f9-105">Razor コンポーネントは、Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-105">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="ea5f9-106">ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="ea5f9-107">[アプリを準備](#prepare-the-app)した後に、アプリの要件に応じて、次のセクションのガイダンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="ea5f9-108">ルーティング可能なコンポーネント:ユーザー要求から直接ルーティング可能なコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="ea5f9-109">訪問者が [`@page`](xref:mvc/views/razor#page) ディレクティブを含むコンポーネントのブラウザーで HTTP 要求を行うことができる必要がある場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="ea5f9-110">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="ea5f9-110">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="ea5f9-111">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="ea5f9-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="ea5f9-112">[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view):ユーザー要求から直接ルーティングできないコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="ea5f9-113">アプリによって[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を含む既存のページやビューにコンポーネントが埋め込まれる場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="ea5f9-114">アプリの準備</span><span class="sxs-lookup"><span data-stu-id="ea5f9-114">Prepare the app</span></span>

<span data-ttu-id="ea5f9-115">既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-115">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="ea5f9-116">アプリのレイアウト ファイル (`_Layout.cshtml`) で:</span><span class="sxs-lookup"><span data-stu-id="ea5f9-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="ea5f9-117">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="ea5f9-118">前の例の `href` 値 ( *アプリ ベースのパス* ) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-118">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="ea5f9-119">アプリがサブアプリケーションになっている場合は、記事 <xref:blazor/host-and-deploy/index#app-base-path> の「 *アプリのベース パス* 」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="ea5f9-120">`_Layout.cshtml` ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="ea5f9-121">*blazor.server.js* スクリプトの終了 `</body>` タグの直前に、`<script>` タグを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="ea5f9-122">フレームワークによって *blazor.server.js* スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="ea5f9-123">手動でアプリにスクリプトを追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="ea5f9-124">次の内容を含む `_Imports.razor` ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="ea5f9-125">`Startup.ConfigureServices` で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-125">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="ea5f9-126">`Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-126">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="ea5f9-127">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-127">Integrate components into any page or view.</span></span> <span data-ttu-id="ea5f9-128">詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="ea5f9-129">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="ea5f9-129">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="ea5f9-130">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="ea5f9-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ea5f9-131">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-131">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="ea5f9-132">「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="ea5f9-133">次の内容の `App.razor` ファイルをプロジェクト ルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-133">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="ea5f9-134">次の内容の `_Host.cshtml` ファイルを `Pages` フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ea5f9-135">コンポーネントでは、そのレイアウトで共有される `_Layout.cshtml` ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="ea5f9-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="ea5f9-137">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="ea5f9-138">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="ea5f9-139">表示モード</span><span class="sxs-lookup"><span data-stu-id="ea5f9-139">Render Mode</span></span> | <span data-ttu-id="ea5f9-140">説明</span><span class="sxs-lookup"><span data-stu-id="ea5f9-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ea5f9-141">`App` コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-141">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea5f9-142">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-142">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ea5f9-143">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-143">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea5f9-144">`App` コンポーネントからの出力は含まれません。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="ea5f9-145">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-145">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ea5f9-146">`App` コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="ea5f9-147">コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="ea5f9-148">`_Host.cshtml` ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="ea5f9-149">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-149">Add routable components to the app.</span></span> <span data-ttu-id="ea5f9-150">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="ea5f9-151">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="ea5f9-152">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="ea5f9-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="ea5f9-153">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="ea5f9-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="ea5f9-154">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-154">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="ea5f9-155">「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="ea5f9-156">次の内容の `App.razor` ファイルを、プロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. <span data-ttu-id="ea5f9-157">次の内容の `_Host.cshtml` ファイルを `Views/Home` フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="ea5f9-158">コンポーネントでは、そのレイアウトで共有される `_Layout.cshtml` ファイルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="ea5f9-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="ea5f9-160">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="ea5f9-161">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="ea5f9-162">表示モード</span><span class="sxs-lookup"><span data-stu-id="ea5f9-162">Render Mode</span></span> | <span data-ttu-id="ea5f9-163">説明</span><span class="sxs-lookup"><span data-stu-id="ea5f9-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="ea5f9-164">`App` コンポーネントを静的 HTML にレンダリングし、Blazor Server アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-164">Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea5f9-165">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="ea5f9-166">Blazor Server アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="ea5f9-167">`App` コンポーネントからの出力は含まれません。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="ea5f9-168">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="ea5f9-169">`App` コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="ea5f9-170">コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="ea5f9-171">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="ea5f9-172">`Startup.Configure` 内のエンドポイント構成に `_Host.cshtml` ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="ea5f9-173">`Pages` フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="ea5f9-174">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="ea5f9-175">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="ea5f9-176">ページまたはビューからコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="ea5f9-176">Render components from a page or view</span></span>

<span data-ttu-id="ea5f9-177">*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*</span><span class="sxs-lookup"><span data-stu-id="ea5f9-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="ea5f9-178">ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="ea5f9-179">ステートフル対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="ea5f9-179">Render stateful interactive components</span></span>

<span data-ttu-id="ea5f9-180">Razor ページまたはビューには、ステートフル対話型コンポーネントを追加できます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-180">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="ea5f9-181">ページまたはビューがレンダリングされると、次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-181">When the page or view renders:</span></span>

* <span data-ttu-id="ea5f9-182">ページまたはビューと共にコンポーネントがプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="ea5f9-183">プリレンダリングに使用された初期のコンポーネント状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="ea5f9-184">SignalR 接続が確立されると、新しいコンポーネント状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-184">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="ea5f9-185">次の Razor ページには、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-185">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="ea5f9-186">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="ea5f9-187">非対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="ea5f9-187">Render noninteractive components</span></span>

<span data-ttu-id="ea5f9-188">次の Razor ページには、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-188">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="ea5f9-189">コンポーネントは静的にレンダリングされるため、コンポーネントは対話型ではありません。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-189">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="ea5f9-190">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="ea5f9-191">コンポーネントの名前空間</span><span class="sxs-lookup"><span data-stu-id="ea5f9-191">Component namespaces</span></span>

<span data-ttu-id="ea5f9-192">カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、`_ViewImports.cshtml` ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="ea5f9-193">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-193">In the following example:</span></span>

* <span data-ttu-id="ea5f9-194">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="ea5f9-195">コンポーネントを保持するために *Components* という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="ea5f9-196">`_ViewImports.cshtml` ファイルは、Razor Pages アプリの `Pages` フォルダーまたは MVC アプリの `Views` フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="ea5f9-197">詳細については、「<xref:blazor/components/index#namespaces>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ea5f9-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
