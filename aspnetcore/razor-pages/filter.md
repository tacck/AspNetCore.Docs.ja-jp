---
title: ASP.NET Core の Razor Pages のフィルター メソッド
author: Rick-Anderson
description: ASP.NET Core の Razor Pages のフィルター メソッドを作成する方法を学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: 68962d5a3a49e52510d72899e7dead2c1983d8b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775519"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="206c6-103">ASP.NET Core の Razor Pages のフィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="206c6-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="206c6-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="206c6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="206c6-105"> ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor Pages でコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-105"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="206c6-106"> ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="206c6-106"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="206c6-107"> ページ フィルター</span><span class="sxs-lookup"><span data-stu-id="206c6-107"> Page filters:</span></span>

* <span data-ttu-id="206c6-108">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="206c6-109">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="206c6-110">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="206c6-111">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="206c6-112">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="206c6-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="206c6-113">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="206c6-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="206c6-114">詳細については、「[ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute)」と「[TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="206c6-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="206c6-115">ページ コンストラクターとミドルウェアにより、ハンドラー メソッドが実行される前にカスタム コードの実行が可能になりますが、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> とページへのアクセスを可能にするのは Razor ページ フィルターのみです。</span><span class="sxs-lookup"><span data-stu-id="206c6-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="206c6-116">ミドルウェアは `HttpContext` にアクセスできますが、"ページ コンテキスト" にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="206c6-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="206c6-117">フィルターには、`HttpContext` へのアクセスを提供する派生型のパラメーター <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> があります。</span><span class="sxs-lookup"><span data-stu-id="206c6-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="206c6-118">たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="206c6-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="206c6-119">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="206c6-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="206c6-120"> ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="206c6-120"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="206c6-121">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="206c6-121">Synchronous methods:</span></span>

  * <span data-ttu-id="206c6-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="206c6-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0):ハンドラー メソッドが実行される前の、モデル バインドが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="206c6-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0):ハンドラー メソッドが実行された後の、アクションの結果の前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="206c6-125">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="206c6-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="206c6-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="206c6-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0):ハンドラー メソッドが呼び出される前の、モデル バインドの完了後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="206c6-128">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="206c6-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="206c6-129">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="206c6-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="206c6-130">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="206c6-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="206c6-131">両方のインターフェイスを実装した場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="206c6-132">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="206c6-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="206c6-133">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="206c6-134">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="206c6-135">上記のコードでは、`ProcessUserAgent.Write` が、ユーザー エージェント文字列を使用して機能するユーザー指定のコードです。</span><span class="sxs-lookup"><span data-stu-id="206c6-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="206c6-136">次のコードは、`Startup` クラスで `SampleAsyncPageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="206c6-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="206c6-137">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を呼び出して、 */Movies* 内のページにのみ `SampleAsyncPageFilter` を適用しています。</span><span class="sxs-lookup"><span data-stu-id="206c6-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="206c6-138">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="206c6-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="206c6-139">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="206c6-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="206c6-140">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="206c6-141">次のコードでは、非同期 Razor ページ フィルターをオーバーライドしています。</span><span class="sxs-lookup"><span data-stu-id="206c6-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="206c6-142">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-142">Implement a filter attribute</span></span>

<span data-ttu-id="206c6-143">組み込みの属性ベースのフィルターである <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> フィルターは、サブクラス化できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="206c6-144">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="206c6-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="206c6-145">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="206c6-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="206c6-146">ブラウザー開発者ツールなどのツールを使用して、ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="206c6-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="206c6-147">**[応答ヘッダー]** の下に `author: Rick` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="206c6-148">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="206c6-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="206c6-149">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="206c6-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="206c6-150">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="206c6-150">Authorize filter attribute</span></span>

<span data-ttu-id="206c6-151">`PageModel` に [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="206c6-152">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="206c6-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="206c6-153"> ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor Pages でコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-153"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="206c6-154"> ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="206c6-154"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="206c6-155"> ページ フィルター</span><span class="sxs-lookup"><span data-stu-id="206c6-155"> Page filters:</span></span>

* <span data-ttu-id="206c6-156">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="206c6-157">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="206c6-158">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="206c6-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="206c6-159">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="206c6-160">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="206c6-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="206c6-161">コードは、ページ コンストラクターまたはミドルウェアを使用してハンドラー メソッドの実行前に実行できますが、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext) にアクセスできるのは Razor ページ フィルターのみです。</span><span class="sxs-lookup"><span data-stu-id="206c6-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="206c6-162">フィルターには、`HttpContext` へのアクセスを提供する [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生のパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="206c6-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="206c6-163">たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="206c6-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="206c6-164">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="206c6-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="206c6-165"> ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="206c6-165"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="206c6-166">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="206c6-166">Synchronous methods:</span></span>

  * <span data-ttu-id="206c6-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="206c6-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0):ハンドラー メソッドが実行される前の、モデル バインドが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="206c6-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0):ハンドラー メソッドが実行された後の、アクションの結果の前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="206c6-170">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="206c6-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="206c6-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="206c6-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0):ハンドラー メソッドが呼び出される前の、モデル バインドの完了後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="206c6-173">フィルター インターフェイスの同期と非同期バージョンの両方ではなく、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="206c6-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="206c6-174">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="206c6-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="206c6-175">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="206c6-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="206c6-176">両方のインターフェイスを実装した場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="206c6-177">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="206c6-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="206c6-178">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="206c6-179">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="206c6-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="206c6-180">前述のコードでは、[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) は不要です。</span><span class="sxs-lookup"><span data-stu-id="206c6-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="206c6-181">これは、アプリケーションのトレース情報を提供するためにサンプルで使用されています。</span><span class="sxs-lookup"><span data-stu-id="206c6-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="206c6-182">次のコードは、`Startup` クラスで `SampleAsyncPageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="206c6-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="206c6-183">次は、完全な `Startup` クラスのコードです。</span><span class="sxs-lookup"><span data-stu-id="206c6-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="206c6-184">次のコードは、`AddFolderApplicationModelConvention` を呼び出し、 */subFolder* のページにのみ `SampleAsyncPageFilter` を適用します。</span><span class="sxs-lookup"><span data-stu-id="206c6-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="206c6-185">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="206c6-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="206c6-186">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="206c6-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="206c6-187">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="206c6-188">次のコードでは、同期 Razor ページ フィルターをオーバーライドしています。</span><span class="sxs-lookup"><span data-stu-id="206c6-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="206c6-189">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="206c6-189">Implement a filter attribute</span></span>

<span data-ttu-id="206c6-190">組み込みの属性ベースのフィルターである [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) フィルターはサブクラス化することができます。</span><span class="sxs-lookup"><span data-stu-id="206c6-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="206c6-191">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="206c6-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="206c6-192">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="206c6-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="206c6-193">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="206c6-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="206c6-194">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="206c6-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="206c6-195">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="206c6-195">Authorize filter attribute</span></span>

<span data-ttu-id="206c6-196">`PageModel` に [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="206c6-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
