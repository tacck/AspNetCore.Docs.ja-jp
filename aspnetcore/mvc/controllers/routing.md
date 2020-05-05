---
title: ASP.NET Core でのコントローラー アクションへのルーティング
author: rick-anderson
description: ASP.NET Core MVC でルーティング ミドルウェアを使って、受信した要求の URL を照合し、アクションにマップする方法について説明します。
ms.author: riande
ms.date: 3/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 4208ef8fb7a9b10621f214f79679ff8d7fd83996
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775025"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="8d9aa-103">ASP.NET Core でのコントローラー アクションへのルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="8d9aa-104">作成者: [Ryan Nowak](https://github.com/rynowak)、[Kirk Larkin](https://twitter.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d9aa-105">ASP.NET Core コントローラーは、ルーティング[ミドルウェア](xref:fundamentals/middleware/index)を使用して受信要求の url を照合し、[アクション](#action)にマップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="8d9aa-106">ルートテンプレート:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-106">Routes templates:</span></span>

* <span data-ttu-id="8d9aa-107">はスタートアップコードまたは属性で定義されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="8d9aa-108">URL パスと[アクション](#action)の照合方法を記述します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="8d9aa-109">リンクの Url を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="8d9aa-110">生成されたリンクは通常、応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="8d9aa-111">アクションは、[慣例的にルーティング](#cr)されるか、または[属性ルーティング](#ar)されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="8d9aa-112">コントローラーまたは[アクション](#action)にルートを配置すると、ルートが属性ルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="8d9aa-113">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="8d9aa-114">このドキュメント:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-114">This document:</span></span>

* <span data-ttu-id="8d9aa-115">MVC とルーティングの間の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="8d9aa-116">一般的な MVC アプリでルーティング機能を利用する方法。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="8d9aa-117">次の両方について説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-117">Covers both:</span></span>
    * <span data-ttu-id="8d9aa-118">通常、[従来のルーティング](#cr)では、コントローラーとビューが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="8d9aa-119">REST Api で使用される*属性ルーティング*。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="8d9aa-120">REST Api のルーティングに主に関心がある場合は、「 [Rest api の属性ルーティング](#ar)」セクションに進んでください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="8d9aa-121">詳細については、「[ルーティング](xref:fundamentals/routing)の詳細」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="8d9aa-122">ASP.NET Core 3.0 で追加された既定のルーティングシステムを指します (エンドポイントルーティングと呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="8d9aa-123">以前のバージョンのルーティングでは、互換性のためにコントローラーを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="8d9aa-124">手順については、 [2.2-3.0 移行ガイド](xref:migration/22-to-30)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="8d9aa-125">レガシルーティングシステムのリファレンス資料については、[このドキュメントの2.2 バージョン](xref:mvc/controllers/routing?view=aspnetcore-2.2)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="8d9aa-126">従来のルートの設定</span><span class="sxs-lookup"><span data-stu-id="8d9aa-126">Set up conventional route</span></span>

<span data-ttu-id="8d9aa-127">`Startup.Configure`通常、[従来のルーティング](#crd)を使用する場合、には次のようなコードがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="8d9aa-128">の呼び出しの内部<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>で<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>は、を使用して1つのルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="8d9aa-129">単一のルートの名前`default`は route です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-129">The single route is named `default` route.</span></span> <span data-ttu-id="8d9aa-130">コントローラーとビューを使用するほとんどのアプリでは、 `default`ルートと同様のルートテンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="8d9aa-131">REST Api では、[属性ルーティング](#ar)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="8d9aa-132">ルートテンプレート`"{controller=Home}/{action=Index}/{id?}"`:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="8d9aa-133">次のような URL パスと一致します`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="8d9aa-134">パスをトークン化し`{ controller = Products, action = Details, id = 5 }`て、ルートの値を抽出します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="8d9aa-135">アプリケーションにという名前`ProductsController`のコントローラーがあり、 `Details`アクションがある場合、ルート値を抽出した結果が一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="8d9aa-136">`/Products/Details/5`モデルは、の`id = 5`値をバインドし`id`て、 `5`パラメーターをに設定します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="8d9aa-137">詳細については、「[モデルバインド](xref:mvc/models/model-binding)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="8d9aa-138">`{controller=Home}`既定`Home`値`controller`としてを定義します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="8d9aa-139">`{action=Index}`既定`Index`値`action`としてを定義します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="8d9aa-140">の`?` `{id?}`文字は、 `id`省略可能として定義されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="8d9aa-141">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="8d9aa-142">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="8d9aa-143">URL パス`/`と一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="8d9aa-144">ルート値`{ controller = Home, action = Index }`を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="8d9aa-145">と`controller` `action`の値によって、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="8d9aa-146">`id`URL パスに対応するセグメントがないため、は値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="8d9aa-147">`/`とのアクションが存在する`HomeController`場合`Index`にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="8d9aa-148">前のコントローラー定義とルートテンプレートを使用し`HomeController.Index`て、次の URL パスに対してアクションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="8d9aa-149">URL パス`/`は、ルートテンプレートの既定`Home`のコントローラー `Index`とアクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="8d9aa-150">URL パス`/Home`は、ルートテンプレートの既定`Index`のアクションを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="8d9aa-151">便利なメソッド <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="8d9aa-152">もの</span><span class="sxs-lookup"><span data-stu-id="8d9aa-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="8d9aa-153">ルーティングは、 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*>と<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>ミドルウェアを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="8d9aa-154">コントローラーを使用するには:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-154">To use controllers:</span></span>
>
> * <span data-ttu-id="8d9aa-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> [属性ルーティング](#ar)コントローラーをマップするには、内で`UseEndpoints`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="8d9aa-156">また<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>は<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>を呼び出して、[従来のルーティング](#cr)コントローラーをマップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="8d9aa-157">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-157">Conventional routing</span></span>

<span data-ttu-id="8d9aa-158">従来のルーティングは、コントローラーとビューで使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="8d9aa-159">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="8d9aa-160">これは、"*規則ルーティング*" の例です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="8d9aa-161">これは、URL パスの*規則*を確立するため、*従来のルーティング*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="8d9aa-162">最初のパスセグメント`{controller=Home}`は、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="8d9aa-163">2番目の`{action=Index}`セグメントは、[アクション](#action)名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="8d9aa-164">3番目の`{id?}`セグメントは、オプション`id`として使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="8d9aa-165">の`?`は`{id?}`省略可能になります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="8d9aa-166">`id`は、モデルエンティティにマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="8d9aa-167">この`default`ルートを使用して、次の URL パスを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="8d9aa-168">`/Products/List``ProductsController.List`アクションに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="8d9aa-169">`/Blog/Article/17`はに`BlogController.Article`マップされ、通常`id` 、モデルはパラメーターを17にバインドします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="8d9aa-170">このマッピングは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-170">This mapping:</span></span>

* <span data-ttu-id="8d9aa-171">は、コントローラー名と[アクション](#action)名**のみ**に基づいています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="8d9aa-172">は、名前空間、ソースファイルの場所、またはメソッドのパラメーターに基づいていません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="8d9aa-173">既定のルートで従来のルーティングを使用すると、各アクションの新しい URL パターンを使用しなくてもアプリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="8d9aa-174">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)スタイルのアクションを使用するアプリの場合、コントローラー間で url の一貫性を確保します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="8d9aa-175">コードを簡略化するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-175">Helps simplify the code.</span></span>
* <span data-ttu-id="8d9aa-176">UI の予測可能性を向上させます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="8d9aa-177">前`id`のコードのは、ルートテンプレートによってオプションとして定義されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="8d9aa-178">アクションは、URL の一部として指定された省略可能な ID なしで実行できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="8d9aa-179">通常、URL`id`からを省略すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="8d9aa-180">`id`は、モデル`0`バインドによってに設定されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="8d9aa-181">データベース照合`id == 0`でエンティティが見つかりません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="8d9aa-182">[属性ルーティング](#ar)を使用すると、他のアクションではなく、一部のアクションに必要な ID を作成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="8d9aa-183">慣例により、ドキュメントには、正しい`id`使用状況で表示される可能性があるなどの省略可能なパラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="8d9aa-184">ほとんどのアプリでは、URL を読みやすくてわかりやすいものにするために、基本的なでわかりやすいルーティング スキームを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="8d9aa-185">既定の規則ルート `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="8d9aa-186">基本的でわかりやすいルーティング スキームをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="8d9aa-187">UI ベースのアプリの便利な開始点となります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="8d9aa-188">は、多くの web UI アプリに必要な唯一のルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="8d9aa-189">大規模な web UI アプリの場合は、必要な場合は、[領域](#areas)を使用する別のルートがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="8d9aa-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>および<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="8d9aa-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="8d9aa-191">呼び出された順序に基づいて、エンドポイントに**注文**値を自動的に割り当てます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="8d9aa-192">ASP.NET Core 3.0 以降でのエンドポイントのルーティング: </span><span class="sxs-lookup"><span data-stu-id="8d9aa-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="8d9aa-193">ルートの概念がありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="8d9aa-194">は、拡張性の実行に対する順序の保証を提供しません。すべてのエンドポイントが一度に処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="8d9aa-195">[ログ](xref:fundamentals/logging/index)を有効にすると、<xref:Microsoft.AspNetCore.Routing.Route> など、組み込みのルーティング実装で要求を照合するしくみを確認できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="8d9aa-196">[属性ルーティング](#ar)については、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="8d9aa-197">複数の従来のルート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-197">Multiple conventional routes</span></span>

<span data-ttu-id="8d9aa-198">との呼び出しをさら<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>に追加することで、複数の[従来のルート](#cr)を内部`UseEndpoints`に追加できます。 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*></span><span class="sxs-lookup"><span data-stu-id="8d9aa-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="8d9aa-199">これにより、複数の規則を定義したり、次のような特定の[アクション](#action)専用の通常のルートを追加したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="8d9aa-200">前`blog`のコードのルートは、**専用の従来のルート**です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="8d9aa-201">これは、次の理由で専用の従来のルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="8d9aa-202">従来の[ルーティング](#cr)を使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="8d9aa-203">これは特定の[アクション](#action)専用です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="8d9aa-204">と`controller` `action`はルートテンプレート`"blog/{*article}"`にパラメーターとして表示されないため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="8d9aa-205">既定値`{ controller = "Blog", action = "Article" }`のみを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="8d9aa-206">このルートは常にアクション`BlogController.Article`にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="8d9aa-207">`/Blog`、 `/Blog/Article`、および`/Blog/{any-string}`は、ブログルートに一致する唯一の URL パスです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="8d9aa-208">前の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-208">The preceding example:</span></span>

* <span data-ttu-id="8d9aa-209">`blog`ルートは最初に追加されるため、 `default`ルートより優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="8d9aa-210">とは、URL の一部としてアーティクル名を持つ一般的な[スラグ](https://developer.mozilla.org/docs/Glossary/Slug)スタイルのルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-210">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="8d9aa-211">ASP.NET Core 3.0 以降では、ルーティングは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="8d9aa-212">*ルート*と呼ばれる概念を定義します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-212">Define a concept called a *route*.</span></span> <span data-ttu-id="8d9aa-213">`UseRouting` では、ルートの照合がミドルウェア パイプラインに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="8d9aa-214">ミドルウェア`UseRouting`は、アプリで定義されているエンドポイントのセットを調べ、要求に基づいて最適なエンドポイント一致を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="8d9aa-215">や<xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>などの拡張機能の実行順序についての保証を提供します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="8d9aa-216">ルーティングのリファレンス資料については、「[ルーティング](xref:fundamentals/routing)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="8d9aa-217">従来のルーティング順序</span><span class="sxs-lookup"><span data-stu-id="8d9aa-217">Conventional routing order</span></span>

<span data-ttu-id="8d9aa-218">従来のルーティングは、アプリによって定義されたアクションとコントローラーの組み合わせにのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="8d9aa-219">これは、通常のルートが重複するケースを簡略化することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="8d9aa-220">、 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>、および<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>を使用し<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>てルートを追加すると、呼び出された順序に基づいて、そのエンドポイントに自動的に注文値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="8d9aa-221">以前に表示されたルートからの一致は、優先順位が高くなります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="8d9aa-222">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8d9aa-223">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="8d9aa-224">[Dedicated conventional routes](#dcr)など`{*article}`のすべてのルートパラメーターを持つ専用の従来のルートでは、ルートの[最長](xref:fundamentals/routing#greedy)一致が実現されます。これは、他のルートと一致するように意図した url と一致することを意味します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="8d9aa-225">最短一致の一致を防ぐために、ルートテーブルの中で最長一致のルートを指定します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="8d9aa-226">あいまいなアクションの解決</span><span class="sxs-lookup"><span data-stu-id="8d9aa-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="8d9aa-227">ルーティングを通じて2つのエンドポイントが一致する場合、ルーティングでは次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="8d9aa-228">最適な候補を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-228">Choose the best candidate.</span></span>
* <span data-ttu-id="8d9aa-229">例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-229">Throw an exception.</span></span>

<span data-ttu-id="8d9aa-230">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="8d9aa-231">前のコントローラーは、次の2つのアクションに一致するアクションを定義します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="8d9aa-232">URL パス`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="8d9aa-233">データ`{ controller = Products33, action = Edit, id = 17 }`をルーティングします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="8d9aa-234">これは、MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="8d9aa-235">`Edit(int)`製品を編集するためのフォームを表示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="8d9aa-236">`Edit(int, Product)`ポストされたフォームを処理します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="8d9aa-237">正しいルートを解決するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-237">To resolve the correct route:</span></span>

* <span data-ttu-id="8d9aa-238">`Edit(int, Product)`は、要求が HTTP `POST`の場合に選択されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="8d9aa-239">`Edit(int)`[HTTP 動詞](#verb)が他のものである場合は、が選択されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="8d9aa-240">`Edit(int)`は、一般に`GET`経由で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="8d9aa-241"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>、、は、要求の HTTP メソッドに基づいて選択できるように、ルーティングのために用意されてい`[HttpPost]`ます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="8d9aa-242">は`HttpPostAttribute`より`Edit(int, Product)`も`Edit(int)`より適切に一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="8d9aa-243">のような`HttpPostAttribute`属性の役割を理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="8d9aa-244">同様の属性は、他の[HTTP 動詞](#verb)に対して定義されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="8d9aa-245">[従来のルーティング](#cr)では、表示フォーム、送信フォームワークフローの一部である場合、アクションは同じアクション名を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="8d9aa-246">例については、「 [2 つの編集アクションメソッドの確認](xref:tutorials/first-mvc-app/controller-methods-views#get-post)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="8d9aa-247">ルーティングで最適な候補を選択できない<xref:System.Reflection.AmbiguousMatchException>場合は、一致する複数のエンドポイントを一覧表示するがスローされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="8d9aa-248">従来のルート名</span><span class="sxs-lookup"><span data-stu-id="8d9aa-248">Conventional route names</span></span>

<span data-ttu-id="8d9aa-249">次の`"blog"`例`"default"`の文字列とは、通常のルート名です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-250">ルート名によって、ルートに論理名が指定されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-250">The route names give the route a logical name.</span></span> <span data-ttu-id="8d9aa-251">名前付きルートは、URL の生成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="8d9aa-252">ルートの順序によって URL の生成が複雑になる可能性がある場合、名前付きルートを使用すると、URL の作成が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="8d9aa-253">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="8d9aa-254">ルート名:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-254">Route names:</span></span>

* <span data-ttu-id="8d9aa-255">URL の照合や要求の処理には影響しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="8d9aa-256">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-256">Are used only for URL generation.</span></span>

<span data-ttu-id="8d9aa-257">ルート名の概念は、ルーティングで[IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)として表されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="8d9aa-258">**ルート名**と**エンドポイント名**の用語:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="8d9aa-259">交換可能です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-259">Are interchangeable.</span></span>
* <span data-ttu-id="8d9aa-260">ドキュメントとコードで使用されるものは、記述されている API によって異なります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="8d9aa-261">REST Api の属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="8d9aa-262">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が[HTTP 動詞](#verb)によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="8d9aa-263">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="8d9aa-264">次`StartUp.Configure`のコードは、REST API の一般的なコードであり、次のサンプルで使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="8d9aa-265">前のコードでは<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> 、属性ルーティング`UseEndpoints`コントローラーをマップするために、内でが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="8d9aa-266">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-266">In the following example:</span></span>

* <span data-ttu-id="8d9aa-267">上記`Configure`のメソッドが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="8d9aa-268">`HomeController`既定の従来のルート`{controller=Home}/{action=Index}/{id?}`と同様の url のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="8d9aa-269">`HomeController.Index` `/`アクションは、URL パス`/Home`、、 `/Home/Index`、または`/Home/Index/3`のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="8d9aa-270">この例では、属性ルーティングと[従来のルーティング](#cr)の主なプログラミングの違いについて取り上げます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="8d9aa-271">属性ルーティングでは、ルートを指定するためにより多くの入力が必要です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="8d9aa-272">従来の既定のルートは、ルートをより簡潔に処理します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="8d9aa-273">ただし、属性ルーティングでは、各[アクション](#action)に適用されるルートテンプレートを正確に制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="8d9aa-274">属性ルーティングでは、コントローラー名とアクション名は、アクションが一致する役割を果たし**ません**。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-274">With attribute routing, the controller name and action names play **no** role in which action is matched.</span></span> <span data-ttu-id="8d9aa-275">次の例は、前の例と同じ Url に一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="8d9aa-276">次のコードでは、と`action` `controller`のトークン置換を使用しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="8d9aa-277">コントローラーには、 `[Route("[controller]/[action]")]`次のコードが適用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="8d9aa-278">前のコードでは`Index` `/` `~/` 、メソッドテンプレートをルートテンプレートに付加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="8d9aa-279">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="8d9aa-280">ルートテンプレートの選択の詳細については、[ルートテンプレートの優先順位](xref:fundamentals/routing#rtp)に関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="8d9aa-281">ルーティングの予約名</span><span class="sxs-lookup"><span data-stu-id="8d9aa-281">Reserved routing names</span></span>

<span data-ttu-id="8d9aa-282">コントローラーまたは Razor Pages を使用する場合は、次のキーワードが予約されたルートパラメーター名です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="8d9aa-283">属性`page`ルーティングでルートパラメーターとしてを使用することは、一般的なエラーです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="8d9aa-284">その結果、URL の生成によって一貫性のない動作がわかりにくくなります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="8d9aa-285">特別なパラメーター名は、url 生成操作が Razor ページとコントローラーのどちらを参照するかを決定するために、URL 生成によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="8d9aa-286">HTTP 動詞テンプレート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-286">HTTP verb templates</span></span>

<span data-ttu-id="8d9aa-287">ASP.NET Core には、次の HTTP 動詞テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="8d9aa-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="8d9aa-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="8d9aa-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="8d9aa-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="8d9aa-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="8d9aa-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="8d9aa-294">ルートテンプレート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-294">Route templates</span></span>

<span data-ttu-id="8d9aa-295">ASP.NET Core には、次のルートテンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="8d9aa-296">すべての[HTTP 動詞テンプレート](#verb)はルートテンプレートです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="8d9aa-297">[回送](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="8d9aa-298">Http 動詞属性を使用した属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="8d9aa-299">次のコントローラーを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8d9aa-300">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-300">In the preceding code:</span></span>

* <span data-ttu-id="8d9aa-301">各アクションには`[HttpGet]`属性が含まれています。これにより、一致する HTTP GET 要求のみが制限されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="8d9aa-302">アクション`GetProduct`には`"{id}"`テンプレートが含まれ`id`ているため、 `"api/[controller]"`コントローラーのテンプレートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="8d9aa-303">メソッドテンプレートは`"api/[controller]/"{id}""`です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="8d9aa-304">したがって、このアクションでは、の GET 要求`/api/test2/xyz`のみ`/api/test2/123`が`/api/test2/{any string}`、、、などの形式で一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="8d9aa-305">アクション`GetIntProduct`には、 `"int/{id:int}")`テンプレートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="8d9aa-306">テンプレート`:int`の部分では、整数`id`に変換できる文字列にルート値を制限します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="8d9aa-307">GET 要求`/api/test2/int/abc`:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="8d9aa-308">このアクションと一致しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="8d9aa-309">[404 が見つからないこと](https://developer.mozilla.org/docs/Web/HTTP/Status/404)を示すエラーを返します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="8d9aa-310">アクション`GetInt2Product`はテンプレート`{id}`に含まれていますが`id` 、整数に変換できる値に制限されていません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="8d9aa-311">GET 要求`/api/test2/int2/abc`:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="8d9aa-312">このルートと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-312">Matches this route.</span></span>
  * <span data-ttu-id="8d9aa-313">モデルバインドは整数へ`abc`の変換に失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="8d9aa-314">メソッド`id`のパラメーターは integer です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="8d9aa-315">モデルバインド[400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400)が整数に変換`abc`できなかったため、400の無効な要求が返されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="8d9aa-316"><xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute>属性ルーティング<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>では、、、など<xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>の属性<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="8d9aa-317">すべての[HTTP 動詞](#verb)属性は、ルートテンプレートを受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="8d9aa-318">次の例は、同じルートテンプレートに一致する2つのアクションを示しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="8d9aa-319">URL パス`/products3`を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="8d9aa-320">アクション`MyProductsController.ListProducts`は、 [HTTP 動詞](#verb)がの場合`GET`に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="8d9aa-321">アクション`MyProductsController.CreateProduct`は、 [HTTP 動詞](#verb)がの場合`POST`に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="8d9aa-322">REST API をビルドする場合、アクションはすべての HTTP メソッドを受け入れる`[Route(...)]`ため、アクションメソッドでを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="8d9aa-323">API がサポートする内容については、より具体的な[HTTP 動詞属性](#verb)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="8d9aa-324">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="8d9aa-325">REST Api では、属性ルーティングを使用して、アプリの機能をリソースのセットとしてモデル化し、操作が HTTP 動詞によって表されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="8d9aa-326">これは、同じ論理リソースに対する GET や POST などの多くの操作で同じ URL が使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="8d9aa-327">属性ルーティングでは、API のパブリック エンドポイント レイアウトを慎重に設計するために必要となるコントロールのレベルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="8d9aa-328">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="8d9aa-329">次の例では`id` 、URL パスの一部としてが必要です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="8d9aa-330">`Products2ApiController.GetProduct(int)`アクション:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="8d9aa-331">次のように URL パスを使用して実行する`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="8d9aa-332">URL パス`/products2`では実行されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="8d9aa-333">[[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性を使用すると、アクションでサポートされる要求のコンテンツの種類を制限できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="8d9aa-334">詳細については、「[利用属性を使用したサポートされる要求のコンテンツの種類の定義](xref:web-api/index#consumes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="8d9aa-335">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="8d9aa-336">の`[ApiController]`詳細については、「 [ApiController 属性](xref:web-api/index##apicontroller-attribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="8d9aa-337">ルート名</span><span class="sxs-lookup"><span data-stu-id="8d9aa-337">Route name</span></span>

<span data-ttu-id="8d9aa-338">次のコードでは、 の "`Products_List`ルート名" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="8d9aa-339">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="8d9aa-340">ルート名:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-340">Route names:</span></span>

* <span data-ttu-id="8d9aa-341">ルーティングの URL 照合動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="8d9aa-342">は、URL の生成にのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-342">Are only used for URL generation.</span></span>

<span data-ttu-id="8d9aa-343">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="8d9aa-344">前のコードと従来の既定のルートを比較します`id` 。これにより`{id?}`、パラメーターが省略可能な () として定義されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="8d9aa-345">Api を正確に指定する機能には、 `/products`と`/products/5`を別のアクションにディスパッチできるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="8d9aa-346">属性ルートの結合</span><span class="sxs-lookup"><span data-stu-id="8d9aa-346">Combining attribute routes</span></span>

<span data-ttu-id="8d9aa-347">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="8d9aa-348">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="8d9aa-349">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="8d9aa-350">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-350">In the preceding example:</span></span>

* <span data-ttu-id="8d9aa-351">URL パス`/products`を一致させることができます`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="8d9aa-352">URL パス`/products/5`を一致`ProductsApi.GetProduct(int)`させることができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="8d9aa-353">これらのアクションはどちらも、 `GET` `[HttpGet]`属性でマークされているので、HTTP とのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="8d9aa-354">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="8d9aa-355">次の例は、既定のルートに似た URL パスのセットに一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="8d9aa-356">次の表では`[Route]` 、上記のコードの属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="8d9aa-357">属性</span><span class="sxs-lookup"><span data-stu-id="8d9aa-357">Attribute</span></span>               | <span data-ttu-id="8d9aa-358">との組み合わせ`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="8d9aa-359">ルートテンプレートを定義します</span><span class="sxs-lookup"><span data-stu-id="8d9aa-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="8d9aa-360">はい</span><span class="sxs-lookup"><span data-stu-id="8d9aa-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="8d9aa-361">はい</span><span class="sxs-lookup"><span data-stu-id="8d9aa-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="8d9aa-362">**いいえ**</span><span class="sxs-lookup"><span data-stu-id="8d9aa-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="8d9aa-363">はい</span><span class="sxs-lookup"><span data-stu-id="8d9aa-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="8d9aa-364">属性のルートの順序</span><span class="sxs-lookup"><span data-stu-id="8d9aa-364">Attribute route order</span></span>

<span data-ttu-id="8d9aa-365">ルーティングによってツリーが構築され、すべてのエンドポイントが同時に一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="8d9aa-366">ルートエントリは、最適な順序で配置されているかのように動作します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="8d9aa-367">最も具体的なルートは、より一般的なルートの前に実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="8d9aa-368">たとえば、のよう`blog/search/{topic}`な属性ルートは、のような`blog/{*article}`属性ルートよりも具体的です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="8d9aa-369">既定`blog/search/{topic}`では、ルートの優先度は高くなります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="8d9aa-370">開発者は、[従来のルーティング](#cr)を使用して、ルートを目的の順序で配置します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="8d9aa-371">属性ルートでは、 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>プロパティを使用して注文を構成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="8d9aa-372">フレームワークに用意されているすべて`Order`の[ルート属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)には、が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="8d9aa-373">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="8d9aa-374">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-374">The default order is `0`.</span></span> <span data-ttu-id="8d9aa-375">を使用して`Order = -1`ルートを設定すると、順序が設定されていないルートよりも前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="8d9aa-376">を使用し`Order = 1`たルートの設定は、既定のルートの順序の後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="8d9aa-377">**Avoid**に`Order`よっては避けてください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="8d9aa-378">アプリの URL 空間で、明示的な順序の値を正しくルーティングする必要がある場合は、クライアントにも混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="8d9aa-379">一般に、属性ルーティングでは、URL が一致する正しいルートが選択されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="8d9aa-380">URL の生成に使用される既定の順序が機能していない場合は、通常、 `Order`プロパティを適用するよりも、ルート名をオーバーライドとして使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="8d9aa-381">次の2つのコントローラーで、両方がルート`/home`一致を定義しているとします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="8d9aa-382">前`/home`のコードでを要求すると、次のような例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="8d9aa-383">ルート`Order`属性のいずれかにを追加すると、あいまいさが解消されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="8d9aa-384">上記のコードでは`/home` 、に`HomeController.Index`よってエンドポイントが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="8d9aa-385">にアクセスするに`MyDemoController.MyIndex`は、 `/home/MyIndex`を要求します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="8d9aa-386">**注**: </span><span class="sxs-lookup"><span data-stu-id="8d9aa-386">**Note**:</span></span>

* <span data-ttu-id="8d9aa-387">上記のコードは、ルーティング設計の一例または不十分です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="8d9aa-388">これは、 `Order`プロパティを示すために使用されていました。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="8d9aa-389">プロパティ`Order`はあいまいさを解決するだけで、そのテンプレートは一致しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="8d9aa-390">`[Route("Home")]`テンプレートを削除することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="8d9aa-391">ページとRazorルートの順序については[、「ルートとアプリの規則Razor ](xref:razor-pages/razor-pages-conventions#route-order) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="8d9aa-392">場合によっては、あいまいなルートで HTTP 500 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="8d9aa-393">[ログ](xref:fundamentals/logging/index)を使用して、の原因`AmbiguousMatchException`となったエンドポイントを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="8d9aa-394">ルートテンプレートでのトークンの置換 [controller]、[action]、[area]</span><span class="sxs-lookup"><span data-stu-id="8d9aa-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="8d9aa-395">便宜上、属性ルートは、次のいずれかのトークンを囲むことによって、予約ルートパラメーターのトークン置換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="8d9aa-396">角かっこ:`[]`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="8d9aa-397">中かっこ:`{}`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-397">Curly braces: `{}`</span></span>

<span data-ttu-id="8d9aa-398">トークン`[action]`、 `[area]`、および`[controller]`は、ルートが定義されているアクションのアクション名、領域名、およびコントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="8d9aa-399">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="8d9aa-400">一致`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="8d9aa-401">一致`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="8d9aa-402">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="8d9aa-403">前の例では、次のコードと同じ動作が実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="8d9aa-404">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="8d9aa-405">これは、トークンの置換と組み合わせた強力な機能です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="8d9aa-406">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="8d9aa-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="8d9aa-408">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="8d9aa-409"> では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="8d9aa-410">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="8d9aa-411">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8d9aa-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="8d9aa-412">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="8d9aa-413">パラメーター トランスフォーマーは <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="8d9aa-414">たとえば、カスタム`SlugifyParameterTransformer`パラメータートランスフォーマーは、 `SubscriptionManagement`ルート値を次の`subscription-management`ように変更します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="8d9aa-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="8d9aa-416">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="8d9aa-417">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="8d9aa-418">前`ListAll`のメソッドは`/subscription-management/list-all`と一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="8d9aa-419">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="8d9aa-420">スラグの定義については、[スラグの MDN web ドキュメント](https://developer.mozilla.org/docs/Glossary/Slug)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="8d9aa-421">複数の属性ルート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-421">Multiple attribute routes</span></span>

<span data-ttu-id="8d9aa-422">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="8d9aa-423">これの最も一般的な使用方法は、次の例で示すように、"既定の規則ルート" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="8d9aa-424">コントローラーに複数のルート属性を配置することは、それぞれがアクションメソッドの各ルート属性と結合することを意味します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="8d9aa-425">すべての[HTTP 動詞](#verb)ルート制約は`IActionConstraint`、を実装します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="8d9aa-426">を実装<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>する複数のルート属性が1つのアクションに配置されている場合:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="8d9aa-427">各アクション制約は、コントローラーに適用されたルートテンプレートと結合されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="8d9aa-428">アクションで複数のルートを使用すると便利で強力な場合があります。アプリの URL 空間の基本を明確に定義しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="8d9aa-429">既存のクライアントをサポートするために必要な場合に**のみ**、アクションで複数のルートを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="8d9aa-430">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="8d9aa-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="8d9aa-431">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="8d9aa-432">前のコードでは`[HttpPost("product/{id:int}")]` 、はルート制約を適用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="8d9aa-433">アクション`ProductsController.ShowProduct`は、のような`/product/3`URL パスによってのみ照合されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="8d9aa-434">ルートテンプレートの部分`{id:int}`は、そのセグメントを整数のみに制限します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="8d9aa-435">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="8d9aa-436">IRouteTemplateProvider を使用したカスタムルート属性</span><span class="sxs-lookup"><span data-stu-id="8d9aa-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="8d9aa-437">すべての[ルート属性](#rt)はを<xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>実装します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="8d9aa-438">ASP.NET Core ランタイム:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="8d9aa-439">アプリの起動時に、コントローラークラスとアクションメソッドの属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="8d9aa-440">は、を実装`IRouteTemplateProvider`する属性を使用して、ルートの初期セットを構築します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="8d9aa-441">を`IRouteTemplateProvider`実装して、カスタムルート属性を定義します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="8d9aa-442">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="8d9aa-443">前`Get`のメソッドは`Order = 2, Template = api/MyTestApi`を返します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="8d9aa-444">アプリケーションモデルを使用して属性ルートをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8d9aa-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="8d9aa-445">アプリケーションモデル:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-445">The application model:</span></span>

* <span data-ttu-id="8d9aa-446">は、起動時に作成されるオブジェクトモデルです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="8d9aa-447">アプリでアクションをルーティングおよび実行するために ASP.NET Core によって使用されるすべてのメタデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="8d9aa-448">アプリケーションモデルには、ルート属性から収集されたすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="8d9aa-449">ルート属性からのデータは、 `IRouteTemplateProvider`実装によって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="8d9aa-450">規定</span><span class="sxs-lookup"><span data-stu-id="8d9aa-450">Conventions:</span></span>

* <span data-ttu-id="8d9aa-451">アプリケーションモデルを変更して、ルーティングの動作をカスタマイズするために、を記述できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="8d9aa-452">アプリの起動時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-452">Are read at app startup.</span></span>

<span data-ttu-id="8d9aa-453">このセクションでは、アプリケーションモデルを使用してルーティングをカスタマイズする基本的な例を示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="8d9aa-454">次のコードでは、ルートがプロジェクトのフォルダー構造とほぼ同じになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="8d9aa-455">次のコードは、 `namespace`属性がルーティングされているコントローラーに規則が適用されないようにします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="8d9aa-456">たとえば、次のコントローラーではを`NamespaceRoutingConvention`使用しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8d9aa-457">`NamespaceRoutingConvention.Apply` メソッド:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="8d9aa-458">コントローラーが属性ルーティングされている場合は、何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="8d9aa-459">に`namespace`基づいてコントローラーテンプレートを設定します。ベース`namespace`は削除されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="8d9aa-460">は`NamespaceRoutingConvention` 、次の方法`Startup.ConfigureServices`で適用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="8d9aa-461">たとえば、次のコントローラーを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="8d9aa-462">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-462">In the preceding code:</span></span>

* <span data-ttu-id="8d9aa-463">ベース`namespace`が`My.Application`です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="8d9aa-464">前のコントローラーの完全な名前は`My.Application.Admin.Controllers.UsersController`です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="8d9aa-465">に`NamespaceRoutingConvention`よって、コントローラーテンプレート`Admin/Controllers/Users/[action]/{id?`がに設定されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="8d9aa-466">は`NamespaceRoutingConvention` 、コントローラーの属性として適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="8d9aa-467">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="8d9aa-468">ASP.NET Core アプリでは、従来のルーティングと属性ルーティングの使用を混在させることができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="8d9aa-469">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="8d9aa-470">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="8d9aa-471">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="8d9aa-472">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="8d9aa-473">コントローラー**のルート属性では、コントローラー**属性の**すべて**のアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="8d9aa-474">属性ルーティングと従来のルーティングでは、同じルーティングエンジンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="8d9aa-475">URL 生成とアンビエント値</span><span class="sxs-lookup"><span data-stu-id="8d9aa-475">URL Generation and ambient values</span></span>

<span data-ttu-id="8d9aa-476">アプリでは、ルーティング URL 生成機能を使用して、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="8d9aa-477">Url を生成することで、Url をハードコーディングすることがなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="8d9aa-478">このセクションでは、MVC によって提供される URL 生成機能について説明し、URL の生成のしくみの基本についてのみ説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="8d9aa-479">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="8d9aa-480"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>インターフェイスは、MVC と URL 生成のルーティングの間のインフラストラクチャの基になる要素です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="8d9aa-481">の`IUrlHelper`インスタンスは、コントローラー、ビュー `Url` 、およびビューコンポーネントのプロパティを介して使用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="8d9aa-482">次の例では、 `IUrlHelper` `Controller.Url`プロパティを通じてインターフェイスを使用して、別のアクションへの URL を生成しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-483">アプリで既定のルートを使用している場合、 `url`変数の値は URL パス文字列`/UrlGeneration/Destination`になります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="8d9aa-484">この URL パスは、次の組み合わせによってルーティングによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="8d9aa-485">現在の要求からのルート値 (**アンビエント値**と呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="8d9aa-486">に渡され、 `Url.Action`それらの値をルートテンプレートに置き換える値。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="8d9aa-487">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="8d9aa-488">値を持たないルートパラメーターには、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="8d9aa-489">既定値がある場合は、それを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="8d9aa-490">省略可能な場合はスキップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-490">Be skipped if it's optional.</span></span> <span data-ttu-id="8d9aa-491">たとえば、ルートテンプレート`id` `{controller}/{action}/{id?}`のを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="8d9aa-492">必要なルートパラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="8d9aa-493">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="8d9aa-494">前の例で`Url.Action`は、[従来のルーティング](#cr)が想定されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="8d9aa-495">URL の生成は、[属性ルーティング](#ar)と同様に機能しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="8d9aa-496">従来のルーティングの場合:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-496">With conventional routing:</span></span>

* <span data-ttu-id="8d9aa-497">ルート値は、テンプレートを展開するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="8d9aa-498">と`controller` `action`のルート値は、通常、そのテンプレートに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="8d9aa-499">これは、ルーティングによって一致した Url が規則に準拠しているために機能します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="8d9aa-500">次の例では、属性ルーティングを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-501">前`Source`のコードのアクションによっ`custom/url/to/destination`てが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="8d9aa-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>は、の代わりに ASP.NET Core 3.0 で追加`IUrlHelper`されました。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="8d9aa-503">`LinkGenerator`は、同様に柔軟な機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="8d9aa-504">の`IUrlHelper`各メソッドには、に`LinkGenerator`対応するメソッドのファミリもあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="8d9aa-505">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-505">Generating URLs by action name</span></span>

<span data-ttu-id="8d9aa-506">[Url. action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)、 [Linkgenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)、およびすべての関連するすべてのオーバーロードは、コントローラー名とアクション名を指定してターゲットエンドポイントを生成するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="8d9aa-507">を使用`Url.Action`する場合、と`controller` `action`の現在のルート値は、ランタイムによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="8d9aa-508">と`controller` `action`の値は、[アンビエント値](#ambient)と値の両方に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="8d9aa-509">メソッド`Url.Action`は、常にと`action` `controller`の現在の値を使用し、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="8d9aa-510">ルーティングでは、アンビエント値の値を使用して、URL の生成時に提供されなかった情報を入力しようとします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="8d9aa-511">アンビエント値`{ a = Alice, b = Bob, c = Carol, d = David }`のよう`{a}/{b}/{c}/{d}`なルートを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="8d9aa-512">ルーティングには、URL を生成するための十分な情報が含まれており、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="8d9aa-513">すべてのルートパラメーターに値があるため、ルーティングに十分な情報が得られます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="8d9aa-514">値`{ d = Donovan }`が追加された場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="8d9aa-515">この値`{ d = David }`は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="8d9aa-516">生成された URL `Alice/Bob/Carol/Donovan`パスがです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="8d9aa-517">**警告**: URL パスは階層化されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="8d9aa-518">前の例で、値`{ c = Cheryl }`が追加されている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="8d9aa-519">両方の値`{ c = Carol, d = David }`が無視されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="8d9aa-520">の値はなく、URL の`d`生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="8d9aa-521">URL を生成する`c`に`d`は、との必要な値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="8d9aa-522">既定のルート`{controller}/{action}/{id?}`では、この問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="8d9aa-523">この問題は、常にと`Url.Action`の`controller`値が明示的`action`に指定されているため、実際にはめったにありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="8d9aa-524">Url のいくつかのオーバーロードでは、ルート値オブジェクトを使用して、および`action`以外`controller`のルートパラメーターの値を指定[します。](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)</span><span class="sxs-lookup"><span data-stu-id="8d9aa-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="8d9aa-525">ルート値オブジェクトは、と共に`id`よく使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="8d9aa-526">たとえば、「 `Url.Action("Buy", "Products", new { id = 17 })` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="8d9aa-527">ルート値オブジェクト:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-527">The route values object:</span></span>

* <span data-ttu-id="8d9aa-528">慣例により、通常は匿名型のオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="8d9aa-529">に`IDictionary<>`は、または[POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)を指定できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="8d9aa-530">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8d9aa-531">前のコードに`/Products/Buy/17?color=red`よってが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="8d9aa-532">次のコードでは、絶対 URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="8d9aa-533">絶対 URL を作成するには、次のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="8d9aa-534">を`protocol`受け入れるオーバーロード。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="8d9aa-535">たとえば、上記のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-535">For example, the preceding code.</span></span>
* <span data-ttu-id="8d9aa-536">[Linkgenerator. GetUriByAction。](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)既定で絶対 uri を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="8d9aa-537">ルートによる Url の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-537">Generate URLs by route</span></span>

<span data-ttu-id="8d9aa-538">前のコードでは、コントローラーとアクション名を渡すことによって URL を生成することを示していました。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="8d9aa-539">`IUrlHelper`には、メソッドの[Url routeurl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)ファミリも用意されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="8d9aa-540">これらのメソッドは、 [Url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)に似ていますが、と`action` `controller`の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="8d9aa-541">の最も一般的な使用`Url.RouteUrl`方法は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="8d9aa-542">URL を生成するルート名を指定します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="8d9aa-543">通常、コントローラーまたはアクション名は指定しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-544">次Razorのファイルでは、への`Destination_Route`HTML リンクが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="8d9aa-545">HTML およびでの Url の生成Razor</span><span class="sxs-lookup"><span data-stu-id="8d9aa-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="8d9aa-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper><xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>メソッドを使用して、要素と`<form>` `<a>`要素をそれぞれ生成するように html.actionlink と[html](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*)を[提供します](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="8d9aa-547">これらのメソッドは、Url を生成するために[url. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)メソッドを使用し、同様の引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="8d9aa-548">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="8d9aa-549">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="8d9aa-550">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="8d9aa-551">詳細については、「[フォームのタグヘルパー](xref:mvc/views/working-with-forms) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="8d9aa-552">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="8d9aa-553">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-553">URL generation in Action Results</span></span>

<span data-ttu-id="8d9aa-554">前の例では`IUrlHelper` 、コントローラーでを使用して示しました。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="8d9aa-555">コントローラーで最も一般的に使用されるのは、アクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="8d9aa-556">基底クラス <xref:Microsoft.AspNetCore.Mvc.ControllerBase> および <xref:Microsoft.AspNetCore.Mvc.Controller> では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="8d9aa-557">一般的な使用方法の1つは、ユーザー入力を受け入れた後にリダイレクトすることです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="8d9aa-558">アクションの結果ファクトリメソッド ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*>や<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>など) は、の`IUrlHelper`メソッドと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="8d9aa-559">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="8d9aa-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="8d9aa-560">[従来のルーティング](#cr)では、[専用の従来のルート](#dcr)と呼ばれる特別な種類のルート定義を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="8d9aa-561">次の例では、という`blog`名前のルートが専用の従来のルートになっています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-562">前のルート定義を使用`Url.Action("Index", "Home")`すると、は`/` `default`ルートを使用して URL パスを生成しますが、その理由は何でしょうか。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="8d9aa-563">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="8d9aa-564">[専用の従来のルート](#dcr)では、ルートパラメーターが指定されていない既定値の特殊な動作に依存しています。これにより、ルートが URL 生成に[最長一致](xref:fundamentals/routing#greedy)するのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="8d9aa-565">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="8d9aa-566">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="8d9aa-567">値`blog` `{ controller = Home, action = Index }`が一致`{ controller = Blog, action = Article }`しないため、を使用して URL を生成することはできません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="8d9aa-568">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="8d9aa-569">Areas</span><span class="sxs-lookup"><span data-stu-id="8d9aa-569">Areas</span></span>

<span data-ttu-id="8d9aa-570">[区分](xref:mvc/controllers/areas)は、関連する機能を別のグループとしてグループにまとめるために使用される MVC 機能です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="8d9aa-571">コントローラーアクションのルーティング名前空間。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="8d9aa-572">ビューのフォルダー構造。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-572">Folder structure for views.</span></span>

<span data-ttu-id="8d9aa-573">区分を使用すると、異なる領域がある限り、同じ名前の複数のコントローラーをアプリに割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="8d9aa-574">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="8d9aa-575">ここでは、ルーティングと領域の相互作用について説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="8d9aa-576">領域をビューで使用する方法の詳細については、「[区分](xref:mvc/controllers/areas)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="8d9aa-577">次の例では、既定の従来のルートと、 `area` `area`という名前`Blog`ののルートを使用するように MVC を構成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="8d9aa-578">上記のコードで<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>は、が呼び出され、 `"blog_route"`が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="8d9aa-579">2番目の`"Blog"`パラメーターは、領域名です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="8d9aa-580">のような`/Manage/Users/AddUser`URL パスを照合する`"blog_route"`と、ルートによっ`{ area = Blog, controller = Users, action = AddUser }`てルート値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="8d9aa-581">ルート`area`値は、の`area`既定値によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="8d9aa-582">によって`MapAreaControllerRoute`作成されるルートは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="8d9aa-583">`MapAreaControllerRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="8d9aa-584">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="8d9aa-585">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8d9aa-586">一般に、区分を持つルートは、領域を持たないルートよりも固有であるため、前に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8d9aa-587">前の例を使用すると、 `{ area = Blog, controller = Users, action = AddUser }`ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="8d9aa-588">[[区分]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性は、1つの領域の一部としてコントローラーを表します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="8d9aa-589">このコントローラーは、 `Blog`この領域にあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="8d9aa-590">属性を`[Area]`持たないコントローラーは、どの領域のメンバーでもなく、ルーティングに`area`よってルート値が提供されるときには一致しませ**ん**。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="8d9aa-591">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="8d9aa-592">各コントローラーの名前空間は、完全を期すためにここに示されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="8d9aa-593">前のコントローラーで同じ名前空間が使用されている場合は、コンパイラエラーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="8d9aa-594">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="8d9aa-595">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="8d9aa-596">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="8d9aa-597">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="8d9aa-598">領域内でアクションを実行する場合、の`area`ルート値は、URL の生成に使用するルーティングの[アンビエント値](#ambient)として使用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="8d9aa-599">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="8d9aa-600">次のコードでは、へ`/Zebra/Users/AddUser`の URL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="8d9aa-601">アクションの定義</span><span class="sxs-lookup"><span data-stu-id="8d9aa-601">Action definition</span></span>

<span data-ttu-id="8d9aa-602">[非 action](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性を持つパブリックメソッドは、アクションです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="8d9aa-603">サンプル コード</span><span class="sxs-lookup"><span data-stu-id="8d9aa-603">Sample code</span></span>

 * <span data-ttu-id="8d9aa-604">[Mydisplayrouteinfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)メソッドは[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)に含まれており、ルーティング情報を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="8d9aa-605">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d9aa-606">ASP.NET Core MVC は、ルーティング [ミドルウェア](xref:fundamentals/middleware/index)を使って、受信した要求の URL を照合し、アクションにマップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="8d9aa-607">ルートは、スタートアップ コードまたは属性で定義されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="8d9aa-608">ルートでは、URL パスとアクションの照合方法が記述されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="8d9aa-609">また、ルートは、応答で送信される (リンク用) の URL の生成にも使われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="8d9aa-610">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="8d9aa-611">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="8d9aa-612">詳しくは、「[混合ルーティング](#routing-mixed-ref-label)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="8d9aa-613">このドキュメントでは、MVC とルーティングの間の相互作用、および標準的な MVC アプリでのルーティング機能の利用方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="8d9aa-614">高度なルーティングについて詳しくは、「[ASP.NET Core のルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="8d9aa-615">ルーティング ミドルウェアの設定</span><span class="sxs-lookup"><span data-stu-id="8d9aa-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="8d9aa-616">*Configure* メソッドには、次のようなコードが含まれることがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="8d9aa-617">`UseMvc` の呼び出しの内部で、`MapRoute` は単一のルートの作成に使われます。これは、`default` ルートと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="8d9aa-618">ほとんどの MVC アプリは、`default` ルートのようなルートをテンプレートで使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="8d9aa-619">ルート テンプレート `"{controller=Home}/{action=Index}/{id?}"` は、`/Products/Details/5` のような URL パスと一致し、パスをトークン化することによってルート値 `{ controller = Products, action = Details, id = 5 }` を抽出します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="8d9aa-620">MVC は、`ProductsController` という名前のコントローラーを探して、アクション `Details` の実行を試みます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="8d9aa-621">この例では、このアクションを呼び出すときに、モデル バインドが `id = 5` という値を使って、`id` パラメーターを `5` に設定することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="8d9aa-622">詳しくは、「[モデル バインド](../models/model-binding.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="8d9aa-623">`default` ルートの使用:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="8d9aa-624">ルート テンプレート:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-624">The route template:</span></span>

* <span data-ttu-id="8d9aa-625">`{controller=Home}` は、`Home` を既定の `controller` として定義します</span><span class="sxs-lookup"><span data-stu-id="8d9aa-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="8d9aa-626">`{action=Index}` は、`Index` を既定の `action` として定義します</span><span class="sxs-lookup"><span data-stu-id="8d9aa-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="8d9aa-627">`{id?}` は、`id` を省略可能として定義します</span><span class="sxs-lookup"><span data-stu-id="8d9aa-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="8d9aa-628">既定および省略可能のルート パラメーターは、URL パスに存在していなくても一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="8d9aa-629">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="8d9aa-630">`"{controller=Home}/{action=Index}/{id?}"` は URL パス `/` と一致でき、ルート値 `{ controller = Home, action = Index }` を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="8d9aa-631">`controller` と `action` の値は既定値を使い、`id` は URL パスに対応するセグメントが存在しないため値を生成しません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="8d9aa-632">MVC はこれらのルート値を使って、`HomeController` および `Index` アクションを選びます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="8d9aa-633">このコントローラー定義とルート テンプレートを使うと、`HomeController.Index` アクションは次のいずれかの URL パスに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="8d9aa-634">便利なメソッド `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="8d9aa-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="8d9aa-635">これは、次の代わりに使うことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="8d9aa-636">`UseMvc` および `UseMvcWithDefaultRoute` は、`RouterMiddleware` のインスタンスをミドルウェア パイプラインに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="8d9aa-637">MVC は、ミドルウェアと直接相互作用せず、ルーティングを使って要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="8d9aa-638">MVC は、`MvcRouteHandler` のインスタンスによってルートに接続されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="8d9aa-639">`UseMvc` の内部のコードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="8d9aa-640">`UseMvc` は、ルートを直接定義することはなく、`attribute` ルートのルート コレクションにプレースホルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="8d9aa-641">オーバーロード `UseMvc(Action<IRouteBuilder>)` は、独自ルートの追加を可能にし、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="8d9aa-642">`UseMvc` とそのすべてのバリエーションは、属性ルートのプレースホルダーを追加します。属性ルーティングは、`UseMvc` の構成方法に関係なく常に利用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="8d9aa-643">`UseMvcWithDefaultRoute` は既定のルートを定義し、属性ルーティングをサポートします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="8d9aa-644">「[属性ルーティング](#attribute-routing-ref-label)」セクションでは、属性ルーティングについてさらに詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="8d9aa-645">規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-645">Conventional routing</span></span>

<span data-ttu-id="8d9aa-646">次は `default` ルートです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="8d9aa-647">前のコードは、従来のルーティングの例です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="8d9aa-648">このスタイルは、URL パスの*規則*を確立するため、従来のルーティングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="8d9aa-649">最初のパスセグメントは、コントローラー名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="8d9aa-650">2つ目は、アクション名にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-650">The second maps to the action name.</span></span>
* <span data-ttu-id="8d9aa-651">3番目のセグメントは、省略`id`可能なに使用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="8d9aa-652">`id`モデルエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="8d9aa-653">この `default` ルートを使うと、URL パス `/Products/List` は `ProductsController.List` アクションにマップし、`/Blog/Article/17` は `BlogController.Article` にマップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="8d9aa-654">このマッピングは、コントローラーとアクションの名前に**のみ**基づき、名前空間、ソース ファイルの場所、またはメソッドのパラメーターには基づきません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="8d9aa-655">既定のルートで規則ルーティングを使うと、定義するアクションごとに新しい URL パターンを考える必要がなく、アプリケーションをすばやく構築できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="8d9aa-656">CRUD スタイルのアクションを使うアプリケーションでは、コントローラー間で URL に一貫性を持たせると、コードが容易になり、UI の予測可能性が向上します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="8d9aa-657">`id` はルート テンプレートで省略可能と定義されており、これは URL の一部として ID を提供されなくてもアクションを実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="8d9aa-658">通常、`id` が URL から省略されると、ID はモデル バインドによって `0` に設定され、結果として、`id == 0` と一致するエンティティはデータベースで検索されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="8d9aa-659">属性ルーティングを使うと、ID が必須のアクションと必須ではないアクションをきめ細かく制御できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="8d9aa-660">慣例に従って、ドキュメントでは `id` などの省略可能なパラメーターが正しい使用法で使われる可能性がある場合はパラメーターを記載してあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="8d9aa-661">複数のルート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-661">Multiple routes</span></span>

<span data-ttu-id="8d9aa-662">`MapRoute` の呼び出しをさらに追加することで、`UseMvc` の内部に複数のルートを追加できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="8d9aa-663">これにより、次のように、複数の規則を定義すること、または特定のアクションに専用の規則ルートを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="8d9aa-664">`blog` ルートは "*専用の規則ルート*" です。これは、このルートは規則ルーティング システムを使いますが、特定のアクション専用であることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="8d9aa-665">`controller` と `action` はパラメーターとしてルート テンプレートに表示されないので、既定値を持つことだけができ、したがってこのルートは常に `BlogController.Article` アクションにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="8d9aa-666">ルート コレクション内のルートには順序があり、追加された順序で処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="8d9aa-667">そのため、この例では、`blog` ルートが `default` ルートより先に試されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-668">*専用の従来のルート*では、多くの場合`{*article}` 、のような**キャッチオール**ルートパラメーターを使用して、URL パスの残りの部分をキャプチャします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="8d9aa-669">このようにすると、ルートの "一致範囲が広くなりすぎて"、他のルートと一致させるつもりであった URL まで一致する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="8d9aa-670">この問題を解決するには、"一致範囲の広い" ルートはルート テーブルの後の方に配置します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="8d9aa-671">フォールバック</span><span class="sxs-lookup"><span data-stu-id="8d9aa-671">Fallback</span></span>

<span data-ttu-id="8d9aa-672">要求処理の一環として、MVC はルートの値を使ってアプリケーション内のコントローラーとアクションを検索できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="8d9aa-673">ルートの値がアクションと一致しない場合、そのルートは一致と見なされず、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="8d9aa-674">これは "*フォールバック*" と呼ばれ、規則ルートがオーバーラップしている場合の簡略化を意図したものです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="8d9aa-675">アクションの明確化</span><span class="sxs-lookup"><span data-stu-id="8d9aa-675">Disambiguating actions</span></span>

<span data-ttu-id="8d9aa-676">2 つのアクションがルーティングで一致する場合、MVC はあいまいさを解消して "最善の" 候補を選ぶか、または例外をスローする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="8d9aa-677">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="8d9aa-678">このコントローラーでは、URL パス `/Products/Edit/17` およびルート データ `{ controller = Products, action = Edit, id = 17 }` と一致する 2 つのアクションが定義されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="8d9aa-679">これは、`Edit(int)` で製品を編集するためのフォームを表示し、`Edit(int, Product)` で送信されたフォームを処理する MVC コントローラーの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="8d9aa-680">これを MVC で可能にするには、要求が HTTP `POST` の場合は `Edit(int, Product)` を選び、それ以外の HTTP 動詞の場合は `Edit(int)` を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="8d9aa-681">`HttpPostAttribute` (`[HttpPost]`) は、HTTP 動詞が `POST` の場合にのみそのアクションの選択を許可する `IActionConstraint` の実装です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="8d9aa-682">`IActionConstraint` が存在すると、`Edit(int, Product)` の方が `Edit(int)` より "良い" 一致になるので、`Edit(int, Product)` が最初に試されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="8d9aa-683">カスタム `IActionConstraint` を作成する必要があるのは特殊なシナリオだけですが、`HttpPostAttribute` のような属性の役割を理解しておくことが重要です。似た属性が他の HTTP 動詞に対しても定義されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="8d9aa-684">規則ルーティングでは、アクションが `show form -> submit form` ワークフローの一部になっている場合、複数のアクションが同じアクション名を使うのはよくあることです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="8d9aa-685">「[IActionConstraint について](#understanding-iactionconstraint)」セクションを読むと、このパターンの便利さがいっそうよくわかります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="8d9aa-686">複数のルートが一致し、MVC が "最善の " ルートを発見できない場合、MVC は `AmbiguousActionException` をスローします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="8d9aa-687">ルート名</span><span class="sxs-lookup"><span data-stu-id="8d9aa-687">Route names</span></span>

<span data-ttu-id="8d9aa-688">次の例の文字列 `"blog"` と `"default"` は、ルート名です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="8d9aa-689">ルート名は、URL の生成に名前付きのルートを使うことができるよう、ルートに論理名を提供します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="8d9aa-690">これは、ルートの順序指定によって URL の生成が複雑になる場合に、URL の作成を大幅に合理化します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="8d9aa-691">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="8d9aa-692">ルート名が URL の照合や要求の処理に影響を与えることはありません。ルート名は URL の生成のみに使われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="8d9aa-693">MVC 固有のヘルパーでの URL の生成など、URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="8d9aa-694">属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-694">Attribute routing</span></span>

<span data-ttu-id="8d9aa-695">属性ルーティングでは、属性のセットを使ってアクションをルート テンプレートに直接マップします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="8d9aa-696">次の例では、`app.UseMvc();` が `Configure` メソッド内で使われており、ルートは渡されていません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="8d9aa-697">`HomeController` は、既定のルート `{controller=Home}/{action=Index}/{id?}` が一致するのと同じように、URL のセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="8d9aa-698">`HomeController.Index()` アクションは、URL パス `/`、`/Home`、または `/Home/Index` のいずれかに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-699">この例では、属性ルーティングと規則ルーティングでのプログラミングの大きな違いが強調して示されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="8d9aa-700">属性ルーティングの方が、ルートを指定するために多くの入力を必要とします。既定の規則ルートの方が簡潔にルートを処理します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="8d9aa-701">ただし、属性ルーティングでは、各アクションに適用するルート テンプレートを正確に制御できます (そして制御する必要があります)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="8d9aa-702">属性ルーティングでは、コントローラー名とアクション名はアクションの選択において役割を**持ちません**。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="8d9aa-703">この例では、前の例と同じ URL を照合します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-703">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="8d9aa-704">上のルート テンプレートでは、`action`、`area`、`controller` のルート パラメーターは定義されていません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="8d9aa-705">実際、これらのルート パラメーターは属性ルートでは許可されていません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="8d9aa-706">ルート テンプレートはアクションと既に関連付けられているため、URL からアクション名を解析しても意味はありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="8d9aa-707">Http[Verb] 属性を使用する属性ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="8d9aa-708">属性ルーティングでは、`HttpPostAttribute` などの `Http[Verb]` 属性も使うことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="8d9aa-709">これらの属性はすべて、ルート テンプレートを受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="8d9aa-710">次の例では、同じルート テンプレートと一致する 2 つのアクションが示されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-710">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="8d9aa-711">`/products` のような URL パスの場合、HTTP 動詞が `GET` のときは `ProductsApi.ListProducts` アクションが実行され、HTTP 動詞が `POST` のときは `ProductsApi.CreateProduct` が実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="8d9aa-712">属性ルーティングでは、最初に、ルート属性で定義されているルート テンプレートのセットに対して URL が照合されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="8d9aa-713">ルート テンプレートが一致すると、`IActionConstraint` 制約が適用されて、実行できるアクションが決定されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="8d9aa-714">REST API を構築する場合、アクションではすべての HTTP メソッドが受け入れられるので、アクション メソッドに対して `[Route(...)]` を使用することはまれです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="8d9aa-715">より固有の `Http*Verb*Attributes` を使って、API がサポートするものを正確に指定するのがよい方法です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="8d9aa-716">REST API のクライアントは、パスおよび HTTP 動詞と特定の論理操作のマップを理解していることを期待されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="8d9aa-717">属性ルートは特定のアクションに適用されるため、ルート テンプレート定義の一部として簡単にパラメーターを必須にできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="8d9aa-718">次の例では、`id` は URL パスの一部として必須です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="8d9aa-719">`ProductsApi.GetProduct(int)` アクションは、`/products/3` のような URL パスに対しては実行されますが、`/products` のような URL パスに対しては実行されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="8d9aa-720">ルート テンプレートと関連するオプションについて詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="8d9aa-721">ルート名</span><span class="sxs-lookup"><span data-stu-id="8d9aa-721">Route Name</span></span>

<span data-ttu-id="8d9aa-722">次のコードでは、`Products_List` の "*ルート名*" を定義しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="8d9aa-723">ルート名を使うと、特定のルートに基づいて URL を生成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="8d9aa-724">ルート名は、ルーティングの URL 照合動作には影響を与えず、URL 生成に対してのみ使われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="8d9aa-725">ルート名は、アプリケーション全体で一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-726">これに対し、規則 "*既定ルート*" では、`id` パラメーターは省略可能 (`{id?}`) として定義されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="8d9aa-727">API を厳密に指定するこの機能には、`/products` と `/products/5` を異なるアクションに対してディスパッチできるといった利点があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="8d9aa-728">ルートの結合</span><span class="sxs-lookup"><span data-stu-id="8d9aa-728">Combining routes</span></span>

<span data-ttu-id="8d9aa-729">属性ルーティングの反復を少なくするため、コントローラーのルート属性は個々のアクションでのルート属性と結合されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="8d9aa-730">コントローラーで定義されているルート テンプレートが、アクションのルート テンプレートの前に付加されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="8d9aa-731">ルート属性をコントローラーに配置すると、コントローラーの**すべて**のアクションが属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="8d9aa-732">次の例では、URL パス `/products` は `ProductsApi.ListProducts` と一致でき、URL パス `/products/5` は `ProductsApi.GetProduct(int)` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="8d9aa-733">どちらのアクションも、`HttpGetAttribute` でマークされているため、HTTP `GET` だけと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="8d9aa-734">`/` または `~/` で始まるアクションに適用されるルート テンプレートは、コントローラーに適用されるルート テンプレートと結合されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="8d9aa-735">次の例は、"*既定ルート*" と同様の URL パスのセットと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-735">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="8d9aa-736">属性ルートの順序の指定</span><span class="sxs-lookup"><span data-stu-id="8d9aa-736">Ordering attribute routes</span></span>

<span data-ttu-id="8d9aa-737">定義された順序で実行される従来のルートとは対照的に、属性ルーティングはツリーを構築し、すべてのルートを同時に照合します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="8d9aa-738">これは、ルート エントリが理想的な順序で配置されているかのように動作します。一般的なルートより前に、最も固有のルートが実行する機会があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="8d9aa-739">たとえば、`blog/search/{topic}` のようなルートは、`blog/{*article}` のようなルートより固有です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="8d9aa-740">論理的には、`blog/search/{topic}` ルートが唯一の意味のある順序なので、既定では、これが最初に "実行" されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="8d9aa-741">規則ルーティングを使うときは、開発者が目的の順序でルートを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="8d9aa-742">属性ルートでは、フレームワークが提供するすべてのルート属性の `Order` プロパティを使って、順序を構成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="8d9aa-743">ルートは、`Order` プロパティの昇順に従って処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="8d9aa-744">既定の順序は `0` です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-744">The default order is `0`.</span></span> <span data-ttu-id="8d9aa-745">`Order = -1` を使ってルートを設定すると、順序が設定されていないルートの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="8d9aa-746">`Order = 1` を使ってルートを設定すると、既定のルート順序の後で実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="8d9aa-747">`Order` には依存しないでください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="8d9aa-748">URL 空間で正しくルーティングするために明示的な順序値が必要な場合、クライアントの混乱を招く可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="8d9aa-749">一般に、属性ルーティングは URL 照合で正しいルートを選びます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="8d9aa-750">URL の生成に使われる既定の順序がうまくいかない場合は、通常、オーバーライドとしてルート名を使う方が、`Order` プロパティを適用するより簡単です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="8d9aa-751">ページルーティングと MVC コントローラールーティングは、実装を共有します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-751"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="8d9aa-752">「ページのルート[ Razorとアプリの規則: ルートの順序](xref:razor-pages/razor-pages-conventions#route-order)」で、 Razorルートの順序に関する情報を参照できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="8d9aa-753">ルート テンプレートでのトークンの置換 ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="8d9aa-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="8d9aa-754">便宜上、属性ルートは、 *token replacement*トークンを角かっこ (`[`, `]`) で囲むことによってトークンの置換をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="8d9aa-755">トークン `[action]`、`[area]`、および `[controller]` は、ルートが定義されているアクションのアクション名、区分名、コントローラー名の値に置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="8d9aa-756">次の例では、アクションはコメントで説明されているように URL パスと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="8d9aa-757">属性ルート作成の最後のステップで、トークンの置換が発生します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="8d9aa-758">上の例は、次のコードと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="8d9aa-759">属性ルートを継承と組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="8d9aa-760">これをトークンの置換と組み合わせると特に強力です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-760">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="8d9aa-761">トークンの置換は、属性ルートで定義されているルート名にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="8d9aa-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` では、アクションごとに一意のルート名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="8d9aa-763">リテラル トークン置換区切り文字 `[` または `]` と一致させるためには、その文字を繰り返すことでエスケープします (`[[` または `]]`)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="8d9aa-764">パラメーター トランスフォーマーを使用してトークンの置換をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="8d9aa-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="8d9aa-765">トークンの置換は、パラメーター トランスフォーマーを使用してカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="8d9aa-766">パラメーター トランスフォーマーは `IOutboundParameterTransformer` を実装し、パラメーターの値を変換します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="8d9aa-767">たとえば、`SlugifyParameterTransformer` パラメーター トランスフォーマーでは、`SubscriptionManagement` のルート値が `subscription-management` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="8d9aa-768">`RouteTokenTransformerConvention` は、次のようなアプリケーション モデルの規則です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="8d9aa-769">パラメーター トランスフォーマーをアプリケーションのすべての属性ルートに適用します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="8d9aa-770">置き換えられる属性ルートのトークン値をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="8d9aa-771">`RouteTokenTransformerConvention` は、`ConfigureServices` でオプションとして登録されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="8d9aa-772">複数のルート</span><span class="sxs-lookup"><span data-stu-id="8d9aa-772">Multiple Routes</span></span>

<span data-ttu-id="8d9aa-773">属性ルーティングでは、同じアクションに到達する複数のルートの定義がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="8d9aa-774">これの最も一般的な使用方法は、次の例で示すように、"*既定の規則ルート*" の動作を模倣することです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="8d9aa-775">コントローラーに複数のルート属性を配置すると、それぞれが、アクション メソッドの各ルート属性と結合します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="8d9aa-776">アクションに (`IActionConstraint` を実装する) 複数のルート属性を配置すると、各アクション制約がそれを定義した属性のルート テンプレートと結合します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="8d9aa-777">アクションで複数のルートを使うと強力に見えますが、アプリケーションの URL 空間は簡潔で明確に定義された状態に保つことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="8d9aa-778">アクションで複数のルートを使うのは、必要な場合 (既存のクライアントをサポートする、など) だけにしてください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="8d9aa-779">属性ルートの省略可能なパラメーター、既定値、制約を指定する</span><span class="sxs-lookup"><span data-stu-id="8d9aa-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="8d9aa-780">属性ルートでは、省略可能なパラメーター、既定値、および制約の指定に関して、規則ルートと同じインライン構文がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="8d9aa-781">ルート テンプレートの構文について詳しくは、「[ルート テンプレート参照](xref:fundamentals/routing#route-template-reference)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="8d9aa-782">`IRouteTemplateProvider` を使用するカスタム ルート属性</span><span class="sxs-lookup"><span data-stu-id="8d9aa-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="8d9aa-783">フレームワークで提供されるすべてのルート属性 (`[Route(...)]`、`[HttpGet(...)]` など) は、`IRouteTemplateProvider` インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="8d9aa-784">アプリが起動し、`IRouteTemplateProvider` を実装している属性を使ってルートの初期セットを作成すると、MVC はコントローラー クラスとアクション メソッドで属性を検索します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="8d9aa-785">`IRouteTemplateProvider` を実装して、独自のルート属性を定義できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="8d9aa-786">各 `IRouteTemplateProvider` では、カスタム ルート テンプレート、順序、名前を使って 1 つのルートを定義できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="8d9aa-787">上の例の属性は、`[MyApiController]` が適用されると、自動的に `Template` を `"api/[controller]"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="8d9aa-788">アプリケーション モデルを使用した属性ルートのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="8d9aa-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="8d9aa-789">"*アプリケーション モデル*" は、アクションをルーティングおよび実行するために MVC によって使われるすべてのメタデータで起動時に作成されるオブジェクト モデルです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="8d9aa-790">"*アプリケーション モデル*" には、(`IRouteTemplateProvider` によって) ルート属性から収集されるすべてのデータが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="8d9aa-791">起動時にアプリケーション モデルを変更してルーティングの動作方法をカスタマイズする "*規則*" を作成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="8d9aa-792">このセクションでは、アプリケーション モデルを使ってルーティングをカスタマイズする簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="8d9aa-793">混合ルーティング: 属性ルーティングと規則ルーティング</span><span class="sxs-lookup"><span data-stu-id="8d9aa-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="8d9aa-794">MVC アプリケーションでは、規則ルーティングと属性ルーティングを併用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="8d9aa-795">一般に、ブラウザーに HTML ページを提供するコントローラーには規則ルートを使い、REST API を提供するコントローラーには属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="8d9aa-796">アクションは、規則的にルーティングされるか、または属性でルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="8d9aa-797">コントローラーまたはアクションにルートを配置すると、そのルートは属性ルーティングされるようになります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="8d9aa-798">属性ルートが定義されているアクションには規則ルートでは到達できず、規則ルートが定義されているアクションには属性ルートでは到達できません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="8d9aa-799">コントローラーの**任意**のルート属性により、コントローラー属性のすべてのアクションがルーティングされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-800">2 種類のルーティング システムを区別しているのは、URL がルート テンプレートと一致した後に適用されるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="8d9aa-801">規則ルーティングでは、一致のルート値を使って、規則ルーティングされるすべてのアクションの参照テーブルから、アクションとコントローラーが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="8d9aa-802">属性ルーティングでは、各テンプレートはアクションと既に関連付けられており、それ以上参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="8d9aa-803">複雑なセグメント</span><span class="sxs-lookup"><span data-stu-id="8d9aa-803">Complex segments</span></span>

<span data-ttu-id="8d9aa-804">複雑なセグメント (たとえば、`[Route("/dog{token}cat")]`) は、右から左にリテラルを最短の方法で照合することによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="8d9aa-805">説明については、[ソース コード](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="8d9aa-806">詳しくは、[こちらの懸案事項](https://github.com/dotnet/AspNetCore.Docs/issues/8197)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="8d9aa-807">URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-807">URL Generation</span></span>

<span data-ttu-id="8d9aa-808">MVC アプリケーションでは、ルーティングの URL 生成機能を使って、アクションへの URL リンクを生成できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="8d9aa-809">URL を生成すると URL をハードコーディングする必要がなくなり、コードの堅牢性と保守性が向上します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="8d9aa-810">このセクションでは、MVC によって提供される URL 生成機能について説明します。URL 生成のしくみに関する基本だけを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="8d9aa-811">URL の生成について詳しくは、「[ルーティング](xref:fundamentals/routing)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="8d9aa-812">`IUrlHelper` インターフェイスは、MVC と URL 生成のルーティングの間にあるインフラストラクチャの基盤部分です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="8d9aa-813">使用可能な `IUrlHelper` のインスタンスは、コントローラー、ビュー、およびビュー コンポーネントの `Url` プロパティを使って探します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="8d9aa-814">この例の `IUrlHelper` インターフェイスは、別のアクションへの URL を生成するために `Controller.Url` プロパティを介して使われています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-815">アプリケーションが既定の規則ルートを使っている場合、`url` 変数の値は URL パス文字列 `/UrlGeneration/Destination` になります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="8d9aa-816">この URL パスは、ルーティングにより、現在の要求からのルート値 (アンビエント値) と、`Url.Action` に渡された値を結合し、これらの値でルート テンプレートを置換することによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="8d9aa-817">ルート テンプレートの各ルート パラメーターの値は、一致する名前と値およびアンビエント値によって置換されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="8d9aa-818">値を持たないルート パラメーターは、既定値がある場合はそれを使うことができ、省略可能な場合はスキップできます (この例の `id` の場合と同様)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="8d9aa-819">必須のルート パラメーターに対応する値がない場合、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="8d9aa-820">ルートの URL 生成が失敗した場合、すべてのルートが試されるまで、または一致が見つかるまで、次のルートが試されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="8d9aa-821">上の `Url.Action` の例では規則ルーティングを想定しています。URL 生成は属性ルーティングでも同じように動作しますが、概念は異なります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="8d9aa-822">規則ルーティングでは、ルート値を使ってテンプレートが展開され、通常、`controller` と `action` のルートがそのテンプレートに表示されます。これは、ルーティングによって一致した URL が "*規則*" に従うために動作します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="8d9aa-823">属性ルーティングでは、`controller` と `action` のルート値はテンプレートに表示できません。代わりに、使うテンプレートの検索に使われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="8d9aa-824">この例では、属性ルーティングを使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="8d9aa-825">MVC はすべての属性ルーティングされるアクションの参照テーブルを作成し、`controller` と `action` の値で照合して、URL 生成に使うルート テンプレートを選びます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="8d9aa-826">上の例では、`custom/url/to/destination` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="8d9aa-827">アクション名による URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-827">Generating URLs by action name</span></span>

<span data-ttu-id="8d9aa-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="8d9aa-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="8d9aa-829">`Action`) およびすべての関連するオーバーロードは、コントローラー名とアクション名を指定することによってリンク先を指定するアイデアに基づきます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-830">`Url.Action` を使うと、`controller` および `action` の現在のルート値が自動的に指定されます。`controller` および `action` の値は、"\*アンビエント値" \* **と "** *値*" 両方の一部です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="8d9aa-831">`Url.Action` メソッドは、常に `action` と `controller` の現在の値は使い、現在のアクションにルーティングする URL パスを生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="8d9aa-832">ルーティングは、ユーザーが URL 生成時に指定しなかった情報を、アンビエント値を使って埋めようとします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="8d9aa-833">`{a}/{b}/{c}/{d}` やアンビエント値 `{ a = Alice, b = Bob, c = Carol, d = David }` のようなルートを使うと、すべてのルート パラメーターが値を持っているため、ルーティングには URL を生成するための十分な情報があり、追加の値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="8d9aa-834">値 `{ d = Donovan }` を追加した場合は、値 `{ d = David }` は無視されて、生成される URL パスは `Alice/Bob/Carol/Donovan` になります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="8d9aa-835">URL パスは階層的です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-835">URL paths are hierarchical.</span></span> <span data-ttu-id="8d9aa-836">上の例で、値 `{ c = Cheryl }` を追加した場合は、両方の値 `{ c = Carol, d = David }` が無視されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="8d9aa-837">この場合、`d` には値がなくなり、URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="8d9aa-838">`c` および `d` の目的の値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="8d9aa-839">既定のルート (`{controller}/{action}/{id?}`) でもこの問題が発生すると思われるかもしれませんが、`Url.Action` が常に `controller` と `action` の値を明示的に指定するので、実際には、このような動作が発生することはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="8d9aa-840">`Url.Action` の長いオーバーロードも、追加の "*ルート値*" オブジェクトを受け取って、`controller` および `action` 以外のルート パラメーターの値を提供します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="8d9aa-841">これを最もよく目にするのは、`Url.Action("Buy", "Products", new { id = 17 })` のように `id` で使われる場合です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="8d9aa-842">慣例では、"*ルート値*" オブジェクトは通常は匿名型のオブジェクトですが、`IDictionary<>` または "*単純な従来の .NET オブジェクト*" を使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="8d9aa-843">ルート パラメーターと一致しないすべての追加ルート値は、クエリ文字列に格納されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="8d9aa-844">絶対 URL を作成するには、`protocol` を受け取るオーバーロードを使います。`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="8d9aa-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="8d9aa-845">ルートによる URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-845">Generating URLs by route</span></span>

<span data-ttu-id="8d9aa-846">上記のコードでは、コントローラーとアクション名を渡すことによる URL の生成を示しました。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="8d9aa-847">`IUrlHelper` では、`Url.RouteUrl` ファミリ メソッドも提供されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="8d9aa-848">これらのメソッドは、`Url.Action` と似ていますが、`action` および `controller` の現在の値をルート値にコピーしません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="8d9aa-849">最も一般的な使用方法は、ルート名を指定して特定のルートを使って URL を生成する場合です。通常、コントローラーまたはアクション名は指定 "*しません*"。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="8d9aa-850">HTML での URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-850">Generating URLs in HTML</span></span>

<span data-ttu-id="8d9aa-851">`IHtmlHelper` で提供されている `HtmlHelper` メソッドの `Html.BeginForm` および `Html.ActionLink` は、それぞれ `<form>` 要素と `<a>` 要素を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="8d9aa-852">これらのメソッドは `Url.Action` メソッドを使って URL を生成するので、同じような引数を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="8d9aa-853">`HtmlHelper` の `Url.RouteUrl` コンパニオンは、同様の機能を持つ `Html.BeginRouteForm` と `Html.RouteLink` です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="8d9aa-854">TagHelper は、`form` TagHelper と `<a>` TagHelper を使って URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="8d9aa-855">これらはどちらも、実装に `IUrlHelper` を使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="8d9aa-856">詳しくは、「[フォームの操作](../views/working-with-forms.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="8d9aa-857">ビューの内部では、`Url` プロパティを使って任意のアドホック URL 生成に `IUrlHelper` を使うことができます (上の記事では説明されていません)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="8d9aa-858">アクションの結果での URL の生成</span><span class="sxs-lookup"><span data-stu-id="8d9aa-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="8d9aa-859">上の例ではコントローラーでの `IUrlHelper` の使用が示されていますが、コントローラーでの最も一般的な使用方法はアクションの結果の一部として URL を生成することです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="8d9aa-860">基底クラス `ControllerBase` および `Controller` では、別のアクションを参照するアクション結果用の便利なメソッドが提供されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="8d9aa-861">一般的な使用法の 1 つは、ユーザー入力を受け付けた後でリダイレクトする場合です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-861">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="8d9aa-862">アクション結果ファクトリ メソッドは、`IUrlHelper` のメソッドに似たパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="8d9aa-863">専用規則ルートの特殊なケース</span><span class="sxs-lookup"><span data-stu-id="8d9aa-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="8d9aa-864">規則ルーティングでは、"*専用規則ルート*" と呼ばれる特殊なルート定義を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="8d9aa-865">次の例の `blog` という名前のルートが専用規則ルートです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="8d9aa-866">これらのルート定義を使うと、`Url.Action("Index", "Home")` は `default` ルートで URL パス `/` を生成します。なぜでしょうか。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="8d9aa-867">ルート値 `{ controller = Home, action = Index }` は `blog` を使って URL を生成するのに十分であり、結果は `/blog?action=Index&controller=Home` になるように思われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="8d9aa-868">専用規則ルートは、URL の生成でルートの "一致範囲が広くなりすぎる" のを防ぐために対応するルート パラメーターを持たない既定値の特別な動作に依存します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="8d9aa-869">この場合、既定値は `{ controller = Blog, action = Article }` であり、`controller` と `action` はどちらもルート パラメーターとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="8d9aa-870">ルーティングが URL 生成を実行するとき、指定された値は既定値と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="8d9aa-871">値 `{ controller = Home, action = Index }` は `{ controller = Blog, action = Article }` と一致しないため、`blog` を使った URL の生成は失敗します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="8d9aa-872">そして、ルーティングはフォールバックして `default` を試み、これは成功します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="8d9aa-873">Areas</span><span class="sxs-lookup"><span data-stu-id="8d9aa-873">Areas</span></span>

<span data-ttu-id="8d9aa-874">[区分](areas.md)は MVC の機能であり、関連する機能を独立したルーティング名前空間 (コントローラー アクションの場合) およびフォルダー構造 (ビューの場合) としてグループ化するために使われます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="8d9aa-875">区分を使うと、アプリケーションは同じ名前の複数のコントローラーを持つことができます (ただし、"*区分*" が異なる場合)。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="8d9aa-876">区分を使い、別のルート パラメーター `area` を `controller` および `action` に追加することで、ルーティングのための階層を作成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="8d9aa-877">このセクションでは、ルーティングと区分がどのように相互作用するのかについて説明します。ビューでの区分の使い方について詳しくは、「[区分](areas.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="8d9aa-878">次の例では、既定の規則ルートと、`Blog` という名前の区分に対する "*区分ルート*" を使うように、MVC を構成しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="8d9aa-879">`/Manage/Users/AddUser` のような URL パスを照合すると、最初のルートではルート値 `{ area = Blog, controller = Users, action = AddUser }` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="8d9aa-880">`area` ルート値は、`area` の既定値によって生成されます。実際には、`MapAreaRoute` によって作成されるルートは以下と同等です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="8d9aa-881">`MapAreaRoute` は、指定された区分名 (この場合は`Blog`) を使い、既定値と、`area` に対する制約の両方からルートを作成します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="8d9aa-882">既定値はルートが常に `{ area = Blog, ... }` を生成することを保証し、制約では URL を生成するために値 `{ area = Blog, ... }` が必要です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="8d9aa-883">規則ルーティングは順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="8d9aa-884">一般に、区分のあるルートは区分を持たないルートより具体的なので、区分のあるルートはルート テーブルの前の方に配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="8d9aa-885">上の例を使うと、ルート値は次のアクションと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="8d9aa-886">`AreaAttribute` はコントローラーが区分の一部であることを示すものであり、このコントローラーは `Blog` 区分に含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="8d9aa-887">`[Area]` 属性を持たないコントローラーはどの区域のメンバーでもなく、`area` ルート値がルーティングによって提供されたときは一致**しません**。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="8d9aa-888">次の例では、リストの最初のコントローラーだけがルート値 `{ area = Blog, controller = Users, action = AddUser }` と一致できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="8d9aa-889">ここでは完全を期すために各コントローラーの名前空間を示してあります。そうしないと、コントローラーの名前が競合し、コンパイラ エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="8d9aa-890">クラスの名前空間は、MVC のルーティングに対して影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="8d9aa-891">最初の 2 つのコントローラーは区分のメンバーであり、それぞれの区分名が `area` ルート値によって提供された場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="8d9aa-892">3 番目のコントローラーはどの区分のメンバーでもなく、ルーティングによって `area` の値が提供されない場合にのみ一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-893">"*値なし*" との一致では、`area` の値がないことは、`area` の値が null または空の文字列であることと同じです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="8d9aa-894">区分の内部でアクションを実行するとき、`area` のルート値は、URL の生成に使うルーティングの "*アンビエント値*" として利用できます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="8d9aa-895">つまり、既定では、次の例で示すように、区分は URL の生成に対する "*付箋*" として機能します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="8d9aa-896">IActionConstraint について</span><span class="sxs-lookup"><span data-stu-id="8d9aa-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="8d9aa-897">このセクションでは、フレームワークの内部構造と MVC が実行するアクションを選ぶ方法について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="8d9aa-898">一般的なアプリケーションでは、カスタム `IActionConstraint` は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="8d9aa-899">インターフェイスにあまり詳しくない場合でも、既に `IActionConstraint` を使ったことがあるはずです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="8d9aa-900">`[HttpGet]` 属性およびそれに似た `[Http-VERB]` 属性は、アクション メソッドの実行を制限するために `IActionConstraint` を実装しています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="8d9aa-901">既定の規則ルートでは、URL パス `/Products/Edit` は値 `{ controller = Products, action = Edit }` を生成し、ここで示す**両方**のアクションと一致するものとします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="8d9aa-902">`IActionConstraint` ではこれを、両方のアクションが候補と見なされる、といいます。どちらもルート データと一致するからです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="8d9aa-903">`HttpGetAttribute` は実行すると、*Edit()* は *GET* に対して一致し、他の HTTP 動詞には一致しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="8d9aa-904">`Edit(...)` アクションには制約が定義されていないので、すべての HTTP 動詞と一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="8d9aa-905">したがって、`POST` は `Edit(...)` のみと一致します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="8d9aa-906">一方、`GET` の場合は両方のアクションが一致します。しかし、`IActionConstraint` を指定されているアクションの方が常に、指定されていないアクション "*より適切*" であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="8d9aa-907">したがって、`Edit()` には `[HttpGet]` があるので、より具体的と見なされ、両方のアクションが一致する場合はこちらが選ばれます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="8d9aa-908">概念的には、`IActionConstraint` は "*オーバーロード*" の形式ですが、同じ名前のメソッドをオーバーロードするのではなく、同じ URL に一致するアクション間でオーバーロードします。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="8d9aa-909">属性ルーティングも `IActionConstraint` を使い、異なるコントローラーのアクションがどちらも候補と見なされる結果になることがあります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="8d9aa-910">IActionConstraint の実装</span><span class="sxs-lookup"><span data-stu-id="8d9aa-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="8d9aa-911">`IActionConstraint` を実装する最も簡単な方法は、`System.Attribute` の派生クラスを作成し、アクションとコントローラーに配置する方法です。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="8d9aa-912">MVC は、属性として適用された `IActionConstraint` を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="8d9aa-913">アプリケーション モデルを使って制約を適用することができ、適用方法をメタプログラムできるので、おそらくこれが最も柔軟なアプローチです。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="8d9aa-914">次の例では、制約は、ルートデータから*国コード*に基づいてアクションを選択します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="8d9aa-915">[GitHub の完全なサンプルはこちら](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="8d9aa-916">ユーザーは、`Accept` メソッドを実装し、実行する制約の "順序" を選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="8d9aa-917">この例では、`country` ルート値が一致すると、`Accept` メソッドは `true` を返してアクションが一致することを示します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="8d9aa-918">この動作は、非属性アクションにフォールバックできる `RouteValueAttribute` とは異なります。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="8d9aa-919">サンプルでは、`en-US` アクションを定義すると、`fr-FR` のような国コードは `[CountrySpecific(...)]` が適用されていない、より一般的なコントローラーにフォールバックすることが示されています。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="8d9aa-920">`Order` プロパティは、制約が含まれる "*ステージ*" を決定します。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="8d9aa-921">アクションの制約は、`Order` に基づいてグループで実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="8d9aa-922">たとえば、フレームワークで提供されるすべての HTTP メソッド属性は、同じステージで実行されるように、同じ `Order` 値を使います。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="8d9aa-923">目的のポリシーを実装するため、必要なだけいくつでもステージを使うことができます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="8d9aa-924">`Order` の値を決めるときは、HTTP メソッドより前に制約を適用する必要があるかどうかを考えます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="8d9aa-925">小さい値が先に実行されます。</span><span class="sxs-lookup"><span data-stu-id="8d9aa-925">Lower numbers run first.</span></span>

::: moniker-end
