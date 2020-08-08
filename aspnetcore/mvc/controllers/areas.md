---
title: ASP.NET Core の区分
author: rick-anderson
description: 区分は ASP.NET MVC の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用する方法を説明します。
ms.author: riande
ms.date: 03/21/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: af765eebfa8bfd147bd3b721508b5794d15d64a7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018443"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="9db54-103">ASP.NET Core の区分</span><span class="sxs-lookup"><span data-stu-id="9db54-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="9db54-104">作成者: [Dhananjay Kumar](https://twitter.com/debug_mode) および [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9db54-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9db54-105">区分は、関連する機能を別のグループとしてグループにまとめるために使用される ASP.NET の機能です。</span><span class="sxs-lookup"><span data-stu-id="9db54-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="9db54-106">ルーティング用の名前空間。</span><span class="sxs-lookup"><span data-stu-id="9db54-106">Namespace for routing.</span></span>
* <span data-ttu-id="9db54-107">ビューおよびページのフォルダー構造 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9db54-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="9db54-108">区分を使用すると、別のルートパラメーターである、、またはをページに追加することで、ルーティングのための階層が作成さ `area` `controller` `action` Razor `page` れます。</span><span class="sxs-lookup"><span data-stu-id="9db54-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="9db54-109">区分を使用すると、ASP.NET Core Web アプリをより小さな機能グループに分割し、それぞれに独自の Razor ページ、コントローラー、ビュー、およびモデルのセットを持たせることができます。</span><span class="sxs-lookup"><span data-stu-id="9db54-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="9db54-110">区分は、実質的にはアプリ内の構造体となります。</span><span class="sxs-lookup"><span data-stu-id="9db54-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="9db54-111">ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="9db54-112">ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="9db54-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="9db54-113">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="9db54-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="9db54-114">チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="9db54-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="9db54-115">これらの各ユニットには、ビュー、コントローラー、ページ、およびモデルを格納するための独自の領域があり Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="9db54-116">次のような場合は、プロジェクトで区分を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="9db54-117">論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。</span><span class="sxs-lookup"><span data-stu-id="9db54-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="9db54-118">各機能区分を個別に使用できるようにアプリを分割したい。</span><span class="sxs-lookup"><span data-stu-id="9db54-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="9db54-119">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9db54-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="9db54-120">ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。</span><span class="sxs-lookup"><span data-stu-id="9db54-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="9db54-121">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページがある領域](#areas-with-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="9db54-122">ビューを伴うコントローラーの区分</span><span class="sxs-lookup"><span data-stu-id="9db54-122">Areas for controllers with views</span></span>

<span data-ttu-id="9db54-123">区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:</span><span class="sxs-lookup"><span data-stu-id="9db54-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="9db54-124">[区分フォルダーの構造](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="9db54-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="9db54-125">コントローラーとその [`[Area]`](#attribute) 領域を関連付ける属性を持つコントローラー:</span><span class="sxs-lookup"><span data-stu-id="9db54-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="9db54-126">[開始するために追加された区分ルート](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="9db54-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="9db54-127">区分フォルダーの構造</span><span class="sxs-lookup"><span data-stu-id="9db54-127">Area folder structure</span></span>

<span data-ttu-id="9db54-128">あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。</span><span class="sxs-lookup"><span data-stu-id="9db54-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="9db54-129">区分を利用すると、フォルダーの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9db54-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="9db54-130">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="9db54-130">Project name</span></span>
  * <span data-ttu-id="9db54-131">Areas</span><span class="sxs-lookup"><span data-stu-id="9db54-131">Areas</span></span>
    * <span data-ttu-id="9db54-132">製品</span><span class="sxs-lookup"><span data-stu-id="9db54-132">Products</span></span>
      * <span data-ttu-id="9db54-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="9db54-133">Controllers</span></span>
        * <span data-ttu-id="9db54-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-134">HomeController.cs</span></span>
        * <span data-ttu-id="9db54-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-135">ManageController.cs</span></span>
      * <span data-ttu-id="9db54-136">ビュー</span><span class="sxs-lookup"><span data-stu-id="9db54-136">Views</span></span>
        * <span data-ttu-id="9db54-137">Home</span><span class="sxs-lookup"><span data-stu-id="9db54-137">Home</span></span>
          * <span data-ttu-id="9db54-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-138">Index.cshtml</span></span>
        * <span data-ttu-id="9db54-139">管理する</span><span class="sxs-lookup"><span data-stu-id="9db54-139">Manage</span></span>
          * <span data-ttu-id="9db54-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-140">Index.cshtml</span></span>
          * <span data-ttu-id="9db54-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-141">About.cshtml</span></span>
    * <span data-ttu-id="9db54-142">サービス</span><span class="sxs-lookup"><span data-stu-id="9db54-142">Services</span></span>
      * <span data-ttu-id="9db54-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="9db54-143">Controllers</span></span>
        * <span data-ttu-id="9db54-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-144">HomeController.cs</span></span>
      * <span data-ttu-id="9db54-145">ビュー</span><span class="sxs-lookup"><span data-stu-id="9db54-145">Views</span></span>
        * <span data-ttu-id="9db54-146">Home</span><span class="sxs-lookup"><span data-stu-id="9db54-146">Home</span></span>
          * <span data-ttu-id="9db54-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-147">Index.cshtml</span></span>

<span data-ttu-id="9db54-148">区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。</span><span class="sxs-lookup"><span data-stu-id="9db54-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="9db54-149">ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="9db54-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="9db54-150">コントローラーを区分に関連付ける</span><span class="sxs-lookup"><span data-stu-id="9db54-150">Associate the controller with an Area</span></span>

<span data-ttu-id="9db54-151">区分コントローラーは、 [ &lbrack; 区分 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="9db54-152">区分ルートを追加する</span><span class="sxs-lookup"><span data-stu-id="9db54-152">Add Area route</span></span>

<span data-ttu-id="9db54-153">通常、区分ルートは、[属性ルーティング](xref:mvc/controllers/routing#ar)ではなく、通常の[ルーティング](xref:mvc/controllers/routing#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="9db54-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="9db54-154">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9db54-155">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9db54-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="9db54-156">URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9db54-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="9db54-157">上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="9db54-158">使用 `{area:...}` `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="9db54-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="9db54-159">は、エリアにルーティングを追加するための最も複雑なメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="9db54-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="9db54-160">すべてのコントローラーを属性と照合 `[Area("Area name")]` します。</span><span class="sxs-lookup"><span data-stu-id="9db54-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="9db54-161">次のコードでは、<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="9db54-162">詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="9db54-163">MVC 区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="9db54-163">Link generation with MVC areas</span></span>

<span data-ttu-id="9db54-164">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="9db54-165">サンプルダウンロードには、次のものを含む[部分ビュー](xref:mvc/views/partial)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9db54-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="9db54-166">前のリンク。</span><span class="sxs-lookup"><span data-stu-id="9db54-166">The preceding links.</span></span>
* <span data-ttu-id="9db54-167">前に示した以外のリンク `area` は指定されていません。</span><span class="sxs-lookup"><span data-stu-id="9db54-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="9db54-168">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="9db54-169">区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="9db54-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="9db54-170">区分またはコントローラーが指定されていないとき、ルーティングは[アンビエント](xref:mvc/controllers/routing#ambient)値に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="9db54-171">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="9db54-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9db54-172">サンプルアプリの多くの場合、アンビエント値を使用すると、領域が指定されていないマークアップとの正しくないリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="9db54-173">詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="9db54-174">_ViewStart.cshtml ファイルを使用した区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="9db54-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="9db54-175">アプリ全体の共通レイアウトを共有するには、[アプリケーションのルートフォルダー](#arf)にある *_ViewStart*を保持します。</span><span class="sxs-lookup"><span data-stu-id="9db54-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="9db54-176">詳細については、<xref:mvc/views/layout> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="9db54-177">アプリケーションルートフォルダー</span><span class="sxs-lookup"><span data-stu-id="9db54-177">Application root folder</span></span>

<span data-ttu-id="9db54-178">アプリケーションルートフォルダーは、ASP.NET Core テンプレートを使用して作成された web アプリ内の*Startup.cs*を含むフォルダーです。</span><span class="sxs-lookup"><span data-stu-id="9db54-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="9db54-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="9db54-180">/ビュー */_ViewImports*、MVC の場合は、ページの場合は */ページ/_ViewImports*の場合 Razor 、領域内のビューにはインポートされません。</span><span class="sxs-lookup"><span data-stu-id="9db54-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="9db54-181">次のいずれかの方法を使用して、ビューをすべてのビューにインポートします。</span><span class="sxs-lookup"><span data-stu-id="9db54-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="9db54-182">[アプリケーションのルートフォルダー](#arf)に _ViewImports を追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="9db54-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="9db54-183">アプリケーションルートフォルダー内の *_ViewImports*は、アプリ内のすべてのビューに適用されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="9db54-184">[区分] の下にある適切な表示フォルダーに _ViewImports ファイルをコピーし*ます*。</span><span class="sxs-lookup"><span data-stu-id="9db54-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="9db54-185">通常、 *_ViewImports*のファイルには、、、およびステートメントをインポートする[タグヘルパー](xref:mvc/views/tag-helpers/intro)が含まれています `@using` `@inject` 。</span><span class="sxs-lookup"><span data-stu-id="9db54-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="9db54-186">詳細については、「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="9db54-187">ビューが保存されている既定の区分フォルダーを変更する</span><span class="sxs-lookup"><span data-stu-id="9db54-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="9db54-188">次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="9db54-189">ページがある領域 Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-189">Areas with Razor Pages</span></span>

<span data-ttu-id="9db54-190">ページがある領域には、 Razor `Areas/<area name>/Pages` アプリのルートにフォルダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="9db54-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="9db54-191">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)では次のフォルダー構造が使われます。</span><span class="sxs-lookup"><span data-stu-id="9db54-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="9db54-192">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="9db54-192">Project name</span></span>
  * <span data-ttu-id="9db54-193">Areas</span><span class="sxs-lookup"><span data-stu-id="9db54-193">Areas</span></span>
    * <span data-ttu-id="9db54-194">製品</span><span class="sxs-lookup"><span data-stu-id="9db54-194">Products</span></span>
      * <span data-ttu-id="9db54-195">ページ</span><span class="sxs-lookup"><span data-stu-id="9db54-195">Pages</span></span>
        * <span data-ttu-id="9db54-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="9db54-196">_ViewImports</span></span>
        * <span data-ttu-id="9db54-197">詳細</span><span class="sxs-lookup"><span data-stu-id="9db54-197">About</span></span>
        * <span data-ttu-id="9db54-198">インデックス</span><span class="sxs-lookup"><span data-stu-id="9db54-198">Index</span></span>
    * <span data-ttu-id="9db54-199">サービス</span><span class="sxs-lookup"><span data-stu-id="9db54-199">Services</span></span>
      * <span data-ttu-id="9db54-200">ページ</span><span class="sxs-lookup"><span data-stu-id="9db54-200">Pages</span></span>
        * <span data-ttu-id="9db54-201">管理する</span><span class="sxs-lookup"><span data-stu-id="9db54-201">Manage</span></span>
          * <span data-ttu-id="9db54-202">詳細</span><span class="sxs-lookup"><span data-stu-id="9db54-202">About</span></span>
          * <span data-ttu-id="9db54-203">インデックス</span><span class="sxs-lookup"><span data-stu-id="9db54-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="9db54-204">ページと領域を含むリンク生成 Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="9db54-205">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。</span><span class="sxs-lookup"><span data-stu-id="9db54-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="9db54-206">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9db54-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="9db54-207">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="9db54-208">区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="9db54-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="9db54-209">区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="9db54-210">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="9db54-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9db54-211">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="9db54-212">たとえば、次のコードから生成されるリンクを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9db54-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="9db54-213">上のコードの場合:</span><span class="sxs-lookup"><span data-stu-id="9db54-213">For the preceding code:</span></span>

* <span data-ttu-id="9db54-214">`<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="9db54-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="9db54-215">たとえば、「`/Services/Manage/`」、「`/Services/Manage/Index`」、「`/Services/Manage/About`」のように指定します。</span><span class="sxs-lookup"><span data-stu-id="9db54-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="9db54-216">`<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="9db54-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="9db54-217">コードは、[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)からのものです。</span><span class="sxs-lookup"><span data-stu-id="9db54-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="9db54-218">_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする</span><span class="sxs-lookup"><span data-stu-id="9db54-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="9db54-219">各区分*ページ*フォルダーに *_ViewImports*ファイルを追加して、名前空間とタグヘルパーを Razor フォルダー内の各ページにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="9db54-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="9db54-220">サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="9db54-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9db54-221">次のマークアップは、 */サービス/情報*ページを示してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="9db54-222">上のマークアップについて:</span><span class="sxs-lookup"><span data-stu-id="9db54-222">In the preceding markup:</span></span>

* <span data-ttu-id="9db54-223">完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="9db54-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="9db54-224">[タグヘルパー](xref:mvc/views/tag-helpers/intro)は、によって有効になります。`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="9db54-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="9db54-225">サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9db54-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="9db54-226">次のマークアップは、 */製品/バージョン*ページを示してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="9db54-227">上のファイルでは、*Areas/Products/Pages/_ViewImports.cshtml* ファイルによって、名前空間と `@addTagHelper` ディレクティブがファイルにインポートされています。</span><span class="sxs-lookup"><span data-stu-id="9db54-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="9db54-228">詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9db54-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="9db54-229">ページ領域の共有レイアウト Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="9db54-230">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="9db54-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="9db54-231">区分の発行</span><span class="sxs-lookup"><span data-stu-id="9db54-231">Publishing Areas</span></span>

<span data-ttu-id="9db54-232">\*.csproj ファイルに `<Project Sdk="Microsoft.NET.Sdk.Web">` が含まれているときは、すべての \*.cshtml ファイル、および *wwwroot* ディレクトリ内のファイルが出力に発行されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9db54-233">区分は ASP.NET の機能であり、関連する機能を別の名前空間 (ルーティングの場合) およびフォルダー構造 (ビューの場合) としてグループにまとめるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="9db54-234">区分を使用すると、別のルートパラメーターである、、またはをページに追加することで、ルーティングのための階層が作成さ `area` `controller` `action` Razor `page` れます。</span><span class="sxs-lookup"><span data-stu-id="9db54-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="9db54-235">区分を使用すると、ASP.NET Core Web アプリをより小さな機能グループに分割し、それぞれに独自の Razor ページ、コントローラー、ビュー、およびモデルのセットを持たせることができます。</span><span class="sxs-lookup"><span data-stu-id="9db54-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="9db54-236">区分は、実質的にはアプリ内の構造体となります。</span><span class="sxs-lookup"><span data-stu-id="9db54-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="9db54-237">ASP.NET Core Web プロジェクトでは、ページ、モデル、コントローラー、ビューなどの論理コンポーネントが別々のフォルダーに保存されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="9db54-238">ASP.NET Core ランタイムでは、名前付け規則を使用し、これらのコンポーネント間のリレーションシップを作成します。</span><span class="sxs-lookup"><span data-stu-id="9db54-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="9db54-239">大きなアプリでは、アプリを機能の個別の高レベル区分に分割すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="9db54-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="9db54-240">チェックアウト、請求、検索などの複数のビジネス ユニットがある eコマース アプリの場合です。</span><span class="sxs-lookup"><span data-stu-id="9db54-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="9db54-241">これらの各ユニットには、ビュー、コントローラー、ページ、およびモデルを格納するための独自の領域があり Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="9db54-242">次のような場合は、プロジェクトで区分を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="9db54-243">論理的に大まかに区切れる複数の機能コンポーネントでアプリが構成されている。</span><span class="sxs-lookup"><span data-stu-id="9db54-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="9db54-244">各機能区分を個別に使用できるようにアプリを分割したい。</span><span class="sxs-lookup"><span data-stu-id="9db54-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="9db54-245">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="9db54-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="9db54-246">ダウンロード サンプルからは、区分をテストするための基本的なアプリが与えられます。</span><span class="sxs-lookup"><span data-stu-id="9db54-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="9db54-247">ページを使用している場合は Razor 、このドキュメントの「 [ Razor ページがある領域](#areas-with-razor-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="9db54-248">ビューを伴うコントローラーの区分</span><span class="sxs-lookup"><span data-stu-id="9db54-248">Areas for controllers with views</span></span>

<span data-ttu-id="9db54-249">区分、コントローラー、ビューを使用する一般的な ASP.NET Core Web アプリに含まれる内容:</span><span class="sxs-lookup"><span data-stu-id="9db54-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="9db54-250">[区分フォルダーの構造](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="9db54-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="9db54-251">コントローラーとその [`[Area]`](#attribute) 領域を関連付ける属性を持つコントローラー:</span><span class="sxs-lookup"><span data-stu-id="9db54-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="9db54-252">[開始するために追加された区分ルート](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="9db54-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="9db54-253">区分フォルダーの構造</span><span class="sxs-lookup"><span data-stu-id="9db54-253">Area folder structure</span></span>

<span data-ttu-id="9db54-254">あるアプリに *Products* と *Services* という 2 つの論理グループが与えられているとします。</span><span class="sxs-lookup"><span data-stu-id="9db54-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="9db54-255">区分を利用すると、フォルダーの構造は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9db54-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="9db54-256">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="9db54-256">Project name</span></span>
  * <span data-ttu-id="9db54-257">Areas</span><span class="sxs-lookup"><span data-stu-id="9db54-257">Areas</span></span>
    * <span data-ttu-id="9db54-258">製品</span><span class="sxs-lookup"><span data-stu-id="9db54-258">Products</span></span>
      * <span data-ttu-id="9db54-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="9db54-259">Controllers</span></span>
        * <span data-ttu-id="9db54-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-260">HomeController.cs</span></span>
        * <span data-ttu-id="9db54-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-261">ManageController.cs</span></span>
      * <span data-ttu-id="9db54-262">ビュー</span><span class="sxs-lookup"><span data-stu-id="9db54-262">Views</span></span>
        * <span data-ttu-id="9db54-263">Home</span><span class="sxs-lookup"><span data-stu-id="9db54-263">Home</span></span>
          * <span data-ttu-id="9db54-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-264">Index.cshtml</span></span>
        * <span data-ttu-id="9db54-265">管理する</span><span class="sxs-lookup"><span data-stu-id="9db54-265">Manage</span></span>
          * <span data-ttu-id="9db54-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-266">Index.cshtml</span></span>
          * <span data-ttu-id="9db54-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-267">About.cshtml</span></span>
    * <span data-ttu-id="9db54-268">サービス</span><span class="sxs-lookup"><span data-stu-id="9db54-268">Services</span></span>
      * <span data-ttu-id="9db54-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="9db54-269">Controllers</span></span>
        * <span data-ttu-id="9db54-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="9db54-270">HomeController.cs</span></span>
      * <span data-ttu-id="9db54-271">ビュー</span><span class="sxs-lookup"><span data-stu-id="9db54-271">Views</span></span>
        * <span data-ttu-id="9db54-272">Home</span><span class="sxs-lookup"><span data-stu-id="9db54-272">Home</span></span>
          * <span data-ttu-id="9db54-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-273">Index.cshtml</span></span>

<span data-ttu-id="9db54-274">区分を使用するとき、前述のレイアウトが一般的ですが、このフォルダー構造を使用するにはビュー ファイルのみが求められます。</span><span class="sxs-lookup"><span data-stu-id="9db54-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="9db54-275">ビューの検出では、一致する区分ビュー ファイルを次の順序で検索します。</span><span class="sxs-lookup"><span data-stu-id="9db54-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="9db54-276">コントローラーを区分に関連付ける</span><span class="sxs-lookup"><span data-stu-id="9db54-276">Associate the controller with an Area</span></span>

<span data-ttu-id="9db54-277">区分コントローラーは、 [ &lbrack; 区分 &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="9db54-278">区分ルートを追加する</span><span class="sxs-lookup"><span data-stu-id="9db54-278">Add Area route</span></span>

<span data-ttu-id="9db54-279">区分ルートでは通常、属性ルーティングではなく、従来のルーティングが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="9db54-280">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="9db54-281">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9db54-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="9db54-282">URL スペースがすべての区分で統一されている場合、ルート テンプレートでトークンとして `{area:...}` を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9db54-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="9db54-283">上記のコードでは、ルートは 1 つの区分に一致しなければならないという制約が `exists` によって適用されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="9db54-284">`{area:...}` の使用は、ルーティングを区分に追加するメカニズムとして最も単純です。</span><span class="sxs-lookup"><span data-stu-id="9db54-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="9db54-285">次のコードでは、<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> を使用し、名前の付いた区分ルートが 2 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="9db54-286">ASP.NET Core 2.2 で `MapAreaRoute` を使用するときは、[この GitHub 問題](https://github.com/dotnet/AspNetCore/issues/7772)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="9db54-287">詳細については、[区分のルーティング](xref:mvc/controllers/routing#areas)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="9db54-288">MVC 区分を使ったリンクの生成</span><span class="sxs-lookup"><span data-stu-id="9db54-288">Link generation with MVC areas</span></span>

<span data-ttu-id="9db54-289">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)に含まれる次のコードでは、区分が指定された上でリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="9db54-290">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="9db54-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="9db54-291">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9db54-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="9db54-292">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="9db54-293">区分が指定されずに生成されたリンクは、同じ区分やコントローラーのページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="9db54-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="9db54-294">区分またはコントローラーが指定されていないとき、ルーティングは*アンビエント*値に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="9db54-295">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="9db54-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9db54-296">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="9db54-297">詳細については、「[コントローラー アクションへのルーティング](xref:mvc/controllers/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9db54-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="9db54-298">_ViewStart.cshtml ファイルを使用した区分の共有レイアウト</span><span class="sxs-lookup"><span data-stu-id="9db54-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="9db54-299">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="9db54-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="9db54-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="9db54-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="9db54-301">標準の場所では、*/Views/_ViewImports.cshtml* は区分に適用されません。</span><span class="sxs-lookup"><span data-stu-id="9db54-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="9db54-302">領域で共通の[タグヘルパー](xref:mvc/views/tag-helpers/intro)、、またはを使用するには、 `@using` `@inject` 適切な *_ViewImports cshtml*ファイルが[領域ビューに適用さ](xref:mvc/views/layout#importing-shared-directives)れていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9db54-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="9db54-303">すべてのビューで同じ動作が必要な場合は、*/Views/_ViewImports.cshtml* をアプリケーション ルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="9db54-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="9db54-304">ビューが保存されている既定の区分フォルダーを変更する</span><span class="sxs-lookup"><span data-stu-id="9db54-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="9db54-305">次のコードでは、既定の区分フォルダーが `"Areas"` から `"MyAreas"` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="9db54-306">ページがある領域 Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-306">Areas with Razor Pages</span></span>

<span data-ttu-id="9db54-307">ページがある領域には、 Razor `Areas/<area name>/Pages` アプリのルートにフォルダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="9db54-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="9db54-308">[サンプル アプリ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)では次のフォルダー構造が使われます。</span><span class="sxs-lookup"><span data-stu-id="9db54-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="9db54-309">プロジェクト名</span><span class="sxs-lookup"><span data-stu-id="9db54-309">Project name</span></span>
  * <span data-ttu-id="9db54-310">Areas</span><span class="sxs-lookup"><span data-stu-id="9db54-310">Areas</span></span>
    * <span data-ttu-id="9db54-311">製品</span><span class="sxs-lookup"><span data-stu-id="9db54-311">Products</span></span>
      * <span data-ttu-id="9db54-312">ページ</span><span class="sxs-lookup"><span data-stu-id="9db54-312">Pages</span></span>
        * <span data-ttu-id="9db54-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="9db54-313">_ViewImports</span></span>
        * <span data-ttu-id="9db54-314">詳細</span><span class="sxs-lookup"><span data-stu-id="9db54-314">About</span></span>
        * <span data-ttu-id="9db54-315">インデックス</span><span class="sxs-lookup"><span data-stu-id="9db54-315">Index</span></span>
    * <span data-ttu-id="9db54-316">サービス</span><span class="sxs-lookup"><span data-stu-id="9db54-316">Services</span></span>
      * <span data-ttu-id="9db54-317">ページ</span><span class="sxs-lookup"><span data-stu-id="9db54-317">Pages</span></span>
        * <span data-ttu-id="9db54-318">管理する</span><span class="sxs-lookup"><span data-stu-id="9db54-318">Manage</span></span>
          * <span data-ttu-id="9db54-319">詳細</span><span class="sxs-lookup"><span data-stu-id="9db54-319">About</span></span>
          * <span data-ttu-id="9db54-320">インデックス</span><span class="sxs-lookup"><span data-stu-id="9db54-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="9db54-321">ページと領域を含むリンク生成 Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="9db54-322">[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)の次のコードでは、区分を指定したリンクの生成を示しています (例: `asp-area="Products"`)。</span><span class="sxs-lookup"><span data-stu-id="9db54-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="9db54-323">上記のコードで生成されたリンクは、アプリ内のあらゆる場所で有効となります。</span><span class="sxs-lookup"><span data-stu-id="9db54-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="9db54-324">サンプル ダウンロードには、[部分ビュー](xref:mvc/views/partial)が含まれます。部分ビューには、上記のリンクと区分が指定されていない同じリンクが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9db54-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="9db54-325">部分ビューは[レイアウト ファイル](xref:mvc/views/layout)で参照されます。そのため、生成されたリンクがアプリのすべてのページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="9db54-326">区分が指定されずに生成されたリンクは、同じ区分のページから参照されるときにのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="9db54-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="9db54-327">区分が指定されていないとき、ルーティングは "*アンビエント*" 値に依存します。</span><span class="sxs-lookup"><span data-stu-id="9db54-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="9db54-328">現在の要求の現在のルート値は、リンク生成の場合、アンビエント値として見なされます。</span><span class="sxs-lookup"><span data-stu-id="9db54-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="9db54-329">サンプル アプリでは多くの場合、アンビエント値を使用すると、間違ったリンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="9db54-330">たとえば、次のコードから生成されるリンクを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9db54-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="9db54-331">上のコードの場合:</span><span class="sxs-lookup"><span data-stu-id="9db54-331">For the preceding code:</span></span>

* <span data-ttu-id="9db54-332">`<a asp-page="/Manage/About">` から生成されるリンクは、前回の要求が `Services` 区分内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="9db54-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="9db54-333">たとえば、「`/Services/Manage/`」、「`/Services/Manage/Index`」、「`/Services/Manage/About`」のように指定します。</span><span class="sxs-lookup"><span data-stu-id="9db54-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="9db54-334">`<a asp-page="/About">` から生成されるリンクは、前回の要求が `/Home` 内のページに向けられていた場合にのみ正しくなります。</span><span class="sxs-lookup"><span data-stu-id="9db54-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="9db54-335">コードは、[サンプル ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)からのものです。</span><span class="sxs-lookup"><span data-stu-id="9db54-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="9db54-336">_ViewImports ファイルを使って名前空間とタグ ヘルパーをインポートする</span><span class="sxs-lookup"><span data-stu-id="9db54-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="9db54-337">各区分*ページ*フォルダーに *_ViewImports*ファイルを追加して、名前空間とタグヘルパーを Razor フォルダー内の各ページにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="9db54-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="9db54-338">サンプル コードの *Services* 区分について検討します。これには *_ViewImports.cshtml* ファイルが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="9db54-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9db54-339">次のマークアップは、 */サービス/情報*ページを示してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="9db54-340">上のマークアップについて:</span><span class="sxs-lookup"><span data-stu-id="9db54-340">In the preceding markup:</span></span>

* <span data-ttu-id="9db54-341">完全修飾ドメイン名を使ってモデルを指定する必要があります (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="9db54-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="9db54-342">[タグヘルパー](xref:mvc/views/tag-helpers/intro)は、によって有効になります。`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="9db54-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="9db54-343">サンプル ダウンロードでは、Products 区分に次の *_ViewImports.cshtml* ファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="9db54-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="9db54-344">次のマークアップは、 */製品/バージョン*ページを示してい Razor ます。</span><span class="sxs-lookup"><span data-stu-id="9db54-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="9db54-345">上のファイルでは、*Areas/Products/Pages/_ViewImports.cshtml* ファイルによって、名前空間と `@addTagHelper` ディレクティブがファイルにインポートされています。</span><span class="sxs-lookup"><span data-stu-id="9db54-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="9db54-346">詳細については、「[タグ ヘルパーのスコープの管理](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)」と「[共有ディレクティブのインポート](xref:mvc/views/layout#importing-shared-directives)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9db54-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="9db54-347">ページ領域の共有レイアウト Razor</span><span class="sxs-lookup"><span data-stu-id="9db54-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="9db54-348">アプリ全体で共通レイアウトを共有するには、アプリケーションのルート フォルダーに *_ViewStart.cshtml* を移動します。</span><span class="sxs-lookup"><span data-stu-id="9db54-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="9db54-349">区分の発行</span><span class="sxs-lookup"><span data-stu-id="9db54-349">Publishing Areas</span></span>

<span data-ttu-id="9db54-350">\*.csproj ファイルに `<Project Sdk="Microsoft.NET.Sdk.Web">` が含まれているときは、すべての \*.cshtml ファイル、および *wwwroot* ディレクトリ内のファイルが出力に発行されます。</span><span class="sxs-lookup"><span data-stu-id="9db54-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
