---
<span data-ttu-id="b10da-101">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-101">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-103">'Blazor'</span></span>
- <span data-ttu-id="b10da-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-104">'Identity'</span></span>
- <span data-ttu-id="b10da-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-106">'Razor'</span></span>
- <span data-ttu-id="b10da-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-107">'SignalR' uid:</span></span> 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="b10da-108">ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する</span><span class="sxs-lookup"><span data-stu-id="b10da-108">Integrate ASP.NET Core Razor components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="b10da-109">作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b10da-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="b10da-110"> コンポーネントは、Razor Pages と MVC アプリに統合できます。</span><span class="sxs-lookup"><span data-stu-id="b10da-110"> components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="b10da-111">ページまたはビューがレンダリングされるときには、コンポーネントを同時に事前レンダリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="b10da-111">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="b10da-112">[アプリを準備](#prepare-the-app)した後に、アプリの要件に応じて、次のセクションのガイダンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b10da-112">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="b10da-113">ルーティング可能なコンポーネント:ユーザー要求から直接ルーティング可能なコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="b10da-113">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="b10da-114">訪問者が [`@page`](xref:mvc/views/razor#page) ディレクティブを含むコンポーネントのブラウザーで HTTP 要求を行うことができる必要がある場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b10da-114">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * <span data-ttu-id="b10da-115">[Razor Pages アプリでルーティング可能なコンポーネントを使用する](#use-routable-components-in-a-razor-pages-app)</span><span class="sxs-lookup"><span data-stu-id="b10da-115">[Use routable components in a Razor Pages app](#use-routable-components-in-a-razor-pages-app)</span></span>
  * [<span data-ttu-id="b10da-116">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="b10da-116">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="b10da-117">[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view):ユーザー要求から直接ルーティングできないコンポーネント。</span><span class="sxs-lookup"><span data-stu-id="b10da-117">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="b10da-118">アプリによって[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を含む既存のページやビューにコンポーネントが埋め込まれる場合は、このガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b10da-118">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="b10da-119">アプリの準備</span><span class="sxs-lookup"><span data-stu-id="b10da-119">Prepare the app</span></span>

<span data-ttu-id="b10da-120">既存の Razor Pages や MVC アプリでは、Razor コンポーネントをページとビューに統合できます。</span><span class="sxs-lookup"><span data-stu-id="b10da-120">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="b10da-121">アプリのレイアウト ファイル ( *_Layout.cshtml*) で次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="b10da-121">In the app's layout file (*_Layout.cshtml*):</span></span>

   * <span data-ttu-id="b10da-122">次の `<base>` タグを `<head>` 要素に追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-122">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="b10da-123">前の例の `href` 値 (*アプリ ベースのパス*) は、アプリがルート URL パス (`/`) に置かれていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="b10da-123">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="b10da-124">アプリがサブアプリケーションになっている場合は、記事 <xref:host-and-deploy/blazor/index#app-base-path> の「*アプリのベース パス*」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b10da-124">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:host-and-deploy/blazor/index#app-base-path> article.</span></span>

     <span data-ttu-id="b10da-125">*_Layout.cshtml* ファイルは、Razor Pages アプリの *Pages/Shared* フォルダーまたは MVC アプリの *Views/Shared* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="b10da-125">The *_Layout.cshtml* file is located in the *Pages/Shared* folder in a Razor Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="b10da-126">*blazor.server.js* スクリプトの `<script>` タグを、終了 `</body>` タグの直前に追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-126">Add a `<script>` tag for the *blazor.server.js* script immediately before of the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="b10da-127">フレームワークによって *blazor.server.js* スクリプトがアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-127">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="b10da-128">手動でアプリにスクリプトを追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="b10da-128">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="b10da-129">次の内容を含む *_Imports.razor* ファイルをプロジェクトのルート フォルダーに追加します (最後の名前空間 `MyAppNamespace` をアプリの名前空間に変更します)。</span><span class="sxs-lookup"><span data-stu-id="b10da-129">Add an *_Imports.razor* file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="b10da-130">`Startup.ConfigureServices` で、Blazor Server サービスを登録します。</span><span class="sxs-lookup"><span data-stu-id="b10da-130">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="b10da-131">`Startup.Configure` で、Blazor Hub エンドポイントを `app.UseEndpoints` に追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-131">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="b10da-132">コンポーネントを任意のページまたはビューに統合します。</span><span class="sxs-lookup"><span data-stu-id="b10da-132">Integrate components into any page or view.</span></span> <span data-ttu-id="b10da-133">詳細については、「[ページまたはビューからコンポーネントをレンダリングする](#render-components-from-a-page-or-view)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-133">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="b10da-134">Razor Pages アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="b10da-134">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="b10da-135">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="b10da-135">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b10da-136">Razor Pages アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b10da-136">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="b10da-137">「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b10da-137">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="b10da-138">次の内容の *App.razor* ファイルをプロジェクト ルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-138">Add an *App.razor* file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="b10da-139">次の内容の *_Host.cshtml* ファイルを *Pages* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-139">Add a *_Host.cshtml* file to the *Pages* folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b10da-140">コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="b10da-140">Components use the shared *_Layout.cshtml* file for their layout.</span></span>

   <span data-ttu-id="b10da-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="b10da-141"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b10da-142">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="b10da-142">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b10da-143">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="b10da-143">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="b10da-144">表示モード</span><span class="sxs-lookup"><span data-stu-id="b10da-144">Render Mode</span></span> | <span data-ttu-id="b10da-145">説明</span><span class="sxs-lookup"><span data-stu-id="b10da-145">Description</span></span> |
   | ---
<span data-ttu-id="b10da-146">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-146">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-148">'Blazor'</span></span>
- <span data-ttu-id="b10da-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-149">'Identity'</span></span>
- <span data-ttu-id="b10da-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-151">'Razor'</span></span>
- <span data-ttu-id="b10da-152">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-153">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-153">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-155">'Blazor'</span></span>
- <span data-ttu-id="b10da-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-156">'Identity'</span></span>
- <span data-ttu-id="b10da-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-158">'Razor'</span></span>
- <span data-ttu-id="b10da-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-160">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-160">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-162">'Blazor'</span></span>
- <span data-ttu-id="b10da-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-163">'Identity'</span></span>
- <span data-ttu-id="b10da-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-165">'Razor'</span></span>
- <span data-ttu-id="b10da-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-166">'SignalR' uid:</span></span> 

<span data-ttu-id="b10da-167">------ | --- title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-167">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-169">'Blazor'</span></span>
- <span data-ttu-id="b10da-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-170">'Identity'</span></span>
- <span data-ttu-id="b10da-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-172">'Razor'</span></span>
- <span data-ttu-id="b10da-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-174">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-174">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-176">'Blazor'</span></span>
- <span data-ttu-id="b10da-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-177">'Identity'</span></span>
- <span data-ttu-id="b10da-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-179">'Razor'</span></span>
- <span data-ttu-id="b10da-180">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-181">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-181">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-183">'Blazor'</span></span>
- <span data-ttu-id="b10da-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-184">'Identity'</span></span>
- <span data-ttu-id="b10da-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-186">'Razor'</span></span>
- <span data-ttu-id="b10da-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-187">'SignalR' uid:</span></span> 

<span data-ttu-id="b10da-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` コンポーネントを静的 HTML にレンダリングし、Blazor サーバー アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="b10da-188">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b10da-189">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-189">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b10da-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor サーバー アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b10da-190">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b10da-191">`App` コンポーネントからの出力は含まれません。</span><span class="sxs-lookup"><span data-stu-id="b10da-191">Output from the `App` component isn't included.</span></span> <span data-ttu-id="b10da-192">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-192">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b10da-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b10da-193">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="b10da-194">コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-194">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b10da-195">*_Host.cshtml* ページの優先度が低いルートを、`Startup.Configure` 内のエンドポイント構成に追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-195">Add a low-priority route for the *_Host.cshtml* page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="b10da-196">ルーティング可能なコンポーネントをアプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-196">Add routable components to the app.</span></span> <span data-ttu-id="b10da-197">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b10da-197">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b10da-198">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-198">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="b10da-199">MVC アプリでルーティング可能なコンポーネントを使用する</span><span class="sxs-lookup"><span data-stu-id="b10da-199">Use routable components in an MVC app</span></span>

<span data-ttu-id="b10da-200">*ここは、ユーザー要求から直接ルーティング可能なコンポーネントを追加することに関係のあるセクションです。*</span><span class="sxs-lookup"><span data-stu-id="b10da-200">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="b10da-201">MVC アプリでルーティング可能な Razor コンポーネントをサポートするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b10da-201">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="b10da-202">「[アプリを準備する](#prepare-the-app)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="b10da-202">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="b10da-203">次の内容の *App.razor* ファイルを、プロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-203">Add an *App.razor* file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="b10da-204">次の内容の *_Host.cshtml* ファイルを *Views/Home* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-204">Add a *_Host.cshtml* file to the *Views/Home* folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="b10da-205">コンポーネントは、そのレイアウトで共有される *_Layout.cshtml* ファイルを使用します。</span><span class="sxs-lookup"><span data-stu-id="b10da-205">Components use the shared *_Layout.cshtml* file for their layout.</span></span>
   
   <span data-ttu-id="b10da-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> によって、`App` コンポーネントに対して以下の構成が行われます。</span><span class="sxs-lookup"><span data-stu-id="b10da-206"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="b10da-207">ページに事前レンダリングするかどうか。</span><span class="sxs-lookup"><span data-stu-id="b10da-207">Is prerendered into the page.</span></span>
   * <span data-ttu-id="b10da-208">ページに静的 HTML としてレンダリングするかどうか。または、ユーザー エージェントから Blazor アプリをブートストラップするために必要な情報が含まれているかどうか。</span><span class="sxs-lookup"><span data-stu-id="b10da-208">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   | <span data-ttu-id="b10da-209">表示モード</span><span class="sxs-lookup"><span data-stu-id="b10da-209">Render Mode</span></span> | <span data-ttu-id="b10da-210">説明</span><span class="sxs-lookup"><span data-stu-id="b10da-210">Description</span></span> |
   | ---
<span data-ttu-id="b10da-211">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-211">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-213">'Blazor'</span></span>
- <span data-ttu-id="b10da-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-214">'Identity'</span></span>
- <span data-ttu-id="b10da-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-216">'Razor'</span></span>
- <span data-ttu-id="b10da-217">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-218">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-218">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-220">'Blazor'</span></span>
- <span data-ttu-id="b10da-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-221">'Identity'</span></span>
- <span data-ttu-id="b10da-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-223">'Razor'</span></span>
- <span data-ttu-id="b10da-224">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-225">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-225">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-227">'Blazor'</span></span>
- <span data-ttu-id="b10da-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-228">'Identity'</span></span>
- <span data-ttu-id="b10da-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-230">'Razor'</span></span>
- <span data-ttu-id="b10da-231">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-231">'SignalR' uid:</span></span> 

<span data-ttu-id="b10da-232">------ | --- title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-232">------ | --- title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-234">'Blazor'</span></span>
- <span data-ttu-id="b10da-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-235">'Identity'</span></span>
- <span data-ttu-id="b10da-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-237">'Razor'</span></span>
- <span data-ttu-id="b10da-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-239">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-239">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-241">'Blazor'</span></span>
- <span data-ttu-id="b10da-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-242">'Identity'</span></span>
- <span data-ttu-id="b10da-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-244">'Razor'</span></span>
- <span data-ttu-id="b10da-245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b10da-246">title:'ASP.NET Core Razor コンポーネントを Razor Pages と MVC アプリに統合する' author: description:'Blazor アプリのコンポーネントと DOM 要素のデータ バインディングのシナリオについて説明します。'</span><span class="sxs-lookup"><span data-stu-id="b10da-246">title: 'Integrate ASP.NET Core Razor components into Razor Pages and MVC apps' author: description: 'Learn about data binding scenarios for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="b10da-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="b10da-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b10da-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b10da-248">'Blazor'</span></span>
- <span data-ttu-id="b10da-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b10da-249">'Identity'</span></span>
- <span data-ttu-id="b10da-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b10da-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="b10da-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b10da-251">'Razor'</span></span>
- <span data-ttu-id="b10da-252">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b10da-252">'SignalR' uid:</span></span> 

<span data-ttu-id="b10da-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` コンポーネントを静的 HTML にレンダリングし、Blazor サーバー アプリのマーカーを含めます。</span><span class="sxs-lookup"><span data-stu-id="b10da-253">------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renders the `App` component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b10da-254">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-254">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b10da-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor サーバー アプリのマーカーをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b10da-255">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b10da-256">`App` コンポーネントからの出力は含まれません。</span><span class="sxs-lookup"><span data-stu-id="b10da-256">Output from the `App` component isn't included.</span></span> <span data-ttu-id="b10da-257">このマーカーは、ユーザー エージェントの起動時に Blazor アプリをブートストラップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-257">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="b10da-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` コンポーネントを静的 HTML にレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b10da-258">| | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="b10da-259">コンポーネント タグ ヘルパーの詳細については、「ASP.NET コアのコンポーネント タグ ヘルパー<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-259">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="b10da-260">Home コントローラーにアクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-260">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="b10da-261">`Startup.Configure` 内のエンドポイント構成に *_Host.cshtml* ビューを返すコントローラー アクションのために、優先度が低いルートを追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-261">Add a low-priority route for the controller action that returns the *_Host.cshtml* view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="b10da-262">*Pages* フォルダーを作成し、アプリにルーティング可能なコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-262">Create a *Pages* folder and add routable components to the app.</span></span> <span data-ttu-id="b10da-263">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b10da-263">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="b10da-264">名前空間の詳細については、「[コンポーネントの名前空間](#component-namespaces)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-264">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="b10da-265">ページまたはビューからコンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="b10da-265">Render components from a page or view</span></span>

<span data-ttu-id="b10da-266">*これは、コンポーネントをユーザー要求から直接ルーティングできないページまたはビューにコンポーネントを追加することに関係するセクションです。*</span><span class="sxs-lookup"><span data-stu-id="b10da-266">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="b10da-267">ページまたはビューからコンポーネントをレンダリングするには、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)を使用します。</span><span class="sxs-lookup"><span data-stu-id="b10da-267">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="b10da-268">ステートフル対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="b10da-268">Render stateful interactive components</span></span>

<span data-ttu-id="b10da-269">Razor ページまたはビューには、ステートフル対話型コンポーネントを追加できます。</span><span class="sxs-lookup"><span data-stu-id="b10da-269">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="b10da-270">ページまたはビューがレンダリングされると、次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="b10da-270">When the page or view renders:</span></span>

* <span data-ttu-id="b10da-271">ページまたはビューと共にコンポーネントがプリレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b10da-271">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b10da-272">プリレンダリングに使用された初期のコンポーネント状態は失われます。</span><span class="sxs-lookup"><span data-stu-id="b10da-272">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b10da-273">SignalR 接続が確立されると、新しいコンポーネント状態が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b10da-273">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="b10da-274">次の Razor ページには、`Counter` コンポーネントがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b10da-274">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b10da-275">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-275">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="b10da-276">非対話型コンポーネントをレンダリングする</span><span class="sxs-lookup"><span data-stu-id="b10da-276">Render noninteractive components</span></span>

<span data-ttu-id="b10da-277">次の Razor ページには、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="b10da-277">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="b10da-278">コンポーネントは静的にレンダリングされるため、コンポーネントは対話型ではありません。</span><span class="sxs-lookup"><span data-stu-id="b10da-278">Since the component is statically rendered, the component isn't interactive:</span></span>

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

<span data-ttu-id="b10da-279">詳細については、「<xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-279">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="b10da-280">コンポーネントの名前空間</span><span class="sxs-lookup"><span data-stu-id="b10da-280">Component namespaces</span></span>

<span data-ttu-id="b10da-281">カスタム フォルダーを使用してアプリのコンポーネントを保持する場合は、フォルダーを表す名前空間を、ページまたはビューのいずれかに追加するか、 *_ViewImports.cshtml* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="b10da-281">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b10da-282">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b10da-282">In the following example:</span></span>

* <span data-ttu-id="b10da-283">`MyAppNamespace` をアプリの名前空間に変更します。</span><span class="sxs-lookup"><span data-stu-id="b10da-283">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="b10da-284">コンポーネントを保持するために *Components* という名前のフォルダーを使用していない場合は、`Components` を、コンポーネントが置かれているフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="b10da-284">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="b10da-285">*_ViewImports.cshtml* ファイルは、Razor Pages アプリの *Pages* フォルダーまたは MVC アプリの *Views* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="b10da-285">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="b10da-286">詳細については、「<xref:blazor/components#import-components>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b10da-286">For more information, see <xref:blazor/components#import-components>.</span></span>
