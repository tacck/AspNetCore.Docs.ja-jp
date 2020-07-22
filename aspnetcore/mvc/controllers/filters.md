---
title: ASP.NET Core フィルター
author: Rick-Anderson
description: フィルターのしくみと ASP.NET Core でそれを使用する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 96d24940af6c591e3c02bfa26ed9d7d6ea60d27d
ms.sourcegitcommit: d00a200bc8347af794b24184da14ad5c8b6bba9a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/21/2020
ms.locfileid: "86869979"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="887fc-103">ASP.NET Core フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="887fc-104">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="887fc-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="887fc-105">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="887fc-106">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="887fc-107">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="887fc-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="887fc-108">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="887fc-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="887fc-109">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="887fc-110">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="887fc-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="887fc-111">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="887fc-112">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="887fc-113">このドキュメントは、 Razor ビューがあるページ、API コントローラー、およびコントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="887fc-114">フィルターは、 [ Razor コンポーネント](xref:blazor/components/index)で直接使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="887fc-115">次の場合、フィルターは間接的にコンポーネントに影響するのみです。</span><span class="sxs-lookup"><span data-stu-id="887fc-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="887fc-116">コンポーネントがページまたはビューに埋め込まれている。</span><span class="sxs-lookup"><span data-stu-id="887fc-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="887fc-117">ページまたはコントローラー/ビューでフィルターが使用されている。</span><span class="sxs-lookup"><span data-stu-id="887fc-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="887fc-118">[サンプルを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="887fc-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="887fc-119">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="887fc-119">How filters work</span></span>

<span data-ttu-id="887fc-120">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="887fc-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="887fc-121">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、およびアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="887fc-124">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="887fc-124">Filter types</span></span>

<span data-ttu-id="887fc-125">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="887fc-126">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="887fc-127">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="887fc-128">[リソースフィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="887fc-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="887fc-129">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-129">Run after authorization.</span></span>  
  * <span data-ttu-id="887fc-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="887fc-131">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="887fc-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="887fc-133">[アクションフィルター](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="887fc-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="887fc-134"> アクション メソッドが呼び出される直前と直後にコードを実行します。</span><span class="sxs-lookup"><span data-stu-id="887fc-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="887fc-135">アクションに渡される引数を変更できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="887fc-136">アクションから返された結果を変更できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="887fc-137">は、ページではサポートされて**いません** Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="887fc-138">[例外フィルター](#exception-filters)では、応答本文への書き込みが行われる前に発生する未処理の例外にグローバル ポリシーが適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="887fc-139">[結果フィルター](#result-filters)では、アクション結果の実行の直前と直後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="887fc-140">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="887fc-141">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="887fc-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="887fc-142">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="887fc-145">実装</span><span class="sxs-lookup"><span data-stu-id="887fc-145">Implementation</span></span>

<span data-ttu-id="887fc-146">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="887fc-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="887fc-147">同期フィルターでは、そのパイプライン ステージの前と後にコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="887fc-148">たとえば、<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="887fc-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="887fc-150">上記のコードでは、 [Mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)は[サンプルダウンロード](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)のユーティリティ関数です。</span><span class="sxs-lookup"><span data-stu-id="887fc-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="887fc-151">非同期フィルターでは、`On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="887fc-152"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="887fc-153">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="887fc-154">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="887fc-154">Multiple filter stages</span></span>

<span data-ttu-id="887fc-155">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="887fc-156">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスでは次のものが実装されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="887fc-157">同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="887fc-158">非同期: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> と <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="887fc-159">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="887fc-160">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="887fc-161">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="887fc-162">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="887fc-163"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="887fc-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="887fc-164">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="887fc-164">Built-in filter attributes</span></span>

<span data-ttu-id="887fc-165">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="887fc-166">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="887fc-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-167">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="887fc-168">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="887fc-169">[ブラウザー開発者ツール](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)などのツールを使用して、ヘッダーを確認します。</span><span class="sxs-lookup"><span data-stu-id="887fc-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="887fc-170">**[応答ヘッダー]** の下に `author: Rick Anderson` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="887fc-171">次のコードでは、次のことを行う `ActionFilterAttribute` が実装されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="887fc-172">構成システムからタイトルと名前を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="887fc-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="887fc-173">前のサンプルとは異なり、次のコードでは、フィルター パラメーターをコードに追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="887fc-174">応答ヘッダーにタイトルと名前を追加します。</span><span class="sxs-lookup"><span data-stu-id="887fc-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-175">構成オプションは、[構成システム](xref:fundamentals/configuration/index)から[オプション パターン](xref:fundamentals/configuration/options)を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="887fc-176">たとえば、*appsettings.json* ファイルから、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="887fc-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="887fc-177">`StartUp.ConfigureServices` では、次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="887fc-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="887fc-178">`"Position"` 構成領域を使用して `PositionOptions` クラスをサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="887fc-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="887fc-179">`MyActionFilterAttribute` をサービス コンテナーに追加します。</span><span class="sxs-lookup"><span data-stu-id="887fc-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="887fc-180">次のコードは `PositionOptions` クラスを示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="887fc-181">次のコードでは、`Index2` メソッドに `MyActionFilterAttribute` が適用されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="887fc-182">**応答ヘッダー**では、 `author: Rick Anderson` `Editor: Joe Smith` `Sample/Index2` エンドポイントが呼び出されると、とが表示されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="887fc-183">次のコードでは、 `MyActionFilterAttribute` ページにとを適用し `AddHeaderAttribute` Razor ます。</span><span class="sxs-lookup"><span data-stu-id="887fc-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="887fc-184">フィルターをページハンドラーメソッドに適用することはできません Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="887fc-185">これらは、ページモデルに適用することも、グローバルに適用することもでき Razor ます。</span><span class="sxs-lookup"><span data-stu-id="887fc-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="887fc-186">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="887fc-187">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="887fc-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="887fc-188">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="887fc-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="887fc-189">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="887fc-190">コントローラー アクションでの属性の使用。</span><span class="sxs-lookup"><span data-stu-id="887fc-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="887fc-191">フィルター属性は、ページハンドラーメソッドには適用できません Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="887fc-192">コントローラーまたはページで属性を使用し Razor ます。</span><span class="sxs-lookup"><span data-stu-id="887fc-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="887fc-193">Razor次のコードに示すように、すべてのコントローラー、アクション、およびページに対してグローバルに。</span><span class="sxs-lookup"><span data-stu-id="887fc-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="887fc-194">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="887fc-194">Default order of execution</span></span>

<span data-ttu-id="887fc-195">パイプラインの特定のステージに対して複数のフィルターがある場合に、スコープがフィルターの実行の既定の順序を決定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="887fc-196">グローバル フィルターがクラス フィルターを囲み、クラス フィルターがメソッド フィルターを囲みます。</span><span class="sxs-lookup"><span data-stu-id="887fc-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="887fc-197">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="887fc-198">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="887fc-198">The filter sequence:</span></span>

* <span data-ttu-id="887fc-199">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="887fc-200">コントローラーおよびページフィルターの*前*のコード Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="887fc-201">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="887fc-202">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="887fc-203">コントローラーおよびページフィルターの*後*のコード Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="887fc-204">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="887fc-205">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="887fc-206">Sequence</span><span class="sxs-lookup"><span data-stu-id="887fc-206">Sequence</span></span> | <span data-ttu-id="887fc-207">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="887fc-207">Filter scope</span></span> | <span data-ttu-id="887fc-208">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="887fc-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="887fc-209">1</span><span class="sxs-lookup"><span data-stu-id="887fc-209">1</span></span> | <span data-ttu-id="887fc-210">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-211">2</span><span class="sxs-lookup"><span data-stu-id="887fc-211">2</span></span> | <span data-ttu-id="887fc-212">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="887fc-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="887fc-213">3</span><span class="sxs-lookup"><span data-stu-id="887fc-213">3</span></span> | <span data-ttu-id="887fc-214">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-215">4</span><span class="sxs-lookup"><span data-stu-id="887fc-215">4</span></span> | <span data-ttu-id="887fc-216">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="887fc-217">5</span><span class="sxs-lookup"><span data-stu-id="887fc-217">5</span></span> | <span data-ttu-id="887fc-218">コントローラーまたは Razor ページ</span><span class="sxs-lookup"><span data-stu-id="887fc-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="887fc-219">6</span><span class="sxs-lookup"><span data-stu-id="887fc-219">6</span></span> | <span data-ttu-id="887fc-220">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="887fc-221">コントローラー レベルのフィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-221">Controller level filters</span></span>

<span data-ttu-id="887fc-222"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="887fc-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="887fc-223">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="887fc-223">These methods:</span></span>

* <span data-ttu-id="887fc-224">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="887fc-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="887fc-225">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="887fc-226">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="887fc-227">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="887fc-228">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="887fc-229">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="887fc-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="887fc-230">The `TestController`:</span></span>

* <span data-ttu-id="887fc-231">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="887fc-232">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="887fc-233">`https://localhost:5001/Test2/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="887fc-234">コントローラー レベルのフィルターでは、[Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="887fc-235">コントローラー レベルのフィルターは、メソッドにフィルターが適用された後に実行されるように設定することは**できません**。</span><span class="sxs-lookup"><span data-stu-id="887fc-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="887fc-236">順序については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="887fc-236">Order is explained in the next section.</span></span>

<span data-ttu-id="887fc-237">ページについて Razor は、「 [ Razor フィルターメソッドをオーバーライドしてページフィルターを実装](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="887fc-238">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="887fc-238">Overriding the default order</span></span>

<span data-ttu-id="887fc-239"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="887fc-240">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="887fc-241">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="887fc-242">より高い `Order` の値を持つフィルターのそれよりも前に *before* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="887fc-243">より高い `Order` の値を持つフィルターのそれよりも後に *after* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="887fc-244">`Order` プロパティは、次のコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="887fc-245">次のコントローラーの 2 つのアクションフィルターについて考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="887fc-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="887fc-246">グローバル フィルターが次のように `StartUp.ConfigureServices` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="887fc-247">3 つのフィルターは、次の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="887fc-248">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="887fc-249">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="887fc-250">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="887fc-251">既に説明したように、コントローラー レベルのフィルターでは、[Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) プロパティが `int.MinValue` に設定されます。組み込みのフィルターの場合は、`Order` が 0 以外の値に設定されている場合を除き、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="887fc-252">上記のコードにおいて、`MySampleActionFilter` にはグローバル スコープがあるため、コントローラー スコープを持つ `MyAction2FilterAttribute` の前で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="887fc-253">`MyAction2FilterAttribute` を最初に実行するようにするには、`int.MinValue` に対して順序を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="887fc-254">グローバル フィルター `MySampleActionFilter` を最初に実行するようにするには、次のように `Order` を `int.MinValue` に設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="887fc-255">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="887fc-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="887fc-256">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="887fc-257">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-258">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="887fc-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="887fc-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="887fc-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="887fc-260">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="887fc-261">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="887fc-262">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="887fc-263">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="887fc-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="887fc-264">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="887fc-265">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="887fc-265">Dependency injection</span></span>

<span data-ttu-id="887fc-266">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="887fc-267">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="887fc-268">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="887fc-269">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="887fc-269">A type-activated filter means:</span></span>

* <span data-ttu-id="887fc-270">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-270">An instance is created for each request.</span></span>
* <span data-ttu-id="887fc-271">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="887fc-272">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="887fc-273">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="887fc-274">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="887fc-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="887fc-275">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="887fc-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="887fc-276">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="887fc-277">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="887fc-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="887fc-278">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="887fc-279">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-279">Loggers are available from DI.</span></span> <span data-ttu-id="887fc-280">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="887fc-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="887fc-281">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="887fc-282">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="887fc-282">Logging added to filters:</span></span>

* <span data-ttu-id="887fc-283">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="887fc-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="887fc-284">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="887fc-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="887fc-285">組み込みのフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="887fc-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="887fc-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="887fc-287">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="887fc-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="887fc-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="887fc-289">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="887fc-290">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="887fc-291">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="887fc-292">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="887fc-293">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="887fc-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="887fc-294">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="887fc-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="887fc-295">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="887fc-296">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="887fc-297">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="887fc-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="887fc-299">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="887fc-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="887fc-300">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="887fc-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="887fc-301">TypeFilterAttribute</span></span>

<span data-ttu-id="887fc-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="887fc-303"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="887fc-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="887fc-304">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="887fc-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="887fc-305">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="887fc-306">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="887fc-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="887fc-307">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="887fc-308">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="887fc-309">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="887fc-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="887fc-310">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="887fc-311">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="887fc-312">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="887fc-313">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-313">Authorization filters</span></span>

<span data-ttu-id="887fc-314">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-314">Authorization filters:</span></span>

* <span data-ttu-id="887fc-315">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="887fc-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="887fc-316">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="887fc-316">Control access to action methods.</span></span>
* <span data-ttu-id="887fc-317">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="887fc-318">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="887fc-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="887fc-319">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="887fc-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="887fc-320">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="887fc-321">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-321">Calls the authorization system.</span></span>
* <span data-ttu-id="887fc-322">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-322">Does not authorize requests.</span></span>

<span data-ttu-id="887fc-323">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="887fc-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="887fc-324">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-324">The exception will not be handled.</span></span>
* <span data-ttu-id="887fc-325">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="887fc-326">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="887fc-327">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="887fc-328">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-328">Resource filters</span></span>

<span data-ttu-id="887fc-329">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-329">Resource filters:</span></span>

* <span data-ttu-id="887fc-330"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="887fc-331">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="887fc-332">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="887fc-333">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="887fc-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="887fc-334">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="887fc-335">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="887fc-335">Resource filter examples:</span></span>

* <span data-ttu-id="887fc-336">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="887fc-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="887fc-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="887fc-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="887fc-338">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="887fc-339">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="887fc-340">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-340">Action filters</span></span>

<span data-ttu-id="887fc-341">アクションフィルターは、ページには適用**されません** Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-341">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="887fc-342">ページは <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> とをサポートし <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> ます。</span><span class="sxs-lookup"><span data-stu-id="887fc-342"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="887fc-343">詳細については、[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="887fc-344">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-344">Action filters:</span></span>

* <span data-ttu-id="887fc-345"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="887fc-346">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="887fc-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="887fc-347">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="887fc-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="887fc-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力の読み取りを有効にします。</span><span class="sxs-lookup"><span data-stu-id="887fc-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="887fc-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="887fc-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="887fc-352">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="887fc-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="887fc-353">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="887fc-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="887fc-354">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="887fc-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="887fc-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="887fc-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="887fc-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="887fc-358">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="887fc-358">Setting this property to null:</span></span>

  * <span data-ttu-id="887fc-359">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="887fc-360">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="887fc-361">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="887fc-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="887fc-362">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="887fc-363">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="887fc-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="887fc-364">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="887fc-365">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="887fc-366">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="887fc-367">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="887fc-367">Validate model state.</span></span>
* <span data-ttu-id="887fc-368">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="887fc-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="887fc-369">属性で注釈が付けられたコントローラーは、 `[ApiController]` モデルの状態を自動的に検証し、400の応答を返します。</span><span class="sxs-lookup"><span data-stu-id="887fc-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="887fc-370">詳細については、「[自動的な HTTP 400 応答](xref:web-api/index#automatic-http-400-responses)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="887fc-371">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="887fc-372">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="887fc-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="887fc-374">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="887fc-375">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="887fc-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="887fc-376">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="887fc-377">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="887fc-378">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-378">Exception filters</span></span>

<span data-ttu-id="887fc-379">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-379">Exception filters:</span></span>

* <span data-ttu-id="887fc-380"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="887fc-381">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="887fc-382">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="887fc-383">次のコードでは、例外フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="887fc-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="887fc-384">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-384">Exception filters:</span></span>

* <span data-ttu-id="887fc-385">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-385">Don't have before and after events.</span></span>
* <span data-ttu-id="887fc-386"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="887fc-387">Razorページまたはコントローラーの作成、[モデルのバインド](xref:mvc/models/model-binding)、アクションフィルター、アクションメソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="887fc-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="887fc-388">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="887fc-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="887fc-389">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="887fc-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="887fc-390">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-390">This stops propagation of the exception.</span></span> <span data-ttu-id="887fc-391">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="887fc-392">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="887fc-392">Only an action filter can do that.</span></span>

<span data-ttu-id="887fc-393">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-393">Exception filters:</span></span>

* <span data-ttu-id="887fc-394">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="887fc-395">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="887fc-396">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="887fc-397">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="887fc-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="887fc-398">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="887fc-399">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="887fc-400">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-400">Result filters</span></span>

<span data-ttu-id="887fc-401">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-401">Result filters:</span></span>

* <span data-ttu-id="887fc-402">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="887fc-402">Implement an interface:</span></span>
  * <span data-ttu-id="887fc-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="887fc-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="887fc-405">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="887fc-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="887fc-406">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="887fc-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="887fc-407">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="887fc-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="887fc-408">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="887fc-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="887fc-409">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="887fc-410">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="887fc-411">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="887fc-412">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="887fc-413">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="887fc-414">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="887fc-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="887fc-415">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="887fc-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="887fc-417">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="887fc-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="887fc-418">`IResultFilter.OnResultExecuting` での例外のスロー: </span><span class="sxs-lookup"><span data-stu-id="887fc-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="887fc-419">アクション結果と後続フィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="887fc-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="887fc-420">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="887fc-421"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、おそらく応答は既にクライアントに送信されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="887fc-422">応答が既にクライアントに送信されていた場合は、それを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="887fc-423">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="887fc-424">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="887fc-425">`Exception` を null に設定すると、例外を効果的に処理し、パイプラインの後方で例外が再スローされるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="887fc-426">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="887fc-427">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="887fc-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> で `await next` を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="887fc-429">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="887fc-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="887fc-430">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="887fc-431">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="887fc-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="887fc-432">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="887fc-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="887fc-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="887fc-434">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-434">This includes action results produced by:</span></span>

* <span data-ttu-id="887fc-435">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="887fc-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="887fc-436">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="887fc-436">Exception filters.</span></span>

<span data-ttu-id="887fc-437">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "*422 処理不可エンティティ*" 状態コードを使ってアクションの結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="887fc-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="887fc-438">IFilterFactory</span></span>

<span data-ttu-id="887fc-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="887fc-440">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="887fc-441">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="887fc-442">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="887fc-443">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="887fc-444">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="887fc-445">フィルターは次のコードで適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="887fc-446">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)を実行することで、上記のコードをテストします。</span><span class="sxs-lookup"><span data-stu-id="887fc-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="887fc-447">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="887fc-448">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="887fc-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="887fc-449">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="887fc-450">**作成者:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="887fc-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="887fc-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="887fc-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="887fc-452">**内部:**`My header`</span><span class="sxs-lookup"><span data-stu-id="887fc-452">**internal:** `My header`</span></span>

<span data-ttu-id="887fc-453">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="887fc-454">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="887fc-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="887fc-455">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="887fc-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="887fc-456">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="887fc-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="887fc-457">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="887fc-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="887fc-459">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="887fc-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="887fc-460">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="887fc-461">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="887fc-462">上記のコードでは、`SampleActionFilter` を適用する方法としては、`[SampleActionFilter]` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="887fc-463">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="887fc-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="887fc-464">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="887fc-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="887fc-465">ただし、フィルターはランタイムの一部である点がミドルウェアとは異なります。つまり、それらはコンテキストとコンストラクトにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="887fc-466">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="887fc-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="887fc-467">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="887fc-468"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="887fc-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="887fc-469">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="887fc-470">次の操作</span><span class="sxs-lookup"><span data-stu-id="887fc-470">Next actions</span></span>

* <span data-ttu-id="887fc-471">「 [ Razor ページのフィルターメソッド」を](xref:razor-pages/filter)参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="887fc-472">フィルターを試してみるには、 [GitHub サンプルをダウンロードし、テスト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)して、変更します。</span><span class="sxs-lookup"><span data-stu-id="887fc-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="887fc-473">作成者: [Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/)、[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="887fc-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="887fc-474">ASP.NET Core で*フィルター*を使用すると、要求処理パイプラインの特定のステージの前または後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="887fc-475">組み込みのフィルターでは次のようなタスクが処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="887fc-476">許可 (ユーザーに許可が与えられていないリソースの場合、アクセスを禁止する)。</span><span class="sxs-lookup"><span data-stu-id="887fc-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="887fc-477">応答キャッシュ (要求パイプラインを迂回し、キャッシュされている応答を返す)。</span><span class="sxs-lookup"><span data-stu-id="887fc-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="887fc-478">横断的な問題を処理するカスタム フィルターを作成できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="887fc-479">横断的な問題の例には、エラー処理、キャッシュ、構成、認証、ログなどがあります。</span><span class="sxs-lookup"><span data-stu-id="887fc-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="887fc-480">フィルターにより、コードの重複が回避されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="887fc-481">たとえば、エラー処理例外フィルターではエラー処理を統合できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="887fc-482">このドキュメントは、 Razor ビューがあるページ、API コントローラー、およびコントローラーに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="887fc-483">[サンプルを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="887fc-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="887fc-484">フィルターのしくみ</span><span class="sxs-lookup"><span data-stu-id="887fc-484">How filters work</span></span>

<span data-ttu-id="887fc-485">*ASP.NET Core のアクション呼び出しパイプライン*内で実行されるフィルターは、*フィルター パイプライン*と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="887fc-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="887fc-486">フィルター パイプラインは、ASP.NET Core が実行するアクションを選択した後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![要求は、他のミドルウェア、ルーティング ミドルウェア、アクション選択、ASP.NET Core のアクション呼び出しパイプラインを通じて処理されます。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="887fc-489">フィルターの種類</span><span class="sxs-lookup"><span data-stu-id="887fc-489">Filter types</span></span>

<span data-ttu-id="887fc-490">フィルターの種類はそれぞれ、フィルター パイプラインの異なるステージで実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="887fc-491">[承認フィルター](#authorization-filters)は、最初に実行され、ユーザーが要求に対して承認されているかどうかを判断するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="887fc-492">承認フィルターでは、要求が承認されていない場合、パイプラインがショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="887fc-493">[リソースフィルター](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="887fc-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="887fc-494">承認後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-494">Run after authorization.</span></span>  
  * <span data-ttu-id="887fc-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> では、残りのフィルター パイプラインの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="887fc-496">たとえば、`OnResourceExecuting` では、モデル バインディングの前にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="887fc-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> では、残りのパイプラインの完了後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="887fc-498">[アクション フィルター](#action-filters)は、個々のアクション メソッドが呼び出される直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="887fc-499">アクションに渡される引数とアクションから返される結果を操作するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="887fc-500">アクションフィルターは、ページではサポートされて**いません** Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="887fc-501">[例外フィルター](#exception-filters)は、応答本文に何かが書き込まれる前に発生する未処理の例外にグローバル ポリシーを適用するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="887fc-502">[結果フィルター](#result-filters)は、個々のアクション結果の実行の直前と直後にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="887fc-503">アクション メソッドが正常に実行された場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="887fc-504">ビューまたはフォーマッタ実行を取り囲む必要があるロジックに便利です。</span><span class="sxs-lookup"><span data-stu-id="887fc-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="887fc-505">フィルターの種類がフィルター パイプラインでどのように連携しているかを、次の図に示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![要求は、承認フィルター、リソース フィルター、モデル バインド、アクション フィルター、アクションの実行とアクション結果の変換、例外フィルター、結果フィルター、結果の実行を介して処理されます。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="887fc-508">実装</span><span class="sxs-lookup"><span data-stu-id="887fc-508">Implementation</span></span>

<span data-ttu-id="887fc-509">フィルターは、異なるインターフェイス定義を介して、同期と非同期の実装をサポートします。</span><span class="sxs-lookup"><span data-stu-id="887fc-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="887fc-510">同期フィルターでは、パイプライン ステージの前 (`On-Stage-Executing`) と後 (`On-Stage-Executed`) にコードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="887fc-511">たとえば、`OnActionExecuting` はアクション メソッドの呼び出し前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="887fc-512">`OnActionExecuted` は、アクション メソッドが戻った後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="887fc-513">非同期フィルターにより `On-Stage-ExecutionAsync` メソッドが定義されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="887fc-514">上記のコードでは、`SampleAsyncActionFilter` にアクション メソッドを実行する <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="887fc-515">各 `On-Stage-ExecutionAsync` メソッドは、フィルターのパイプライン ステージを実行する `FilterType-ExecutionDelegate` を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="887fc-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="887fc-516">複数のフィルター ステージ</span><span class="sxs-lookup"><span data-stu-id="887fc-516">Multiple filter stages</span></span>

<span data-ttu-id="887fc-517">複数のフィルター ステージのためのインターフェイスを 1 つのクラスで実装できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="887fc-518">たとえば、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> クラスは、`IActionFilter`、`IResultFilter`、およびそれらの非同期バージョンを実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="887fc-519">フィルター インターフェイスの同期と非同期バージョンの**両方ではなく**、**いずれか**を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="887fc-520">ランタイムは、最初にフィルターが非同期インターフェイスを実装しているかどうかをチェックして、している場合はそれを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="887fc-521">していない場合は、同期インターフェイスのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="887fc-522">非同期インターフェイスと同期インターフェイスの両方が 1 つのクラスで実装される場合、非同期メソッドのみが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="887fc-523"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> などの抽象クラスを使用する場合は、同期メソッドのみをオーバーライドするか、フィルターの種類ごとに非同期メソッドをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="887fc-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="887fc-524">組み込みのフィルター属性</span><span class="sxs-lookup"><span data-stu-id="887fc-524">Built-in filter attributes</span></span>

<span data-ttu-id="887fc-525">ASP.NET Core には、サブクラスを作成したり、カスタマイズしたりできる組み込みの属性ベースのフィルターが含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="887fc-526">たとえば、次の結果フィルターは、応答にヘッダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="887fc-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-527">上記の例のように、属性によってフィルターは引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="887fc-528">`AddHeaderAttribute` をコントローラーまたはアクション メソッドに適用し、HTTP ヘッダーの名前と値を指定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="887fc-529">フィルター インターフェイスのいくつかには対応する属性があり、カスタムの実装に基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="887fc-530">フィルター属性:</span><span class="sxs-lookup"><span data-stu-id="887fc-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="887fc-531">フィルターのスコープと実行の順序</span><span class="sxs-lookup"><span data-stu-id="887fc-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="887fc-532">フィルターは、3 つの*スコープ*のいずれかでパイプラインに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="887fc-533">アクションで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="887fc-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="887fc-534">コントローラーで属性を使用します。</span><span class="sxs-lookup"><span data-stu-id="887fc-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="887fc-535">次のコードのように、すべてのコントローラーとアクションに対してグローバルに:</span><span class="sxs-lookup"><span data-stu-id="887fc-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="887fc-536">上記のコードでは、[MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) コレクションを利用し、3 つのフィルターがグローバルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="887fc-537">実行の既定の順序</span><span class="sxs-lookup"><span data-stu-id="887fc-537">Default order of execution</span></span>

<span data-ttu-id="887fc-538">"*同じ種類のフィルター*" が複数存在する場合は、スコープによって、フィルターの実行の既定の順序が決まります。</span><span class="sxs-lookup"><span data-stu-id="887fc-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="887fc-539">グローバル フィルターによってクラス フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-539">Global filters surround class filters.</span></span> <span data-ttu-id="887fc-540">クラス フィルターによってメソッド フィルターが囲まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-540">Class filters surround method filters.</span></span>

<span data-ttu-id="887fc-541">フィルターの入れ子の結果として、フィルターの *after* コードが *before* コードと逆の順序で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="887fc-542">フィルター シーケンス:</span><span class="sxs-lookup"><span data-stu-id="887fc-542">The filter sequence:</span></span>

* <span data-ttu-id="887fc-543">グローバル フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="887fc-544">コントローラー フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="887fc-545">アクション メソッド フィルターの *before* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="887fc-546">アクション メソッド フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="887fc-547">コントローラー フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="887fc-548">グローバル フィルターの *after* コード。</span><span class="sxs-lookup"><span data-stu-id="887fc-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="887fc-549">次の例は、同期アクション フィルターに対してフィルター メソッドが呼び出される順序を示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="887fc-550">Sequence</span><span class="sxs-lookup"><span data-stu-id="887fc-550">Sequence</span></span> | <span data-ttu-id="887fc-551">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="887fc-551">Filter scope</span></span> | <span data-ttu-id="887fc-552">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="887fc-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="887fc-553">1</span><span class="sxs-lookup"><span data-stu-id="887fc-553">1</span></span> | <span data-ttu-id="887fc-554">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-555">2</span><span class="sxs-lookup"><span data-stu-id="887fc-555">2</span></span> | <span data-ttu-id="887fc-556">コントローラー</span><span class="sxs-lookup"><span data-stu-id="887fc-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-557">3</span><span class="sxs-lookup"><span data-stu-id="887fc-557">3</span></span> | <span data-ttu-id="887fc-558">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-559">4</span><span class="sxs-lookup"><span data-stu-id="887fc-559">4</span></span> | <span data-ttu-id="887fc-560">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="887fc-561">5</span><span class="sxs-lookup"><span data-stu-id="887fc-561">5</span></span> | <span data-ttu-id="887fc-562">コントローラー</span><span class="sxs-lookup"><span data-stu-id="887fc-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="887fc-563">6</span><span class="sxs-lookup"><span data-stu-id="887fc-563">6</span></span> | <span data-ttu-id="887fc-564">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="887fc-565">このシーケンスが示すもの:</span><span class="sxs-lookup"><span data-stu-id="887fc-565">This sequence shows:</span></span>

* <span data-ttu-id="887fc-566">メソッド フィルターは、コントローラー フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="887fc-567">コントローラー フィルターは、グローバル フィルター内で入れ子になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="887fc-568">コントローラーおよび Razor ページレベルのフィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="887fc-569"><xref:Microsoft.AspNetCore.Mvc.Controller> 基底クラスから継承するすべてのコントローラーに、[Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)、[Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="887fc-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="887fc-570">これらのメソッド:</span><span class="sxs-lookup"><span data-stu-id="887fc-570">These methods:</span></span>

* <span data-ttu-id="887fc-571">特定のアクションに対して実行されるフィルターをラップします。</span><span class="sxs-lookup"><span data-stu-id="887fc-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="887fc-572">`OnActionExecuting` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="887fc-573">`OnActionExecuted` は、あらゆるアクション フィルターの後に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="887fc-574">`OnActionExecutionAsync` は、あらゆるアクション フィルターの前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="887fc-575">`next` の後のフィルターのコードは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="887fc-576">たとえば、ダウンロード サンプルで、`MySampleActionFilter` は起動中、グローバルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="887fc-577">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="887fc-577">The `TestController`:</span></span>

* <span data-ttu-id="887fc-578">`SampleActionFilterAttribute` (`[SampleActionFilter]`) が `FilterTest2` アクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="887fc-579">`OnActionExecuting` と `OnActionExecuted` がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="887fc-580">`https://localhost:5001/Test/FilterTest2` に移動すると、次のコードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="887fc-581">ページについて Razor は、「 [ Razor フィルターメソッドをオーバーライドしてページフィルターを実装](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="887fc-582">既定の順序のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="887fc-582">Overriding the default order</span></span>

<span data-ttu-id="887fc-583"><xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> を実装することで、実行の既定の順序をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="887fc-584">`IOrderedFilter` により、実行の順序を決定するために、スコープよりも優先される <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> プロパティが公開されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="887fc-585">より低い `Order` 値を持つフィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="887fc-586">より高い `Order` の値を持つフィルターのそれよりも前に *before* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="887fc-587">より高い `Order` の値を持つフィルターのそれよりも後に *after* コードが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="887fc-588">`Order` プロパティはコンストラクター パラメーターで設定できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="887fc-589">上記の例にある同じ 3 つのアクション フィルターを検討してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="887fc-590">コントローラーとグローバル フィルターの `Order` プロパティが 1 と 2 にそれぞれ設定される場合、実行順序が逆になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="887fc-591">Sequence</span><span class="sxs-lookup"><span data-stu-id="887fc-591">Sequence</span></span> | <span data-ttu-id="887fc-592">フィルターのスコープ</span><span class="sxs-lookup"><span data-stu-id="887fc-592">Filter scope</span></span> | <span data-ttu-id="887fc-593">`Order` プロパティ</span><span class="sxs-lookup"><span data-stu-id="887fc-593">`Order` property</span></span> | <span data-ttu-id="887fc-594">フィルター メソッド</span><span class="sxs-lookup"><span data-stu-id="887fc-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="887fc-595">1</span><span class="sxs-lookup"><span data-stu-id="887fc-595">1</span></span> | <span data-ttu-id="887fc-596">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-596">Method</span></span> | <span data-ttu-id="887fc-597">0</span><span class="sxs-lookup"><span data-stu-id="887fc-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="887fc-598">2</span><span class="sxs-lookup"><span data-stu-id="887fc-598">2</span></span> | <span data-ttu-id="887fc-599">コントローラー</span><span class="sxs-lookup"><span data-stu-id="887fc-599">Controller</span></span> | <span data-ttu-id="887fc-600">1</span><span class="sxs-lookup"><span data-stu-id="887fc-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="887fc-601">3</span><span class="sxs-lookup"><span data-stu-id="887fc-601">3</span></span> | <span data-ttu-id="887fc-602">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-602">Global</span></span> | <span data-ttu-id="887fc-603">2</span><span class="sxs-lookup"><span data-stu-id="887fc-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="887fc-604">4</span><span class="sxs-lookup"><span data-stu-id="887fc-604">4</span></span> | <span data-ttu-id="887fc-605">グローバル</span><span class="sxs-lookup"><span data-stu-id="887fc-605">Global</span></span> | <span data-ttu-id="887fc-606">2</span><span class="sxs-lookup"><span data-stu-id="887fc-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="887fc-607">5</span><span class="sxs-lookup"><span data-stu-id="887fc-607">5</span></span> | <span data-ttu-id="887fc-608">コントローラー</span><span class="sxs-lookup"><span data-stu-id="887fc-608">Controller</span></span> | <span data-ttu-id="887fc-609">1</span><span class="sxs-lookup"><span data-stu-id="887fc-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="887fc-610">6</span><span class="sxs-lookup"><span data-stu-id="887fc-610">6</span></span> | <span data-ttu-id="887fc-611">Method</span><span class="sxs-lookup"><span data-stu-id="887fc-611">Method</span></span> | <span data-ttu-id="887fc-612">0</span><span class="sxs-lookup"><span data-stu-id="887fc-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="887fc-613">フィルターの実行順序を決定するときに、`Order` プロパティによりスコープがオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="887fc-614">最初に順序でフィルターが並べ替えられ、次に同じ順位の優先度を決めるためにスコープが使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="887fc-615">組み込みのフィルターはすべて `IOrderedFilter` を実装し、既定の `Order` 値を 0 に設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="887fc-616">組み込みのフィルターの場合、`Order` をゼロ以外の値に設定しない限り、スコープによって順序が決定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="887fc-617">キャンセルとショートサーキット</span><span class="sxs-lookup"><span data-stu-id="887fc-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="887fc-618">フィルター メソッドに提供される <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> パラメーターで <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> プロパティを設定することで、フィルター パイプラインをショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="887fc-619">たとえば、次のリソース フィルターは、パイプラインの残りの部分が実行されるのを防止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-620">次のコードでは、`ShortCircuitingResourceFilter` と `AddHeader` の両方のフィルターが `SomeResource` アクション メソッドをターゲットにしています。</span><span class="sxs-lookup"><span data-stu-id="887fc-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="887fc-621">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="887fc-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="887fc-622">これはリソース フィルターであり、`AddHeader` はアクション フィルターであるため、最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="887fc-623">パイプラインの残りの部分は迂回されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="887fc-624">そのため、`SomeResource` アクションの場合、`AddHeader` フィルターが実行されることはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="887fc-625">`ShortCircuitingResourceFilter` が最初に実行された場合は、両方のフィルターがアクション メソッド レベルで適用されると、この動作が同じになります。</span><span class="sxs-lookup"><span data-stu-id="887fc-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="887fc-626">そのフィルターの種類が原因で、あるいは `Order` プロパティの明示的な使用により、`ShortCircuitingResourceFilter` が最初に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="887fc-627">依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="887fc-627">Dependency injection</span></span>

<span data-ttu-id="887fc-628">フィルターは種類ごとまたはインスタンスごとに追加できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="887fc-629">インスタンスが追加される場合、そのインスタンスはすべての要求に対して使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="887fc-630">種類が追加される場合、それは種類でアクティブ化されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="887fc-631">種類でアクティブ化されるフィルターの意味:</span><span class="sxs-lookup"><span data-stu-id="887fc-631">A type-activated filter means:</span></span>

* <span data-ttu-id="887fc-632">インスタンスは要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-632">An instance is created for each request.</span></span>
* <span data-ttu-id="887fc-633">コンストラクターの依存関係は、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって入力されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="887fc-634">属性として実装され、コントローラー クラスまたはアクション メソッドに直接追加されるフィルターは、[依存関係の挿入](xref:fundamentals/dependency-injection) (DI) によって提供されるコンストラクターの依存関係を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="887fc-635">コンストラクターの依存関係は、次の理由から DI によって与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="887fc-636">属性には、適用される場所で提供される独自のコンストラクター パラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="887fc-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="887fc-637">これは、属性のしくみの制限です。</span><span class="sxs-lookup"><span data-stu-id="887fc-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="887fc-638">次のフィルターでは、DI から提供されるコンストラクターの依存関係がサポートされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="887fc-639">属性に実装された <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="887fc-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="887fc-640">上記のフィルターは、コントローラーまたはアクション メソッドに適用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="887fc-641">ロガーは DI から利用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-641">Loggers are available from DI.</span></span> <span data-ttu-id="887fc-642">ただし、ログ目的でのみフィルターを作成し、使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="887fc-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="887fc-643">[組み込みフレームワークのログ機能](xref:fundamentals/logging/index)で通常、ログ記録に必要なものが与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="887fc-644">フィルターに追加されるログ記録:</span><span class="sxs-lookup"><span data-stu-id="887fc-644">Logging added to filters:</span></span>

* <span data-ttu-id="887fc-645">ビジネス ドメインの懸念事項やフィルターに固有の動作に焦点を合わせます。</span><span class="sxs-lookup"><span data-stu-id="887fc-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="887fc-646">アクションやその他のフレームワーク イベントはログに記録**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="887fc-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="887fc-647">組み込みフィルターでは、アクションとフレームワーク イベントが記録されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="887fc-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="887fc-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="887fc-649">サービス フィルターの実装の種類は `ConfigureServices` に登録されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="887fc-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は DI からフィルターのインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="887fc-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="887fc-651">このコードでは、`AddHeaderResultServiceFilter` が示されています。</span><span class="sxs-lookup"><span data-stu-id="887fc-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="887fc-652">次のコードでは、`AddHeaderResultServiceFilter` が DI コンテナーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="887fc-653">次のコードでは、`ServiceFilter` 属性により、DI から `AddHeaderResultServiceFilter` フィルターのインスタンスが取得されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="887fc-654">`ServiceFilterAttribute` を使用するときに、[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="887fc-655">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="887fc-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="887fc-656">ASP.NET Core ランタイムで保証されないこと:</span><span class="sxs-lookup"><span data-stu-id="887fc-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="887fc-657">フィルターのインスタンスが 1 つ作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="887fc-658">フィルターが後の時点で、DI コンテナーから再要求されることはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="887fc-659">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="887fc-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="887fc-661">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="887fc-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="887fc-662">`CreateInstance` により、DI から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="887fc-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="887fc-663">TypeFilterAttribute</span></span>

<span data-ttu-id="887fc-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は<xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> と似ていますが、その型は DI コンテナーから直接解決されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="887fc-665"><xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> を使って型をインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="887fc-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="887fc-666">`TypeFilterAttribute` 型は DI コンテナーから直接解決されないためです。</span><span class="sxs-lookup"><span data-stu-id="887fc-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="887fc-667">`TypeFilterAttribute` を利用して参照される型は、DI コンテナーに登録する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="887fc-668">DI コンテナーによって依存関係が満たされています。</span><span class="sxs-lookup"><span data-stu-id="887fc-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="887fc-669">`TypeFilterAttribute` は必要に応じて、型のコンストラクター引数を受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="887fc-670">`TypeFilterAttribute` を使用するときに、[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="887fc-671">フィルター インスタンスが、それが作成された要求範囲の外で再利用される*可能性がある*ことを示唆します。</span><span class="sxs-lookup"><span data-stu-id="887fc-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="887fc-672">ASP.NET Core ランタイムでは、フィルターの 1 インスタンスが作成されるという保証はありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="887fc-673">シングルトン以外で有効期間があるサービスに依存するフィルターと共に使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="887fc-674">次の例は、`TypeFilterAttribute` を使用して、型に引数を渡す方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="887fc-675">承認フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-675">Authorization filters</span></span>

<span data-ttu-id="887fc-676">承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-676">Authorization filters:</span></span>

* <span data-ttu-id="887fc-677">フィルター パイプライン内で実行される最初のフィルターです。</span><span class="sxs-lookup"><span data-stu-id="887fc-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="887fc-678">アクション メソッドへのアクセスを制御します。</span><span class="sxs-lookup"><span data-stu-id="887fc-678">Control access to action methods.</span></span>
* <span data-ttu-id="887fc-679">before メソッドが与えられ、after メソッドは与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="887fc-680">カスタム承認フィルターには、カスタム承認フレームワークが必要です。</span><span class="sxs-lookup"><span data-stu-id="887fc-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="887fc-681">カスタム フィルターを記述するよりも、独自の承認ポリシーを構成するか、カスタム承認ポリシーを記述することを選びます。</span><span class="sxs-lookup"><span data-stu-id="887fc-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="887fc-682">組み込み承認フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="887fc-683">承認システムを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-683">Calls the authorization system.</span></span>
* <span data-ttu-id="887fc-684">要求は承認されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-684">Does not authorize requests.</span></span>

<span data-ttu-id="887fc-685">承認フィルター内で例外をスロー**しません**。</span><span class="sxs-lookup"><span data-stu-id="887fc-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="887fc-686">例外は処理されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-686">The exception will not be handled.</span></span>
* <span data-ttu-id="887fc-687">例外フィルターで例外が処理されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="887fc-688">承認フィルターで例外が発生した場合、チャレンジ発行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="887fc-689">承認の詳細については、[こちら](xref:security/authorization/introduction)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="887fc-690">リソース フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-690">Resource filters</span></span>

<span data-ttu-id="887fc-691">リソース フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-691">Resource filters:</span></span>

* <span data-ttu-id="887fc-692"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="887fc-693">実行により、ほとんどのフィルター パイプラインがラップされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="887fc-694">[承認フィルター](#authorization-filters)のみ、リソース フィルターの前に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="887fc-695">リソース フィルターは、ほとんどのパイプラインをショートサーキットする目的で役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="887fc-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="887fc-696">たとえば、キャッシュ フィルターにより、キャッシュ ヒットがあるとき、残りのパイプラインを回避できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="887fc-697">リソース フィルターの例:</span><span class="sxs-lookup"><span data-stu-id="887fc-697">Resource filter examples:</span></span>

* <span data-ttu-id="887fc-698">前に示した[ショートサーキットするリソース フィルター](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="887fc-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="887fc-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="887fc-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="887fc-700">モデル バインドがフォーム データにアクセスすることを禁止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="887fc-701">メモリにフォーム データが読み込まれないようにする目的で大きなファイルのアップロードに使用されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="887fc-702">アクション フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="887fc-703">アクションフィルターは、ページには適用**されません** Razor 。</span><span class="sxs-lookup"><span data-stu-id="887fc-703">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="887fc-704">ページは <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> とをサポートし <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> ます。</span><span class="sxs-lookup"><span data-stu-id="887fc-704"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="887fc-705">詳細については、[Razor Pages のフィルター メソッド](xref:razor-pages/filter)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="887fc-706">アクション フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-706">Action filters:</span></span>

* <span data-ttu-id="887fc-707"><xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> のインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="887fc-708">この実行はアクション メソッドの実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="887fc-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="887fc-709">次のコードは、サンプル アクション フィルターを示しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="887fc-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> では次のプロパティが提供されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="887fc-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - アクション メソッドへの入力を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="887fc-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="887fc-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - コントローラー インスタンスを操作できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="887fc-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - `Result` を設定すると、アクション メソッドと後続のアクション フィルターの実行がショートサーキットされます。</span><span class="sxs-lookup"><span data-stu-id="887fc-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="887fc-714">アクション メソッドで例外をスローする:</span><span class="sxs-lookup"><span data-stu-id="887fc-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="887fc-715">後続のフィルターの実行を回避します。</span><span class="sxs-lookup"><span data-stu-id="887fc-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="887fc-716">`Result` の設定とは異なり、結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="887fc-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> は、`Controller` と `Result` に加え、次のプロパティを提供します。</span><span class="sxs-lookup"><span data-stu-id="887fc-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="887fc-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 別のフィルターによってアクションの実行がショートサーキットされた場合は、true になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="887fc-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - アクションまたは前に実行されたアクション フィルターが例外をスローした場合は、null 以外になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="887fc-720">このプロパティを null に設定する:</span><span class="sxs-lookup"><span data-stu-id="887fc-720">Setting this property to null:</span></span>

  * <span data-ttu-id="887fc-721">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="887fc-722">`Result` は、アクション メソッドから返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="887fc-723">`IAsyncActionFilter` の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> の呼び出しによって:</span><span class="sxs-lookup"><span data-stu-id="887fc-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="887fc-724">後続のすべてのアクション フィルターとアクション メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="887fc-725">`ActionExecutedContext` を返します。</span><span class="sxs-lookup"><span data-stu-id="887fc-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="887fc-726">ショートサーキットするには、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> を結果インスタンスに割り当てます。`next` (`ActionExecutionDelegate`) は呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="887fc-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="887fc-727">このフレームワークからは、サブクラス化できる抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> が与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="887fc-728">`OnActionExecuting` アクション フィルターを次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="887fc-729">モデルの状態を検証します。</span><span class="sxs-lookup"><span data-stu-id="887fc-729">Validate model state.</span></span>
* <span data-ttu-id="887fc-730">状態が有効でない場合は、エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="887fc-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="887fc-731">`OnActionExecuted` メソッドは、アクション メソッドの後に実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="887fc-732">また、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> プロパティを介してアクションの結果を表示したり、操作したりできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="887fc-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> は、アクションの実行が別のフィルターによってショートサーキットされた場合、true に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="887fc-734">アクションまたは後続のアクション フィルターが例外をスローした場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="887fc-735">`Exception` を null に設定すると:</span><span class="sxs-lookup"><span data-stu-id="887fc-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="887fc-736">例外が効果的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="887fc-737">`ActionExecutedContext.Result` は、アクション メソッドから通常どおり返されたかのように実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="887fc-738">例外フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-738">Exception filters</span></span>

<span data-ttu-id="887fc-739">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-739">Exception filters:</span></span>

* <span data-ttu-id="887fc-740"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="887fc-741">共通のエラー処理ポリシーを実装する目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="887fc-742">次の例外フィルターのサンプルでは、カスタムのエラー ビューを使用して、アプリの開発中に発生する例外に関する詳細を表示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="887fc-743">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-743">Exception filters:</span></span>

* <span data-ttu-id="887fc-744">before イベントと after イベントが与えられません。</span><span class="sxs-lookup"><span data-stu-id="887fc-744">Don't have before and after events.</span></span>
* <span data-ttu-id="887fc-745"><xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="887fc-746">Razorページまたはコントローラーの作成、[モデルのバインド](xref:mvc/models/model-binding)、アクションフィルター、アクションメソッドで発生する未処理の例外を処理します。</span><span class="sxs-lookup"><span data-stu-id="887fc-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="887fc-747">リソース フィルター、結果フィルター、または MVC 結果の実行で発生した例外はキャッチ**しません**。</span><span class="sxs-lookup"><span data-stu-id="887fc-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="887fc-748">例外を処理するには、<xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> プロパティを `true` に設定するか、応答を記述します。</span><span class="sxs-lookup"><span data-stu-id="887fc-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="887fc-749">これにより、例外の伝達を停止します。</span><span class="sxs-lookup"><span data-stu-id="887fc-749">This stops propagation of the exception.</span></span> <span data-ttu-id="887fc-750">例外フィルターで例外を "成功" に変えることはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="887fc-751">それができるのは、アクション フィルターだけです。</span><span class="sxs-lookup"><span data-stu-id="887fc-751">Only an action filter can do that.</span></span>

<span data-ttu-id="887fc-752">例外フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-752">Exception filters:</span></span>

* <span data-ttu-id="887fc-753">アクション内で発生する例外のトラップに適しています。</span><span class="sxs-lookup"><span data-stu-id="887fc-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="887fc-754">エラー処理ミドルウェアほど柔軟ではありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="887fc-755">例外処理にはミドルウェアを選択してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="887fc-756">呼び出されたアクション メソッドに基づいてエラー処理が*異なる*状況でのみ例外フィルターを使用します。</span><span class="sxs-lookup"><span data-stu-id="887fc-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="887fc-757">たとえば、アプリには、API エンドポイントとビュー/HTML の両方に対するアクション メソッドがある場合があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="887fc-758">API エンドポイントは、JSON としてのエラー情報を返す可能性がある一方で、ビュー ベースのアクションがエラー ページを HTML として返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="887fc-759">結果フィルター</span><span class="sxs-lookup"><span data-stu-id="887fc-759">Result filters</span></span>

<span data-ttu-id="887fc-760">結果フィルター:</span><span class="sxs-lookup"><span data-stu-id="887fc-760">Result filters:</span></span>

* <span data-ttu-id="887fc-761">インターフェイスを実装します:</span><span class="sxs-lookup"><span data-stu-id="887fc-761">Implement an interface:</span></span>
  * <span data-ttu-id="887fc-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="887fc-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> または <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="887fc-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="887fc-764">この実行はアクション結果の実行を取り囲みます。</span><span class="sxs-lookup"><span data-stu-id="887fc-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="887fc-765">IResultFilter および IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="887fc-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="887fc-766">次は、HTTP ヘッダーを追加する結果フィルターのコードです。</span><span class="sxs-lookup"><span data-stu-id="887fc-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="887fc-767">実行されている結果の種類は、アクションに依存します。</span><span class="sxs-lookup"><span data-stu-id="887fc-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="887fc-768">ビューを返すアクションには、実行されている <xref:Microsoft.AspNetCore.Mvc.ViewResult> の一部として、すべての razor 処理が含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="887fc-769">API メソッドは、結果の実行の一部としていくつかのシリアル化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="887fc-770">[アクション結果](xref:mvc/controllers/actions)に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="887fc-771">結果フィルターは、アクションまたはアクション フィルターによってアクション結果を生成される場合にのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="887fc-772">結果フィルターは、次の場合には実行されません。</span><span class="sxs-lookup"><span data-stu-id="887fc-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="887fc-773">承認フィルターまたはリソース フィルターによって、パイプラインがショートサーキットされる。</span><span class="sxs-lookup"><span data-stu-id="887fc-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="887fc-774">アクションの結果を生成することで、例外フィルターによって例外が処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="887fc-775"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> メソッドは、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> を `true` に設定することで、アクションの結果と後続の結果フィルターの実行をショートサーキットできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="887fc-776">ショートサーキットする場合は、空の応答が生成されないように応答オブジェクトに記述します。</span><span class="sxs-lookup"><span data-stu-id="887fc-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="887fc-777">`IResultFilter.OnResultExecuting` で例外をスローすることで:</span><span class="sxs-lookup"><span data-stu-id="887fc-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="887fc-778">アクション結果と後続フィルターの実行が回避されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="887fc-779">結果は成功ではなく、失敗として処理されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="887fc-780"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> メソッドが実行されたとき、応答が既にクライアントに送信されている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="887fc-781">応答が既にクライアントに送信されていた場合は、それ以上変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="887fc-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="887fc-782">別のフィルターによってアクションの結果の実行がショートサーキットされた場合、`ResultExecutedContext.Canceled` は `true` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="887fc-783">アクションの結果または後続の結果フィルターが例外をスローした場合、`ResultExecutedContext.Exception` は null 以外の値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="887fc-784">`Exception` を null に設定すると、例外を効果的に処理でき、パイプラインの後方で ASP.NET Core によって例外が再スローされることを防げます。</span><span class="sxs-lookup"><span data-stu-id="887fc-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="887fc-785">結果フィルターの例外を処理するとき、応答にデータを書き込む目的で信頼できる方法はありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="887fc-786">アクションの結果により例外がスローされるとき、ヘッダーがクライアントにフラッシュされている場合、エラー コードを送信する目的で信頼できるメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="887fc-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="887fc-787"><xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> の場合、<xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> で `await next` を呼び出すと、後続のすべての結果フィルターとアクションの結果が実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="887fc-788">ショートサーキットするには、[ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) を `true` に設定し、`ResultExecutionDelegate` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="887fc-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="887fc-789">このフレームワークからは、サブクラス化できる抽象 `ResultFilterAttribute` が与えられます。</span><span class="sxs-lookup"><span data-stu-id="887fc-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="887fc-790">前に示した [AddHeaderAttribute](#add-header-attribute) クラスは、結果フィルター属性の一例です。</span><span class="sxs-lookup"><span data-stu-id="887fc-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="887fc-791">IAlwaysRunResultFilter および IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="887fc-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="887fc-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> および <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> インターフェイスでは、すべてのアクションの結果に対して実行される <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> の実装が宣言されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="887fc-793">これには、以下によって生成されるアクションの結果が含まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-793">This includes action results produced by:</span></span>

* <span data-ttu-id="887fc-794">ショートサーキットが行われる承認フィルターとリソース フィルター。</span><span class="sxs-lookup"><span data-stu-id="887fc-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="887fc-795">例外フィルター。</span><span class="sxs-lookup"><span data-stu-id="887fc-795">Exception filters.</span></span>

<span data-ttu-id="887fc-796">たとえば、次のフィルターは常に実行され、コンテンツ ネゴシエーションが失敗した場合に "*422 処理不可エンティティ*" 状態コードを使ってアクションの結果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) を設定します。</span><span class="sxs-lookup"><span data-stu-id="887fc-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="887fc-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="887fc-797">IFilterFactory</span></span>

<span data-ttu-id="887fc-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="887fc-799">そのため、`IFilterFactory` インスタンスはフィルター パイプライン内の任意の場所で `IFilterMetadata` インスタンスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="887fc-800">ランタイムでは、フィルターを呼び出す準備をする際、`IFilterFactory` へのキャストが試行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="887fc-801">そのキャストが成功した場合、呼び出される `IFilterMetadata` インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="887fc-802">これにより、アプリの起動時に正確なフィルター パイプラインを明示的に設定する必要がないため、柔軟なデザインが可能になります。</span><span class="sxs-lookup"><span data-stu-id="887fc-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="887fc-803">フィルターを作成するための別の方法として、カスタムの属性の実装で `IFilterFactory` を実装できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="887fc-804">[ダウンロード サンプル](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)を実行することで、前のコードをテストできます。</span><span class="sxs-lookup"><span data-stu-id="887fc-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="887fc-805">F12 開発者ツールを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="887fc-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="887fc-806">`https://localhost:5001/Sample/HeaderWithFactory` に移動します。</span><span class="sxs-lookup"><span data-stu-id="887fc-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="887fc-807">F12 開発者ツールでは、サンプル コードによって追加された次の応答ヘッダーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="887fc-808">**作成者:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="887fc-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="887fc-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="887fc-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="887fc-810">**内部:**`My header`</span><span class="sxs-lookup"><span data-stu-id="887fc-810">**internal:** `My header`</span></span>

<span data-ttu-id="887fc-811">上記のコードでは、**internal:** `My header` という応答ヘッダーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="887fc-812">属性に実装された IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="887fc-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="887fc-813">`IFilterFactory` を実装するフィルターは次のようなフィルターに便利です。</span><span class="sxs-lookup"><span data-stu-id="887fc-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="887fc-814">パラメーターの引き渡しを必要としません。</span><span class="sxs-lookup"><span data-stu-id="887fc-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="887fc-815">DI で満たす必要があるコンストラクター依存関係があります。</span><span class="sxs-lookup"><span data-stu-id="887fc-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="887fc-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> を実装します。</span><span class="sxs-lookup"><span data-stu-id="887fc-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="887fc-817">`IFilterFactory` は、<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> インスタンスを作成するために <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> メソッドを公開します。</span><span class="sxs-lookup"><span data-stu-id="887fc-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="887fc-818">`CreateInstance` により、サービス コンテナー (DI) から指定の型が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="887fc-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="887fc-819">次のコードでは、`[SampleActionFilter]` を適用する 3 つの手法を確認できます。</span><span class="sxs-lookup"><span data-stu-id="887fc-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="887fc-820">上記のコードでは、`SampleActionFilter` を適用する方法としては、`[SampleActionFilter]` でメソッドを装飾する方法が推奨されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="887fc-821">フィルター パイプラインでのミドルウェアの使用</span><span class="sxs-lookup"><span data-stu-id="887fc-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="887fc-822">リソース フィルターは、パイプラインの後方で登場するすべての実行を囲む点で、[ミドルウェア](xref:fundamentals/middleware/index)のように機能します。</span><span class="sxs-lookup"><span data-stu-id="887fc-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="887fc-823">ただし、フィルターは ASP.NET Core ランタイムの一部である点がミドルウェアとは異なります。つまり、フィルターには ASP.NET Core コンテキストとコンストラクトへのアクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="887fc-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="887fc-824">ミドルウェアをフィルターとして使用するには、フィルター パイプラインに挿入するミドルウェアを指定する `Configure` メソッドを使用して型を作成します。</span><span class="sxs-lookup"><span data-stu-id="887fc-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="887fc-825">ローカリゼーション ミドルウェアを使用して要求の現在のカルチャを確立する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="887fc-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="887fc-826"><xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> を使用し、ミドルウェアを実行します。</span><span class="sxs-lookup"><span data-stu-id="887fc-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="887fc-827">ミドルウェア フィルターは、フィルター パイプラインのリソース フィルターと同じステージ (モデル バインドの前、残りのパイプラインの後) で実行されます。</span><span class="sxs-lookup"><span data-stu-id="887fc-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="887fc-828">次の操作</span><span class="sxs-lookup"><span data-stu-id="887fc-828">Next actions</span></span>

* <span data-ttu-id="887fc-829">「 [ Razor ページのフィルターメソッド」を](xref:razor-pages/filter)参照してください。</span><span class="sxs-lookup"><span data-stu-id="887fc-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="887fc-830">フィルターを試してみるには、 [GitHub サンプルをダウンロードし、テスト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)して、変更します。</span><span class="sxs-lookup"><span data-stu-id="887fc-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
