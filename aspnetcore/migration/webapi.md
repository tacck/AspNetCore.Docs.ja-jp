---
<span data-ttu-id="97c9f-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="97c9f-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="97c9f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="97c9f-102">'Blazor'</span></span>
- <span data-ttu-id="97c9f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="97c9f-103">'Identity'</span></span>
- <span data-ttu-id="97c9f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="97c9f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="97c9f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="97c9f-105">'Razor'</span></span>
- <span data-ttu-id="97c9f-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="97c9f-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="97c9f-107">ASP.NET Web API から ASP.NET Core への移行</span><span class="sxs-lookup"><span data-stu-id="97c9f-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="97c9f-108">[Scott Addie](https://twitter.com/scott_addie)と[上田 Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="97c9f-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="97c9f-109">ASP.NET 4.x Web API は、ブラウザーやモバイルデバイスを含む広範なクライアントに到達する HTTP サービスです。</span><span class="sxs-lookup"><span data-stu-id="97c9f-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="97c9f-110">ASP.NET Core は、ASP.NET 4.x の MVC と Web API アプリのモデルを、ASP.NET Core MVC と呼ばれる単一のプログラミングモデルに統合します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="97c9f-111">この記事では、ASP.NET 4.x Web API から ASP.NET Core MVC に移行するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="97c9f-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="97c9f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="97c9f-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="97c9f-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="97c9f-114">ASP.NET 4.x Web API プロジェクトを確認する</span><span class="sxs-lookup"><span data-stu-id="97c9f-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="97c9f-115">この記事では、 [ASP.NET Web API 2 ではじめに](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)で作成された製品*アプリ*プロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="97c9f-116">そのプロジェクトでは、基本的な ASP.NET 4.x Web API プロジェクトは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="97c9f-117">*Global.asax.cs*では、次の呼び出しが行われ `WebApiConfig.Register` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="97c9f-118">`WebApiConfig`クラスは*App_Start*フォルダーにあり、静的メソッドがあり `Register` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="97c9f-119">前のクラスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97c9f-119">The preceding class:</span></span>

* <span data-ttu-id="97c9f-120">[属性ルーティング](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)を構成します。ただし、実際には使用されません。</span><span class="sxs-lookup"><span data-stu-id="97c9f-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="97c9f-121">ルーティングテーブルを構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-121">Configures the routing table.</span></span>
<span data-ttu-id="97c9f-122">このサンプルコードでは、Url が形式と一致する必要 `/api/{controller}/{id}` が `{id}` あり、省略可能です。</span><span class="sxs-lookup"><span data-stu-id="97c9f-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="97c9f-123">以下のセクションでは、ASP.NET Core MVC に Web API プロジェクトを移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="97c9f-124">変換先プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="97c9f-124">Create the destination project</span></span>

<span data-ttu-id="97c9f-125">Visual Studio で新しい空のソリューションを作成し、移行する ASP.NET 4.x Web API プロジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="97c9f-126">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="97c9f-127">空の**ソリューション**テンプレートを選択し、[**次へ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="97c9f-128">ソリューションに*WebAPIMigration*という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="97c9f-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-129">Select **Create**.</span></span>
1. <span data-ttu-id="97c9f-130">既存の製品*アプリ*プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="97c9f-131">移行先の新しい API プロジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="97c9f-132">新しい**ASP.NET Core Web アプリケーション**プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="97c9f-133">[**新しいプロジェクトの構成**] ダイアログで、プロジェクトに*Productscore*という名前を付け、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="97c9f-134">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="97c9f-135">**[API]** プロジェクト テンプレートを選択し、**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="97c9f-136">新しい*Productscore*プロジェクトから*WeatherForecast.cs*と*Controllers/WeatherForecastController*サンプルファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="97c9f-137">ソリューションに2つのプロジェクトが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="97c9f-137">The solution now contains two projects.</span></span> <span data-ttu-id="97c9f-138">次のセクションでは、 *Productscore*プロジェクトの内容を*productscore*プロジェクトに移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="97c9f-139">構成の移行</span><span class="sxs-lookup"><span data-stu-id="97c9f-139">Migrate configuration</span></span>

<span data-ttu-id="97c9f-140">ASP.NET Core では、 *App_Start*フォルダーや*global.asax*ファイルは使用しません。</span><span class="sxs-lookup"><span data-stu-id="97c9f-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="97c9f-141">また、web.config*ファイルは*発行時に追加されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="97c9f-142">`Startup` クラス:</span><span class="sxs-lookup"><span data-stu-id="97c9f-142">The `Startup` class:</span></span>

* <span data-ttu-id="97c9f-143">*Global.asax*を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="97c9f-144">すべてのアプリスタートアップタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="97c9f-145">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97c9f-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="97c9f-146">モデルとコントローラーの移行</span><span class="sxs-lookup"><span data-stu-id="97c9f-146">Migrate models and controllers</span></span>

<span data-ttu-id="97c9f-147">次のコードは、 `ProductsController` ASP.NET Core に対して更新されるを示しています。</span><span class="sxs-lookup"><span data-stu-id="97c9f-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="97c9f-148">`ProductsController`ASP.NET Core のを更新します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="97c9f-149">*コントローラー/製品コントローラー .cs*と*モデル*フォルダーを元のプロジェクトから新しいプロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="97c9f-150">コピーしたファイルのルート名前空間をに変更し `ProductsCore` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="97c9f-151">`using ProductsApp.Models;`ステートメントをに更新 `using ProductsCore.Models;` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="97c9f-152">ASP.NET Core には、次のコンポーネントは存在しません。</span><span class="sxs-lookup"><span data-stu-id="97c9f-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="97c9f-153">`ApiController` クラス</span><span class="sxs-lookup"><span data-stu-id="97c9f-153">`ApiController` class</span></span>
* <span data-ttu-id="97c9f-154">`System.Web.Http` 名前空間</span><span class="sxs-lookup"><span data-stu-id="97c9f-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="97c9f-155">`IHttpActionResult` インターフェイス</span><span class="sxs-lookup"><span data-stu-id="97c9f-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="97c9f-156">次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="97c9f-156">Make the following changes:</span></span>

1. <span data-ttu-id="97c9f-157">`ApiController` を <xref:Microsoft.AspNetCore.Mvc.ControllerBase> に変更します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="97c9f-158">を追加し `using Microsoft.AspNetCore.Mvc;` て `ControllerBase` 参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="97c9f-159">`using System.Web.Http;`を削除します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="97c9f-160">`GetProduct`アクションの戻り値の型をから `IHttpActionResult` に変更 `ActionResult<Product>` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="97c9f-161">`GetProduct`アクションの `return` ステートメントを次のように簡略化します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="97c9f-162">ルーティングを構成する</span><span class="sxs-lookup"><span data-stu-id="97c9f-162">Configure routing</span></span>

<span data-ttu-id="97c9f-163">ASP.NET Core *API*プロジェクトテンプレートには、生成されたコードにエンドポイントルーティング構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="97c9f-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="97c9f-164">次の <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> とを <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 呼び出します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="97c9f-165">[ミドルウェア](xref:fundamentals/middleware/index)パイプラインでルート照合とエンドポイント実行を登録します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="97c9f-166">製品*アプリ*プロジェクトの*App_Start/Webapiconfig.cs*ファイルを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="97c9f-167">次のようにルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="97c9f-168">次の `ProductsController` 属性を使用してクラスをマークします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="97c9f-169">上記の [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 属性は、コントローラーの属性ルーティングパターンを構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="97c9f-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性を使用すると、このコントローラーのすべてのアクションに対して属性のルーティングが必要になります。</span><span class="sxs-lookup"><span data-stu-id="97c9f-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="97c9f-171">属性ルーティングでは、やなどのトークンがサポートさ `[controller]` `[action]` れます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="97c9f-172">実行時には、各トークンは、属性が適用されたコントローラーまたはアクションの名前にそれぞれ置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="97c9f-173">トークン:</span><span class="sxs-lookup"><span data-stu-id="97c9f-173">The tokens:</span></span>
    * <span data-ttu-id="97c9f-174">プロジェクト内のマジック文字列の数を減らします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="97c9f-175">自動名前変更リファクタリングが適用されている場合は、対応するコントローラーおよびアクションとの同期が維持されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="97c9f-176">アクションに対する HTTP Get 要求を有効にし `ProductController` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="97c9f-177">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)アクションに属性を適用し `GetAllProducts` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="97c9f-178">`[HttpGet("{id}")]`アクションに属性を適用し `GetProduct` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="97c9f-179">移行したプロジェクトを実行し、に移動し `/api/products` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="97c9f-180">3つの製品の完全な一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-180">A full list of three products appears.</span></span> <span data-ttu-id="97c9f-181">`/api/products/1` を参照します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="97c9f-182">最初の製品が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97c9f-183">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="97c9f-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="97c9f-184">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="97c9f-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="97c9f-185">ASP.NET 4.x Web API プロジェクトを確認する</span><span class="sxs-lookup"><span data-stu-id="97c9f-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="97c9f-186">この記事では、 [ASP.NET Web API 2 ではじめに](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)で作成された製品*アプリ*プロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="97c9f-187">そのプロジェクトでは、基本的な ASP.NET 4.x Web API プロジェクトは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="97c9f-188">*Global.asax.cs*では、次の呼び出しが行われ `WebApiConfig.Register` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="97c9f-189">`WebApiConfig`クラスは*App_Start*フォルダーにあり、静的メソッドがあり `Register` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="97c9f-190">このクラスは、実際にはプロジェクトで使用されていませんが、[属性ルーティング](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)を構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="97c9f-191">また、ASP.NET Web API によって使用されるルーティングテーブルも構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="97c9f-192">この場合、ASP.NET 4.x Web API では、Url が形式と一致する必要 `/api/{controller}/{id}` が `{id}` あり、省略可能です。</span><span class="sxs-lookup"><span data-stu-id="97c9f-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="97c9f-193">以下のセクションでは、ASP.NET Core MVC に Web API プロジェクトを移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="97c9f-194">変換先プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="97c9f-194">Create the destination project</span></span>

<span data-ttu-id="97c9f-195">Visual Studio で次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="97c9f-196">[**ファイル**] [  >  **新しい**  >  **プロジェクト**] [  >  **その他のプロジェクト**] [  >  **Visual Studio ソリューション**] にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="97c9f-197">[**空のソリューション**] を選択し、ソリューションに*WebAPIMigration*という名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="97c9f-198">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-198">Click the **OK** button.</span></span>
* <span data-ttu-id="97c9f-199">既存の製品*アプリ*プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="97c9f-200">新しい**ASP.NET Core Web アプリケーション**プロジェクトをソリューションに追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="97c9f-201">ドロップダウンから **.Net Core**ターゲットフレームワークを選択し、[ **API**プロジェクト] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="97c9f-202">プロジェクトに*Productscore*という名前を付け、[ **OK** ] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="97c9f-203">ソリューションに2つのプロジェクトが含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="97c9f-203">The solution now contains two projects.</span></span> <span data-ttu-id="97c9f-204">次のセクションでは、 *Productscore*プロジェクトの内容を*productscore*プロジェクトに移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="97c9f-205">構成の移行</span><span class="sxs-lookup"><span data-stu-id="97c9f-205">Migrate configuration</span></span>

<span data-ttu-id="97c9f-206">ASP.NET Core では使用しません。</span><span class="sxs-lookup"><span data-stu-id="97c9f-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="97c9f-207">*App_Start*フォルダーまたは*global.asax*ファイル</span><span class="sxs-lookup"><span data-stu-id="97c9f-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="97c9f-208">*web.config ファイルは*発行時に追加されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="97c9f-209">`Startup` クラス:</span><span class="sxs-lookup"><span data-stu-id="97c9f-209">The `Startup` class:</span></span>

* <span data-ttu-id="97c9f-210">*Global.asax*を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="97c9f-211">すべてのアプリスタートアップタスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="97c9f-212">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="97c9f-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="97c9f-213">ASP.NET Core MVC では、がで呼び出されるときに、既定で属性ルーティングが組み込まれてい <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="97c9f-214">次の呼び出しを実行すると、製品 `UseMvc` *アプリ*プロジェクトの*App_Start*となります。</span><span class="sxs-lookup"><span data-stu-id="97c9f-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="97c9f-215">モデルとコントローラーの移行</span><span class="sxs-lookup"><span data-stu-id="97c9f-215">Migrate models and controllers</span></span>

<span data-ttu-id="97c9f-216">次のコードは、 `ProductsController` ASP.NET Core の更新プログラムを示しています。[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="97c9f-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="97c9f-217">`ProductsController`ASP.NET Core のを更新します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="97c9f-218">元のプロジェクトから新しいコントローラー */製品コントローラー .cs*をコピーします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="97c9f-219">[*モデル*] フォルダーを元のプロジェクトから新しいプロジェクトにコピーします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="97c9f-220">コピーしたファイルのルート名前空間をに変更し `ProductsCore` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="97c9f-221">`using ProductsApp.Models;`ステートメントをに更新 `using ProductsCore.Models;` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="97c9f-222">ASP.NET Core には、次のコンポーネントは存在しません。</span><span class="sxs-lookup"><span data-stu-id="97c9f-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="97c9f-223">`ApiController` クラス</span><span class="sxs-lookup"><span data-stu-id="97c9f-223">`ApiController` class</span></span>
* <span data-ttu-id="97c9f-224">`System.Web.Http` 名前空間</span><span class="sxs-lookup"><span data-stu-id="97c9f-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="97c9f-225">`IHttpActionResult` インターフェイス</span><span class="sxs-lookup"><span data-stu-id="97c9f-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="97c9f-226">次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="97c9f-226">Make the following changes:</span></span>

1. <span data-ttu-id="97c9f-227">`ApiController` を <xref:Microsoft.AspNetCore.Mvc.ControllerBase> に変更します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="97c9f-228">を追加し `using Microsoft.AspNetCore.Mvc;` て `ControllerBase` 参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="97c9f-229">`using System.Web.Http;`を削除します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="97c9f-230">`GetProduct`アクションの戻り値の型をから `IHttpActionResult` に変更 `ActionResult<Product>` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="97c9f-231">`GetProduct`アクションの `return` ステートメントを次のように簡略化します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="97c9f-232">ルーティングを構成する</span><span class="sxs-lookup"><span data-stu-id="97c9f-232">Configure routing</span></span>

<span data-ttu-id="97c9f-233">次のようにルーティングを構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="97c9f-234">次の `ProductsController` 属性を使用してクラスをマークします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="97c9f-235">上記の [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 属性は、コントローラーの属性ルーティングパターンを構成します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="97c9f-236">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)属性を使用すると、このコントローラーのすべてのアクションに対して属性のルーティングが必要になります。</span><span class="sxs-lookup"><span data-stu-id="97c9f-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="97c9f-237">属性ルーティングでは、やなどのトークンがサポートさ `[controller]` `[action]` れます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="97c9f-238">実行時には、各トークンは、属性が適用されたコントローラーまたはアクションの名前にそれぞれ置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="97c9f-239">トークンにより、プロジェクト内のマジック文字列の数が減少します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="97c9f-240">また、トークンは、自動名前変更リファクタリングが適用された場合に、対応するコントローラーとアクションとの同期を維持することも保証します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="97c9f-241">プロジェクトの互換モードを ASP.NET Core 2.2 に設定します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="97c9f-242">上記の変更:</span><span class="sxs-lookup"><span data-stu-id="97c9f-242">The preceding change:</span></span>

    * <span data-ttu-id="97c9f-243">は、 `[ApiController]` コントローラーレベルで属性を使用するために必要です。</span><span class="sxs-lookup"><span data-stu-id="97c9f-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="97c9f-244">ASP.NET Core 2.2 で導入された動作が中断する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="97c9f-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="97c9f-245">アクションに対する HTTP Get 要求を有効にし `ProductController` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="97c9f-246">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)アクションに属性を適用し `GetAllProducts` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="97c9f-247">`[HttpGet("{id}")]`アクションに属性を適用し `GetProduct` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="97c9f-248">移行したプロジェクトを実行し、に移動し `/api/products` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="97c9f-249">3つの製品の完全な一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-249">A full list of three products appears.</span></span> <span data-ttu-id="97c9f-250">`/api/products/1` を参照します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="97c9f-251">最初の製品が表示されます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="97c9f-252">互換性 shim</span><span class="sxs-lookup"><span data-stu-id="97c9f-252">Compatibility shim</span></span>

<span data-ttu-id="97c9f-253">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)ライブラリは、ASP.NET 4.X Web API プロジェクトを ASP.NET Core に移動する互換性 shim を提供します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="97c9f-254">互換性 shim は、ASP.NET 4.x Web API 2 のさまざまな規則をサポートするために ASP.NET Core を拡張します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="97c9f-255">このドキュメントで以前に移植したサンプルは、互換性 shim が不要であるという基本的なものです。</span><span class="sxs-lookup"><span data-stu-id="97c9f-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="97c9f-256">大規模なプロジェクトでは、互換性 shim を使用すると、ASP.NET Core と ASP.NET 4.x Web API 2 間の API ギャップを一時的にブリッジするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="97c9f-257">Web API 互換性 shim は、大規模な ASP.NET 4.x Web API プロジェクトの ASP.NET Core への移行をサポートするための一時的な手段として使用することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="97c9f-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="97c9f-258">時間の経過と共に、互換性 shim に依存するのではなく、ASP.NET Core パターンを使用するようにプロジェクトを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="97c9f-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="97c9f-259">に含まれる互換性機能 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="97c9f-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="97c9f-260">`ApiController`コントローラーの基本型を更新する必要がないように、型を追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="97c9f-261">Web API スタイルモデルのバインドを有効にします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="97c9f-262">MVC モデルバインドは、既定では ASP.NET 4.x 5 と同様に機能します。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="97c9f-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="97c9f-263">互換性 shim は、モデルバインディングを ASP.NET 4.x Web API 2 モデルバインディング規則に似たものに変更します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="97c9f-264">たとえば、複合型は要求本文から自動的にバインドされます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="97c9f-265">コントローラーアクションが型のパラメーターを受け取ることができるように、モデルバインディングを拡張 `HttpRequestMessage` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="97c9f-266">アクションが型の結果を返すことを可能にするメッセージフォーマッタを追加し `HttpResponseMessage` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="97c9f-267">Web API 2 のアクションが応答の処理に使用する可能性がある応答メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="97c9f-268">`HttpResponseMessage`機</span><span class="sxs-lookup"><span data-stu-id="97c9f-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="97c9f-269">アクションの結果メソッド:</span><span class="sxs-lookup"><span data-stu-id="97c9f-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="97c9f-270">のインスタンスを `IContentNegotiator` アプリの依存関係挿入 (DI) コンテナーに追加し、 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)のコンテンツネゴシエーションに関連する型を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="97c9f-271">このような型の例としては、やなどがあり `DefaultContentNegotiator` `MediaTypeFormatter` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="97c9f-272">互換性 shim を使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="97c9f-273">[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="97c9f-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="97c9f-274">でを呼び出して、互換性 shim のサービスをアプリの DI コンテナーに登録 `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` します。</span><span class="sxs-lookup"><span data-stu-id="97c9f-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="97c9f-275">アプリの呼び出しのでを使用して、web API 固有のルートを定義し `MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc` ます。</span><span class="sxs-lookup"><span data-stu-id="97c9f-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97c9f-276">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="97c9f-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
