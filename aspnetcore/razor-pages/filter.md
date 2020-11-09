---
title: 'ASP.NET Core の Razor Pages のフィルター メソッド'
author: Rick-Anderson
description: 'ASP.NET Core の Razor Pages のフィルター メソッドを作成する方法を学習します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056830"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="745eb-103">ASP.NET Core の Razor Pages のフィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="745eb-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="745eb-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="745eb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="745eb-105">Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor Pages でコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="745eb-106">Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="745eb-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="745eb-107">Razor ページ フィルター</span><span class="sxs-lookup"><span data-stu-id="745eb-107">Razor Page filters:</span></span>

* <span data-ttu-id="745eb-108">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="745eb-109">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="745eb-110">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="745eb-111">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="745eb-112">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="745eb-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="745eb-113">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されるようにできます。</span><span class="sxs-lookup"><span data-stu-id="745eb-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="745eb-114">詳細については、「[ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute)」と「[TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="745eb-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="745eb-115">ページ コンストラクターとミドルウェアにより、ハンドラー メソッドが実行される前にカスタム コードの実行が可能になりますが、<xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> とページへのアクセスを可能にするのは Razor ページ フィルターのみです。</span><span class="sxs-lookup"><span data-stu-id="745eb-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="745eb-116">ミドルウェアは `HttpContext` にアクセスできますが、"ページ コンテキスト" にはアクセスできません。</span><span class="sxs-lookup"><span data-stu-id="745eb-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="745eb-117">フィルターには、`HttpContext` へのアクセスを提供する派生型のパラメーター <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> があります。</span><span class="sxs-lookup"><span data-stu-id="745eb-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="745eb-118">ページ フィルターの例を次に示します。応答にヘッダーが追加する[フィルター属性を実装する](#ifa)。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="745eb-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="745eb-119">ページとそのモデルのインスタンスへのアクセスを含む、ページ コンテキストへのアクセスは、フィルター、ハンドラー、Razor ページの本文を実行するときにのみ利用できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="745eb-120">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="745eb-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="745eb-121">Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="745eb-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="745eb-122">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="745eb-122">Synchronous methods:</span></span>

  * <span data-ttu-id="745eb-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="745eb-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0):ハンドラー メソッドが実行される前の、モデル バインドが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="745eb-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0):ハンドラー メソッドが実行された後の、アクションの結果の前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="745eb-126">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="745eb-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="745eb-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="745eb-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0):ハンドラー メソッドが呼び出される前の、モデル バインドの完了後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="745eb-129">フィルター インターフェイスの同期と非同期バージョンの **両方ではなく** 、 **いずれか** を実装します。</span><span class="sxs-lookup"><span data-stu-id="745eb-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="745eb-130">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="745eb-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="745eb-131">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="745eb-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="745eb-132">両方のインターフェイスを実装した場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="745eb-133">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="745eb-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="745eb-134">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="745eb-135">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="745eb-136">上記のコードでは、`ProcessUserAgent.Write` が、ユーザー エージェント文字列を使用して機能するユーザー指定のコードです。</span><span class="sxs-lookup"><span data-stu-id="745eb-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="745eb-137">次のコードは、`Startup` クラスで `SampleAsyncPageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="745eb-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="745eb-138">次のコードでは、<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> を呼び出して、 */Movies* 内のページにのみ `SampleAsyncPageFilter` を適用しています。</span><span class="sxs-lookup"><span data-stu-id="745eb-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="745eb-139">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="745eb-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="745eb-140">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="745eb-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="745eb-141">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="745eb-142">次のコードでは、非同期 Razor ページ フィルターをオーバーライドしています。</span><span class="sxs-lookup"><span data-stu-id="745eb-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="745eb-143">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-143">Implement a filter attribute</span></span>

<span data-ttu-id="745eb-144">組み込みの属性ベースのフィルターである <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> フィルターは、サブクラス化できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="745eb-145">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="745eb-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="745eb-146">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="745eb-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="745eb-147">ブラウザー開発者ツールなどのツールを使用して、ヘッダーを調べます。</span><span class="sxs-lookup"><span data-stu-id="745eb-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="745eb-148">**[応答ヘッダー]** の下に `author: Rick` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="745eb-149">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="745eb-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="745eb-150">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="745eb-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="745eb-151">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="745eb-151">Authorize filter attribute</span></span>

<span data-ttu-id="745eb-152">`PageModel` に [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="745eb-153">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="745eb-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="745eb-154">Razor ページのフィルターである [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) および [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) を使用すると、Razor ページ ハンドラーの実行の前後に Razor Pages でコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="745eb-155">Razor ページ フィルターは、個々のページ ハンドラー メソッドに適用できないことを除き、[ASP.NET Core MVC アクション フィルター](xref:mvc/controllers/filters#action-filters)と類似しています。</span><span class="sxs-lookup"><span data-stu-id="745eb-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="745eb-156">Razor ページ フィルター</span><span class="sxs-lookup"><span data-stu-id="745eb-156">Razor Page filters:</span></span>

* <span data-ttu-id="745eb-157">モデルのバインドが行われる前の、ハンドラー メソッドが選択された後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="745eb-158">モデルのバインドの完了後の、ハンドラー メソッドの実行前にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="745eb-159">ハンドラー メソッドの実行後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="745eb-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="745eb-160">ページまたはグローバルに実装できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="745eb-161">特定のページ ハンドラー メソッドには適用できません。</span><span class="sxs-lookup"><span data-stu-id="745eb-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="745eb-162">コードは、ページ コンストラクターまたはミドルウェアを使用してハンドラー メソッドの実行前に実行できますが、[HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext) にアクセスできるのは Razor ページ フィルターのみです。</span><span class="sxs-lookup"><span data-stu-id="745eb-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="745eb-163">フィルターには、`HttpContext` へのアクセスを提供する [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生のパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="745eb-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="745eb-164">たとえば、「[フィルター属性を実装する](#ifa)」のサンプルでは、応答にヘッダーが追加されます。これは、コンストラクターやミドルウェアでは実行できません。</span><span class="sxs-lookup"><span data-stu-id="745eb-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="745eb-165">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="745eb-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="745eb-166">Razor ページ フィルターには、グローバルまたはページ レベルで適用できる次のメソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="745eb-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="745eb-167">同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="745eb-167">Synchronous methods:</span></span>

  * <span data-ttu-id="745eb-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="745eb-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0):ハンドラー メソッドが実行される前の、モデル バインドが完了した後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="745eb-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0):ハンドラー メソッドが実行された後の、アクションの結果の前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="745eb-171">非同期メソッド:</span><span class="sxs-lookup"><span data-stu-id="745eb-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="745eb-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0):ハンドラー メソッドが選択された後の、モデル バインドが行われる前に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="745eb-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0):ハンドラー メソッドが呼び出される前の、モデル バインドの完了後に非同期で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="745eb-174">フィルター インターフェイスの同期と非同期バージョンの両方ではなく、 **いずれか** を実装します。</span><span class="sxs-lookup"><span data-stu-id="745eb-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="745eb-175">フレームワークは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="745eb-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="745eb-176">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="745eb-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="745eb-177">両方のインターフェイスを実装した場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="745eb-178">ページのオーバーライドでもこの規則は同じです。オーバーライドの同期バージョンまたは非同期バージョンを実装でき、両方はできません。</span><span class="sxs-lookup"><span data-stu-id="745eb-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="745eb-179">Razor ページにフィルターをグローバルに実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="745eb-180">`IAsyncPageFilter` は、次のコードによって実装されます。</span><span class="sxs-lookup"><span data-stu-id="745eb-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="745eb-181">前述のコードでは、[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) は不要です。</span><span class="sxs-lookup"><span data-stu-id="745eb-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="745eb-182">これは、アプリケーションのトレース情報を提供するためにサンプルで使用されています。</span><span class="sxs-lookup"><span data-stu-id="745eb-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="745eb-183">次のコードは、`Startup` クラスで `SampleAsyncPageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="745eb-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="745eb-184">次は、完全な `Startup` クラスのコードです。</span><span class="sxs-lookup"><span data-stu-id="745eb-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="745eb-185">次のコードは、`AddFolderApplicationModelConvention` を呼び出し、 */subFolder* のページにのみ `SampleAsyncPageFilter` を適用します。</span><span class="sxs-lookup"><span data-stu-id="745eb-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="745eb-186">次のコードは、同期 `IPageFilter` を実装します。</span><span class="sxs-lookup"><span data-stu-id="745eb-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="745eb-187">次のコードは、`SamplePageFilter` を有効にします。</span><span class="sxs-lookup"><span data-stu-id="745eb-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="745eb-188">フィルター メソッドをオーバーライドして Razor ページにフィルターを実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="745eb-189">次のコードでは、同期 Razor ページ フィルターをオーバーライドしています。</span><span class="sxs-lookup"><span data-stu-id="745eb-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="745eb-190">フィルター属性を実装する</span><span class="sxs-lookup"><span data-stu-id="745eb-190">Implement a filter attribute</span></span>

<span data-ttu-id="745eb-191">組み込みの属性ベースのフィルターである [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) フィルターはサブクラス化することができます。</span><span class="sxs-lookup"><span data-stu-id="745eb-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="745eb-192">次のフィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="745eb-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="745eb-193">次のコードは、`AddHeader` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="745eb-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="745eb-194">順序をオーバーライドする手順については、「[既定の順序のオーバーライド](xref:mvc/controllers/filters#overriding-the-default-order)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="745eb-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="745eb-195">フィルターからフィルター パイプラインをショート サーキットする手順については、「[キャンセルとショート サーキット](xref:mvc/controllers/filters#cancellation-and-short-circuiting)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="745eb-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="745eb-196">Authorize フィルター属性</span><span class="sxs-lookup"><span data-stu-id="745eb-196">Authorize filter attribute</span></span>

<span data-ttu-id="745eb-197">`PageModel` に [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性を適用できます。</span><span class="sxs-lookup"><span data-stu-id="745eb-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end