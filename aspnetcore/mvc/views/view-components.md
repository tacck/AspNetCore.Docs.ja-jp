---
title: ASP.NET Core のビュー コンポーネント
author: rick-anderson
description: ASP.NET Core でのビュー コンポーネントの使用方法とそれらをアプリに追加する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 61fcc07aeb30db15014fb716194328d366f27859
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058338"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="2e294-103">ASP.NET Core のビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2e294-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="2e294-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e294-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2e294-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2e294-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="2e294-106">ビュー コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2e294-106">View components</span></span>

<span data-ttu-id="2e294-107">ビュー コンポーネントは部分ビューと似ていますが、はるかに強力なものです。</span><span class="sxs-lookup"><span data-stu-id="2e294-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="2e294-108">ビュー コンポーネントでは、モデル バインドを使用せず、呼び出すときに指定されたデータのみに依存します。</span><span class="sxs-lookup"><span data-stu-id="2e294-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="2e294-109">この記事はコントローラーとビューを使用して記述されていますが、ビューコンポーネントはページでも機能し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="2e294-110">ビュー コンポーネントの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e294-110">A view component:</span></span>

* <span data-ttu-id="2e294-111">応答全体ではなく、チャンクをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="2e294-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="2e294-112">コントローラーとビューの間にあるのと同じ関心の分離とテストの容易性の利点があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="2e294-113">パラメーターとビジネス ロジックを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="2e294-114">通常、レイアウト ページから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="2e294-115">ビュー コンポーネントは、次のような部分ビューには複雑すぎる、再利用可能なレンダリング ロジックをどこでも使用できるようにするためのものです。</span><span class="sxs-lookup"><span data-stu-id="2e294-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="2e294-116">動的なナビゲーション メニュー</span><span class="sxs-lookup"><span data-stu-id="2e294-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="2e294-117">タグ クラウド (データベースをクエリする場所)</span><span class="sxs-lookup"><span data-stu-id="2e294-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="2e294-118">ログイン パネル</span><span class="sxs-lookup"><span data-stu-id="2e294-118">Login panel</span></span>
* <span data-ttu-id="2e294-119">ショッピング カート</span><span class="sxs-lookup"><span data-stu-id="2e294-119">Shopping cart</span></span>
* <span data-ttu-id="2e294-120">新着情報の記事</span><span class="sxs-lookup"><span data-stu-id="2e294-120">Recently published articles</span></span>
* <span data-ttu-id="2e294-121">一般的なブログのサイドバーのコンテンツ</span><span class="sxs-lookup"><span data-stu-id="2e294-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="2e294-122">すべてのページでレンダリングされ、ユーザーの状態のログに応じて、ログアウトまたはログインのいずれかのリンクを示すログイン パネル</span><span class="sxs-lookup"><span data-stu-id="2e294-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="2e294-123">ビュー コンポーネントは、次の 2 つのパーツで構成されます。クラス (通常、[ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) から派生) と、クラスで返される結果 (通常はビュー) です。</span><span class="sxs-lookup"><span data-stu-id="2e294-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="2e294-124">コントローラーと同様に、ビュー コンポーネントは POCO の場合がありますが、ほとんどの開発者は `ViewComponent` から派生させて、利用できるメソッドとプロパティを活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="2e294-125">ビューコンポーネントがアプリの仕様を満たしているかどうかを検討する場合は、代わりにコンポーネントを使用することを検討してください Razor 。</span><span class="sxs-lookup"><span data-stu-id="2e294-125">When considering if view components meet an app's specifications, consider using Razor components instead.</span></span> <span data-ttu-id="2e294-126">Razor コンポーネントでは、マークアップと C# コードを組み合わせて、再利用可能な UI ユニットを生成することもできます。</span><span class="sxs-lookup"><span data-stu-id="2e294-126">Razor components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="2e294-127">Razor コンポーネントは、クライアント側の UI ロジックとコンポジションを提供するときに、開発者の生産性を高めるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="2e294-127">Razor components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="2e294-128">詳細については、「<xref:blazor/components/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e294-128">For more information, see <xref:blazor/components/index>.</span></span> <span data-ttu-id="2e294-129">Razorコンポーネントを MVC またはページアプリに組み込む方法については Razor 、「」を参照してください <xref:blazor/components/prerendering-and-integration?pivots=server> 。</span><span class="sxs-lookup"><span data-stu-id="2e294-129">For information on how to incorporate Razor components into an MVC or Razor Pages app, see <xref:blazor/components/prerendering-and-integration?pivots=server>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="2e294-130">ビューのコンポーネントを作成する</span><span class="sxs-lookup"><span data-stu-id="2e294-130">Creating a view component</span></span>

<span data-ttu-id="2e294-131">このセクションには、ビュー コンポーネントを作成するための高レベルの要件が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e294-131">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="2e294-132">記事の後半で、各ステップの詳細を検証し、ビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e294-132">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="2e294-133">ビュー コンポーネント クラス</span><span class="sxs-lookup"><span data-stu-id="2e294-133">The view component class</span></span>

<span data-ttu-id="2e294-134">ビュー コンポーネント クラスは、次のいずれかによって作成できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-134">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="2e294-135">*ViewComponent* から派生させる</span><span class="sxs-lookup"><span data-stu-id="2e294-135">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="2e294-136">`[ViewComponent]` 属性でクラスを装飾するか、`[ViewComponent]` 属性でクラスから派生させる</span><span class="sxs-lookup"><span data-stu-id="2e294-136">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="2e294-137">名前がサフィックス *ViewComponent* で終わるクラスを作成する</span><span class="sxs-lookup"><span data-stu-id="2e294-137">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="2e294-138">コントローラーと同様に、ビュー コンポーネントは、パブリック クラス、入れ子にされていないクラス、および非抽象クラスである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-138">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="2e294-139">ビュー コンポーネント名は、"ViewComponent" サフィックスを除いたクラス名です。</span><span class="sxs-lookup"><span data-stu-id="2e294-139">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="2e294-140">また、これは `ViewComponentAttribute.Name` プロパティを使用して、明示的に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="2e294-140">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="2e294-141">ビュー コンポーネント クラスの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e294-141">A view component class:</span></span>

* <span data-ttu-id="2e294-142">コンストラクターの[依存性の注入](../../fundamentals/dependency-injection.md)を完全にサポートします</span><span class="sxs-lookup"><span data-stu-id="2e294-142">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="2e294-143">コントローラーのライフサイクルに関わりません。つまり、ビュー コンポーネントで[フィルター](../controllers/filters.md)を使用できないということです</span><span class="sxs-lookup"><span data-stu-id="2e294-143">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="2e294-144">ビュー コンポーネント メソッド</span><span class="sxs-lookup"><span data-stu-id="2e294-144">View component methods</span></span>

<span data-ttu-id="2e294-145">ビュー コンポーネントでは、`Task<IViewComponentResult>` を返す `InvokeAsync` メソッドまたは `IViewComponentResult` を返す同期 `Invoke` メソッドでロジックを定義します。</span><span class="sxs-lookup"><span data-stu-id="2e294-145">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="2e294-146">パラメーターは、モデル バインドではなく、ビュー コンポーネントから直接取得します。</span><span class="sxs-lookup"><span data-stu-id="2e294-146">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="2e294-147">ビュー コンポーネントが要求を直接処理することはありません。</span><span class="sxs-lookup"><span data-stu-id="2e294-147">A view component never directly handles a request.</span></span> <span data-ttu-id="2e294-148">通常、ビュー コンポーネントは、モデルを初期化し、`View` メソッドを呼び出してビューに渡します。</span><span class="sxs-lookup"><span data-stu-id="2e294-148">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="2e294-149">要約すると、ビュー コンポーネント メソッドの特徴は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e294-149">In summary, view component methods:</span></span>

* <span data-ttu-id="2e294-150">`Task<IViewComponentResult>` を返す `InvokeAsync` メソッドまたは `IViewComponentResult` を返す同期 `Invoke` メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="2e294-150">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="2e294-151">通常、モデルを初期化し、メソッドを呼び出してビューに渡し `ViewComponent` `View` ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-151">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="2e294-152">パラメーターは HTTP ではなく、呼び出し元のメソッドから取得されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-152">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="2e294-153">モデル バインドはありません。</span><span class="sxs-lookup"><span data-stu-id="2e294-153">There's no model binding.</span></span>
* <span data-ttu-id="2e294-154">HTTP エンドポイントとして直接到達することはできません。</span><span class="sxs-lookup"><span data-stu-id="2e294-154">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="2e294-155">(通常はビュー内で) コードから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-155">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="2e294-156">ビュー コンポーネントでは要求が処理されません。</span><span class="sxs-lookup"><span data-stu-id="2e294-156">A view component never handles a request.</span></span>
* <span data-ttu-id="2e294-157">現在の HTTP 要求からの詳細ではなく、シグネチャでオーバーロードされます。</span><span class="sxs-lookup"><span data-stu-id="2e294-157">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="2e294-158">ビューの検索パス</span><span class="sxs-lookup"><span data-stu-id="2e294-158">View search path</span></span>

<span data-ttu-id="2e294-159">ランタイムでは、次のパスでビューを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e294-159">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="2e294-160">/Views/{コントローラー名}/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="2e294-160">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="2e294-161">/Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="2e294-161">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="2e294-162">/Pages/Shared/Components/{ビュー コンポーネント名}/{ビュー名}</span><span class="sxs-lookup"><span data-stu-id="2e294-162">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="2e294-163">検索パスは、コントローラー + ビューおよびページを使用してプロジェクトに適用され Razor ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-163">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="2e294-164">ビュー コンポーネントの既定のビュー名は、*Default* です。つまり、通常、ビュー ファイルは *Default.cshtml* という名前になるということです。</span><span class="sxs-lookup"><span data-stu-id="2e294-164">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="2e294-165">ビュー コンポーネントの結果を作成したり、`View` メソッドを呼び出したりするときに、別のビュー名を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-165">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="2e294-166">ビュー ファイルに *Default.cshtml* という名前を付けて、"*Views/Shared/Components/{ビュー コンポーネント名}/{ビュー名}*" というパスを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e294-166">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="2e294-167">このサンプルで使用される `PriorityList` ビュー コンポーネントは、ビュー コンポーネント ビューに *Views/Shared/Components/PriorityList/Default.cshtml* を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e294-167">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="2e294-168">ビューの検索パスをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="2e294-168">Customize the view search path</span></span>

<span data-ttu-id="2e294-169">ビューの検索パスをカスタマイズするには、のコレクションを変更し Razor <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-169">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="2e294-170">たとえば、"/Components/{ビュー コンポーネント名}/{ビュー名}" というパス内のビューを検索するには、次のように新しい項目をコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e294-170">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="2e294-171">上記のコードでは、プレースホルダー "{0}" はパス "/Components/{ビュー コンポーネント名}/{ビュー名}" を表しています。</span><span class="sxs-lookup"><span data-stu-id="2e294-171">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="2e294-172">ビュー コンポーネントを呼び出す</span><span class="sxs-lookup"><span data-stu-id="2e294-172">Invoking a view component</span></span>

<span data-ttu-id="2e294-173">ビュー コンポーネントを使用するには、ビュー内で以下を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2e294-173">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="2e294-174">パラメーターは、`InvokeAsync` メソッドに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-174">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="2e294-175">`PriorityList`記事で開発したビューコンポーネントは、 *Views/ToDo/Index. cshtml* ビューファイルから呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-175">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="2e294-176">以下では、`InvokeAsync` メソッドは、2 つのパラメーターで呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-176">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="2e294-177">タグ ヘルパーとしてビュー コンポーネントを呼び出す</span><span class="sxs-lookup"><span data-stu-id="2e294-177">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="2e294-178">ASP.NET Core 1.1 以降の場合は、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)としてビュー コンポーネントを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-178">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="2e294-179">タグ ヘルパーのパスカル ケースのクラスとメソッドのパラメーターは、それぞれ[ケバブ ケース](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)に変換されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-179">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="2e294-180">ビュー コンポーネントを呼び出すタグ ヘルパーでは、`<vc></vc>` 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e294-180">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="2e294-181">ビュー コンポーネントは、次のように指定されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-181">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="2e294-182">ビュー コンポーネントをタグ ヘルパーとして使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します。</span><span class="sxs-lookup"><span data-stu-id="2e294-182">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="2e294-183">ビュー コンポーネントが `MyWebApp` と呼ばれるアセンブリにある場合は、次のディレクティブを *_ViewImports.cshtml* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e294-183">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="2e294-184">ビュー コンポーネントを参照する任意のファイルへのタグ ヘルパーとして、ビュー コンポーネントを登録できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-184">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="2e294-185">タグ ヘルパーを登録する方法の詳細については、「[Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)」 (タグ ヘルパーのスコープの管理) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e294-185">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="2e294-186">このチュートリアルで使用される `InvokeAsync` メソッドは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2e294-186">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="2e294-187">タグ ヘルパーのマークアップでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2e294-187">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="2e294-188">上記のサンプルでは、`PriorityList` ビュー コンポーネントは `priority-list` になります。</span><span class="sxs-lookup"><span data-stu-id="2e294-188">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="2e294-189">ビュー コンポーネントに対するパラメーターは、ケバブ ケースの属性として渡されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-189">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="2e294-190">ビュー コンポーネントをコントローラーから直接呼び出す</span><span class="sxs-lookup"><span data-stu-id="2e294-190">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="2e294-191">通常、ビュー コンポーネントはビューから呼び出されますが、コントローラー メソッドから直接呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-191">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="2e294-192">ビュー コンポーネントでコントローラーなどのエンドポイントを定義しないときに、`ViewComponentResult` のコンテンツを返すコントローラー アクションを簡単に実装できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-192">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="2e294-193">この例では、ビュー コンポーネントは、コントローラーから直接呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-193">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="2e294-194">チュートリアル: 単純なビュー コンポーネントの作成</span><span class="sxs-lookup"><span data-stu-id="2e294-194">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="2e294-195">スタート コードを[ダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、ビルド、およびテストします。</span><span class="sxs-lookup"><span data-stu-id="2e294-195">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="2e294-196">これは、 `ToDo` *ToDo* 項目の一覧を表示するコントローラーを持つ単純なプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2e294-196">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![[ToDo] のリスト](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="2e294-198">ViewComponent クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2e294-198">Add a ViewComponent class</span></span>

<span data-ttu-id="2e294-199">*ViewComponents* フォルダーを作成して、次の `PriorityListViewComponent` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e294-199">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="2e294-200">コードに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="2e294-200">Notes on the code:</span></span>

* <span data-ttu-id="2e294-201">ビュー コンポーネント クラスは、プロジェクト内の **任意** のフォルダーに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-201">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="2e294-202">クラス名 PriorityList **ViewComponent** は、サフィックス **ViewComponent** で終わるため、ビューからクラス コンポーネントを参照するときに、ランタイムでは文字列 "PriorityList" を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e294-202">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="2e294-203">これについては、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2e294-203">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="2e294-204">`[ViewComponent]` 属性では、ビュー コンポーネントを参照するために使用する名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-204">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="2e294-205">たとえば、クラスに `XYZ` という名前を付けて、`ViewComponent` 属性を適用することができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-205">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="2e294-206">上述の `[ViewComponent]` 属性は、ビュー コンポーネント セレクターに、コンポーネントに関連付けられたビューを探す場合は `PriorityList` という名前を使用し、ビューからクラス コンポーネントを参照する場合は文字列 "PriorityList" を使用するように指示します。</span><span class="sxs-lookup"><span data-stu-id="2e294-206">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="2e294-207">これについては、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2e294-207">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="2e294-208">コンポーネントでは、[依存性の注入](../../fundamentals/dependency-injection.md)を使用して、データ コンテキストを利用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="2e294-208">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="2e294-209">`InvokeAsync` ではビューから呼び出すことができるメソッドを表示し、任意の数の引数を取得できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-209">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="2e294-210">`InvokeAsync` メソッドでは、`isDone` と `maxPriority` パラメーターを満たす `ToDo` 項目のセットを返します。</span><span class="sxs-lookup"><span data-stu-id="2e294-210">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="2e294-211">ビューコンポーネントビューを作成する Razor</span><span class="sxs-lookup"><span data-stu-id="2e294-211">Create the view component Razor view</span></span>

* <span data-ttu-id="2e294-212">*Views/Shared/Components* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e294-212">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="2e294-213">このフォルダーは、*Components* という名前にする **必要があります**。</span><span class="sxs-lookup"><span data-stu-id="2e294-213">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="2e294-214">*Views/Shared/Components/PriorityList* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e294-214">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="2e294-215">このフォルダー名は、ビュー コンポーネント クラスの名前、または (規則に従い、クラス名に *ViewComponent* サフィックスを使用した場合は) サフィックスを差し引いたクラスの名前に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-215">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="2e294-216">`ViewComponent` 属性を使用した場合は、クラス名は属性の指定に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-216">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="2e294-217">Views/ *Shared/Components/優先順位リスト/既定の cshtml* ビューを作成し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-217">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="2e294-218">Razorビューはの一覧を取得し、表示し `TodoItem` ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-218">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="2e294-219">ビュー コンポーネントの `InvokeAsync` メソッドで (サンプルのように) ビューの名前を渡さない場合、*Default* が規則によってビュー名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-219">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="2e294-220">このチュートリアルの後半で、ビューの名前を渡す方法について示します。</span><span class="sxs-lookup"><span data-stu-id="2e294-220">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="2e294-221">特定のコントローラーの既定のスタイルをオーバーライドするには、コントローラー固有のビューフォルダーにビューを追加します (たとえば、 *Views/ToDo/Components/指定リスト/既定値)*。</span><span class="sxs-lookup"><span data-stu-id="2e294-221">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="2e294-222">ビューコンポーネントがコントローラー固有の場合は、コントローラー固有のフォルダーに追加できます (*Views/ToDo/Components/の優先リスト/既定値*)。</span><span class="sxs-lookup"><span data-stu-id="2e294-222">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="2e294-223">`div`Priority list コンポーネントへの呼び出しを含むを *Views/ToDo/index. cshtml* ファイルの一番下に追加します。</span><span class="sxs-lookup"><span data-stu-id="2e294-223">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="2e294-224">`@await Component.InvokeAsync` マークアップは、ビュー コンポーネントを呼び出すための構文を示します。</span><span class="sxs-lookup"><span data-stu-id="2e294-224">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="2e294-225">最初の引数は、呼び出す必要があるコンポーネントの名前です。</span><span class="sxs-lookup"><span data-stu-id="2e294-225">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="2e294-226">後続のパラメーターは、そのコンポーネントに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-226">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="2e294-227">`InvokeAsync` では、任意の数の引数を取得できます。</span><span class="sxs-lookup"><span data-stu-id="2e294-227">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="2e294-228">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e294-228">Test the app.</span></span> <span data-ttu-id="2e294-229">次の画像は、[ToDo] リストと優先順位の項目を示します。</span><span class="sxs-lookup"><span data-stu-id="2e294-229">The following image shows the ToDo list and the priority items:</span></span>

![[ToDo] リストと優先順位の項目](view-components/_static/pi.png)

<span data-ttu-id="2e294-231">また、コントローラーから直接ビュー コンポーネントを呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="2e294-231">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC アクションからの優先順位の項目](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="2e294-233">ビュー名を指定する</span><span class="sxs-lookup"><span data-stu-id="2e294-233">Specifying a view name</span></span>

<span data-ttu-id="2e294-234">複雑なビュー コンポーネントでは、いくつかの条件下で、既定以外のビューを指定する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="2e294-234">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="2e294-235">次のコードでは、`InvokeAsync` メソッドから "PVC" ビューを指定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2e294-235">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="2e294-236">`PriorityListViewComponent` クラスで `InvokeAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e294-236">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="2e294-237">*Views/Shared/Components/PriorityList/Default.cshtml* ファイルを *Views/Shared/Components/PriorityList/PVC.cshtml* という名前のビューにコピーします。</span><span class="sxs-lookup"><span data-stu-id="2e294-237">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="2e294-238">PVC ビューが使用されていることを示すために、見出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e294-238">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="2e294-239">*Views/ToDo/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2e294-239">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="2e294-240">アプリを実行して、PVC ビューを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e294-240">Run the app and verify PVC view.</span></span>

![優先順位のビュー コンポーネント](view-components/_static/pvc.png)

<span data-ttu-id="2e294-242">PVC ビューがレンダリングされない場合は、4 以上の優先順位でビュー コンポーネントを呼び出していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e294-242">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="2e294-243">ビューのパスを調べる</span><span class="sxs-lookup"><span data-stu-id="2e294-243">Examine the view path</span></span>

* <span data-ttu-id="2e294-244">優先順位ビューが返されないように、優先順位パラメーターを 3 以下に変更します。</span><span class="sxs-lookup"><span data-stu-id="2e294-244">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="2e294-245">*Views/ToDo/Components/の優先リスト/既定値* の名前を一時的 *に変更します。*</span><span class="sxs-lookup"><span data-stu-id="2e294-245">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="2e294-246">アプリをテストすると、次のエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-246">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="2e294-247">*Views/ToDo/components/優先順位リスト/1Default. cshtml* を *views/Shared/Components/優先順位リスト/既定の cshtml* にコピーします。</span><span class="sxs-lookup"><span data-stu-id="2e294-247">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="2e294-248">*共有の ToDo ビュー* コンポーネントビューにマークアップを追加して、ビューが *共有* フォルダーからのものであることを示します。</span><span class="sxs-lookup"><span data-stu-id="2e294-248">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="2e294-249">**[Shared]** コンポーネント ビューをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e294-249">Test the **Shared** component view.</span></span>

![[Shared] コンポーネント ビューを含む [ToDo] 出力](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="2e294-251">ハードコーディングされた文字列の回避</span><span class="sxs-lookup"><span data-stu-id="2e294-251">Avoiding hard-coded strings</span></span>

<span data-ttu-id="2e294-252">コンパイル時間の安全性を確保する必要がある場合は、ハードコーディングされたビュー コンポーネント名をクラス名に置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="2e294-252">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="2e294-253">"ViewComponent" サフィックスのないビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e294-253">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="2e294-254">ステートメントを `using` Razor ビューファイルに追加し、演算子を使用し `nameof` ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-254">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="2e294-255">同期作業を実行する</span><span class="sxs-lookup"><span data-stu-id="2e294-255">Perform synchronous work</span></span>

<span data-ttu-id="2e294-256">非同期作業を実行する必要がない場合は、フレームワークで同期 `Invoke` メソッドの呼び出しが処理されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-256">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="2e294-257">次のメソッドでは同期 `Invoke` ビュー コンポーネントを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e294-257">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="2e294-258">ビューコンポーネントのファイルには、 Razor メソッドに渡された文字列が一覧表示され `Invoke` ます (*Views/Home/Components/の設定/既定値は cshtml*)。</span><span class="sxs-lookup"><span data-stu-id="2e294-258">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="2e294-259">ビューコンポーネントは、 Razor 次のいずれかの方法を使用して、ファイル ( *Views/Home/Index. cshtml* など) で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e294-259">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="2e294-260">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="2e294-260">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="2e294-261"><xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> の方法を使用するには、`Component.InvokeAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2e294-261">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="2e294-262">ビューコンポーネントが Razor ファイル (たとえば、 *Views/Home/Index. cshtml*) で呼び出され <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-262">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="2e294-263">`Component.InvokeAsync` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2e294-263">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="2e294-264">タグ ヘルパーを使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録します (ビュー コンポーネントは、`MyWebApp` と呼ばれるアセンブリ内にあります)。</span><span class="sxs-lookup"><span data-stu-id="2e294-264">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="2e294-265">マークアップファイルのビューコンポーネントタグヘルパーを使用し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-265">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="2e294-266">のメソッドシグネチャ `PriorityList.Invoke` は同期ですが、 Razor マークアップファイル内のでメソッドを検索して呼び出し `Component.InvokeAsync` ます。</span><span class="sxs-lookup"><span data-stu-id="2e294-266">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="2e294-267">ビュー コンポーネントのすべてのパラメーターが必要</span><span class="sxs-lookup"><span data-stu-id="2e294-267">All view component parameters are required</span></span>

<span data-ttu-id="2e294-268">ビュー コンポーネントの各パラメーターは、必須の属性です。</span><span class="sxs-lookup"><span data-stu-id="2e294-268">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="2e294-269">[こちらの GitHub のイシュー](https://github.com/dotnet/AspNetCore/issues/5011)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e294-269">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="2e294-270">パラメーターを省略した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="2e294-270">If any  parameter is omitted:</span></span>

* <span data-ttu-id="2e294-271">`InvokeAsync` メソッドのシグネチャが一致しないため、メソッドが実行されません。</span><span class="sxs-lookup"><span data-stu-id="2e294-271">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="2e294-272">ViewComponent がマークアップをレンダリングしません。</span><span class="sxs-lookup"><span data-stu-id="2e294-272">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="2e294-273">エラーがスローされません。</span><span class="sxs-lookup"><span data-stu-id="2e294-273">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e294-274">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2e294-274">Additional resources</span></span>

* [<span data-ttu-id="2e294-275">ビューへの依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="2e294-275">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
