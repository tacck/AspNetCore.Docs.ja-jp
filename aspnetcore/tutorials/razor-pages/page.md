---
title: パート 3、スキャフォールディングされた Razor Pages
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 3。
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: a6efbb22f8b6280bd636cd1575d8a4a2bca0bb06
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97486175"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="16cac-103">パート 3、ASP.NET Core でスキャフォールディングされた Razor ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="16cac-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="16cac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="16cac-105">このチュートリアルでは、[前のチュートリアル](xref:tutorials/razor-pages/model)でスキャフォールディングによって作成された Razor ページについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16cac-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="16cac-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="16cac-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="16cac-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="16cac-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="16cac-108">[作成]、[削除]、[詳細]、および [編集] ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="16cac-109">*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="16cac-110">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="16cac-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="16cac-111">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="16cac-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="16cac-112">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="16cac-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="16cac-113">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="16cac-114">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="16cac-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="16cac-115">Razor ページでは、`OnGetAsync` または `OnGet` が呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="16cac-116">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="16cac-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="16cac-117">`OnGet` によって `void` が返される場合、または `OnGetAsync` によって `Task` が返される場合は、return ステートメントは使用されません。</span><span class="sxs-lookup"><span data-stu-id="16cac-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="16cac-118">たとえば、[プライバシー] ページでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="16cac-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="16cac-119">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="16cac-120">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="16cac-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="16cac-121">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="16cac-122">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="16cac-123">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="16cac-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="16cac-124">@page ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="16cac-124">The @page directive</span></span>

<span data-ttu-id="16cac-125">`@page` Razor ディレクティブを使うと、ファイルが MVC アクションになります。つまり、これで要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="16cac-126">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="16cac-127">`@page` および `@model` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="16cac-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="16cac-128">詳細については、「[Razor の構文](xref:mvc/views/razor#razor-syntax)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="16cac-129">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="16cac-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="16cac-130">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="16cac-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="16cac-131">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="16cac-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="16cac-132">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="16cac-133">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="16cac-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="16cac-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="16cac-135">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="16cac-136">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="16cac-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="16cac-137">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="16cac-138">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-138">The layout page</span></span>

<span data-ttu-id="16cac-139">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="16cac-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="16cac-140">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="16cac-141">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="16cac-142">*Pages/Shared/_Layout.cshtml* ファイルを開いて確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="16cac-143">[レイアウト](xref:mvc/views/layout) テンプレートを使用すると、HTML コンテナーのレイアウトを次のようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="16cac-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="16cac-144">1 つの場所で指定される。</span><span class="sxs-lookup"><span data-stu-id="16cac-144">Specified in one place.</span></span>
* <span data-ttu-id="16cac-145">サイトの複数のページに適用される。</span><span class="sxs-lookup"><span data-stu-id="16cac-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="16cac-146">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="16cac-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="16cac-147">`RenderBody` は、ページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに "*ラップ*" されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="16cac-148">たとえば、 **[プライバシー]** リンクを選択すると、`RenderBody` メソッド内で *Pages/Privacy.cshtml* ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="16cac-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="16cac-149">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="16cac-149">ViewData and layout</span></span>

<span data-ttu-id="16cac-150">*Pages/Movies/Index.cshtml* ファイルの次のマークアップを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="16cac-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="16cac-151">上の強調表示されたマークアップは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="16cac-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="16cac-152">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="16cac-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="16cac-153">`PageModel` 基底クラスには `ViewData` 辞書プロパティが含まれており、これを使用してビューにデータを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="16cac-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="16cac-154">オブジェクトは、\***キー/値** _ のパターンを使用して `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="16cac-155">上のサンプルでは、`Title` プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="16cac-156">`Title` プロパティは _Pages/Shared/_Layout.cshtml\* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="16cac-157">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="16cac-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="16cac-158">`@*Markup removed for brevity.*@` 行は Razor コメントです。</span><span class="sxs-lookup"><span data-stu-id="16cac-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="16cac-159">HTML コメント `<!-- -->` とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="16cac-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="16cac-160">詳細については、「[MDN Web ドキュメント: HTML の概要](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="16cac-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="16cac-161">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="16cac-161">Update the layout</span></span>

1. <span data-ttu-id="16cac-162">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="16cac-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="16cac-163">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="16cac-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="16cac-164">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="16cac-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="16cac-165">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="16cac-166">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="16cac-167">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="16cac-168">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="16cac-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="16cac-169">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="16cac-170">変更内容を保存し、**RpMovie** リンクを選択してアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="16cac-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="16cac-171">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="16cac-172">**[Home]\(ホーム\)** 、 **[RpMovie]** 、 **[Create]/(作成/)** 、 **[Edit]\(編集\)** 、 **[Delete]\(削除\)** のリンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="16cac-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="16cac-173">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="16cac-174">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="16cac-175">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="16cac-176">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="16cac-177">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="16cac-178">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="16cac-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="16cac-179">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="16cac-180">Create ページのモデル</span><span class="sxs-lookup"><span data-stu-id="16cac-180">The Create page model</span></span>

<span data-ttu-id="16cac-181">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="16cac-182">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="16cac-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="16cac-183">[作成] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="16cac-184">このチュートリアルの後半では、状態を初期化する `OnGet` の例を示します。</span><span class="sxs-lookup"><span data-stu-id="16cac-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="16cac-185">`Page` メソッドでは、*Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="16cac-186">`Movie` プロパティでは [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="16cac-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="16cac-187">[作成] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="16cac-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="16cac-188">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="16cac-189">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="16cac-190">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="16cac-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="16cac-191">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="16cac-192">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="16cac-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="16cac-193">モデル エラーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="16cac-193">If there are no model errors:</span></span>

* <span data-ttu-id="16cac-194">データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-194">The data is saved.</span></span>
* <span data-ttu-id="16cac-195">ブラウザーは [Index] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="16cac-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="16cac-196">Razor の作成ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-196">The Create Razor Page</span></span>

<span data-ttu-id="16cac-197">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="16cac-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16cac-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16cac-199">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで次のタグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml ページの VS17 ビュー](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="16cac-201">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16cac-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="16cac-202">上記のマークアップには、次のタグ ヘルパーが示されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="16cac-203">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="16cac-204">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="16cac-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="16cac-205">スキャフォールディング エンジンでは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="16cac-206">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="16cac-207">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="16cac-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="16cac-208">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `[for]` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="16cac-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="16cac-209">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="16cac-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="16cac-210">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16cac-211">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="16cac-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="16cac-212">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次へ:データベースの操作](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="16cac-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="16cac-213">[作成]、[削除]、[詳細]、および [編集] ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="16cac-214">*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="16cac-215">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="16cac-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="16cac-216">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="16cac-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="16cac-217">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="16cac-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="16cac-218">スキャフォールディングされたページでは、このパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="16cac-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="16cac-219">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="16cac-220">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="16cac-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="16cac-221">`OnGetAsync` または `OnGet` が Razor ページで呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="16cac-222">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="16cac-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="16cac-223">`OnGet` から `void` が返される場合、または `OnGetAsync` から `Task` が返される場合、return メソッドは使用されません。</span><span class="sxs-lookup"><span data-stu-id="16cac-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="16cac-224">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="16cac-225">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="16cac-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="16cac-226">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="16cac-227">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="16cac-228">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="16cac-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="16cac-229">`@page` Razor ディレクティブを使うと、ファイルが MVC アクションになります。つまり、これで要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="16cac-230">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="16cac-231">`@page` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="16cac-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="16cac-232">詳細については、「[Razor の構文](xref:mvc/views/razor#razor-syntax)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="16cac-233">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="16cac-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="16cac-234">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="16cac-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="16cac-235">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="16cac-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="16cac-236">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="16cac-237">HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="16cac-237">HTML Helpers</span></span>

<span data-ttu-id="16cac-238">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="16cac-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="16cac-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="16cac-240">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="16cac-241">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="16cac-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="16cac-242">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="16cac-243">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-243">The layout page</span></span>

<span data-ttu-id="16cac-244">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="16cac-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="16cac-245">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="16cac-246">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="16cac-247">*Pages/Shared/_Layout.cshtml* ファイルを開いて確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="16cac-248">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="16cac-249">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="16cac-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="16cac-250">`RenderBody` は、作成したページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="16cac-251">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Pages/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="16cac-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="16cac-252">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="16cac-252">ViewData and layout</span></span>

<span data-ttu-id="16cac-253">*Pages/Movies/Index.cshtml* ファイルの次のコードを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="16cac-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="16cac-254">上の強調表示されたコードは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="16cac-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="16cac-255">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="16cac-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="16cac-256">`PageModel` 基本クラスには `ViewData` 辞書プロパティがあり、これを使用して、ビューに渡すデータを追加できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="16cac-257">キー/値のパターンを使用して、`ViewData` 辞書にオブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="16cac-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="16cac-258">上のサンプルでは、`Title` プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="16cac-259">`Title` プロパティは *Pages/Shared/_Layout.cshtml* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="16cac-260">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="16cac-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="16cac-261">`@*Markup removed for brevity.*@` 行は Razor コメントです。</span><span class="sxs-lookup"><span data-stu-id="16cac-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="16cac-262">HTML コメント `<!-- -->` とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="16cac-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="16cac-263">詳細については、「[MDN Web ドキュメント: HTML の概要](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="16cac-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="16cac-264">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="16cac-264">Update the layout</span></span>

<span data-ttu-id="16cac-265">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="16cac-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="16cac-266">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="16cac-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="16cac-267">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="16cac-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="16cac-268">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="16cac-269">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="16cac-270">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="16cac-271">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="16cac-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="16cac-272">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="16cac-273">変更内容を保存し、**RpMovie** リンクをクリックしてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="16cac-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="16cac-274">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="16cac-275">その他のリンク ( **[Home]** 、 **[RpMovie]** 、 **[Create]** 、 **[Edit]** 、および **[Delete]** ) をテストします。</span><span class="sxs-lookup"><span data-stu-id="16cac-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="16cac-276">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="16cac-277">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="16cac-278">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="16cac-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="16cac-279">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="16cac-280">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="16cac-281">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="16cac-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="16cac-282">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="16cac-283">Create ページのモデル</span><span class="sxs-lookup"><span data-stu-id="16cac-283">The Create page model</span></span>

<span data-ttu-id="16cac-284">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="16cac-285">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="16cac-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="16cac-286">[作成] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="16cac-287">このチュートリアルで後ほど、`OnGet` メソッドの状態の初期化を確認できます。</span><span class="sxs-lookup"><span data-stu-id="16cac-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="16cac-288">`Page` メソッドでは、*Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="16cac-289">`Movie` プロパティでは [BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="16cac-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="16cac-290">[作成] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="16cac-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="16cac-291">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="16cac-292">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="16cac-293">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="16cac-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="16cac-294">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="16cac-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="16cac-295">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="16cac-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="16cac-296">モデル エラーがない場合、データは保存され、ブラウザーは [Index] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="16cac-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-no-locrazor-page"></a><span data-ttu-id="16cac-297">Razor の作成ページ</span><span class="sxs-lookup"><span data-stu-id="16cac-297">The Create Razor Page</span></span>

<span data-ttu-id="16cac-298">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="16cac-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="16cac-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="16cac-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="16cac-300">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml ページの VS17 ビュー](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="16cac-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="16cac-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="16cac-303">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16cac-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="16cac-304">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="16cac-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="16cac-305">Visual Studio for Mac に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="16cac-306">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="16cac-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="16cac-307">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="16cac-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="16cac-308">スキャフォールディング エンジンでは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="16cac-309">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="16cac-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="16cac-310">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="16cac-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="16cac-311">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `[for]` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="16cac-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="16cac-312">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="16cac-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="16cac-313">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="16cac-313">Additional resources</span></span>

* [<span data-ttu-id="16cac-314">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="16cac-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="16cac-315">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次へ:データベースの操作](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="16cac-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
