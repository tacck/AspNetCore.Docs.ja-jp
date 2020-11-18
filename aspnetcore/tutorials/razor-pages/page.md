---
title: パート 3、スキャフォールディングされた Razor Pages
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 3。
ms.author: riande
ms.date: 09/25/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: a9494feacbe783b20a9f5eb98ef9e481f2c713fa
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360893"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="bdc9a-103">パート 3、ASP.NET Core でスキャフォールディングされた Razor ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="bdc9a-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bdc9a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bdc9a-105">このチュートリアルでは、[前のチュートリアル](xref:tutorials/razor-pages/model)でスキャフォールディングによって作成された Razor ページについて説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bdc9a-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="bdc9a-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="bdc9a-108">[Create]、[Delete]、[詳細]、[編集] ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bdc9a-109">*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="bdc9a-110">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bdc9a-111">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="bdc9a-112">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bdc9a-113">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bdc9a-114">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bdc9a-115">Razor ページでは、`OnGetAsync` または `OnGet` が呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="bdc9a-116">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bdc9a-117">`OnGet` によって `void` が返される場合、または `OnGetAsync` によって `Task` が返される場合は、return ステートメントは使用されません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="bdc9a-118">たとえば、[プライバシー] ページでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="bdc9a-119">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bdc9a-120">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bdc9a-121">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="bdc9a-122">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bdc9a-123">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="bdc9a-124">@page ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-124">The @page directive</span></span>

<span data-ttu-id="bdc9a-125">`@page` Razor ディレクティブを使うと、ファイルが MVC アクションになります。つまり、これで要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bdc9a-126">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bdc9a-127">`@page` および `@model` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bdc9a-128">詳細については、「[Razor の構文](xref:mvc/views/razor#razor-syntax)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bdc9a-129">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bdc9a-130">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bdc9a-131">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bdc9a-132">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="bdc9a-133">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bdc9a-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bdc9a-135">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bdc9a-136">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="bdc9a-137">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="bdc9a-138">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-138">The layout page</span></span>

<span data-ttu-id="bdc9a-139">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="bdc9a-140">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="bdc9a-141">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bdc9a-142">*Pages/Shared/_Layout.cshtml* ファイルを開いて確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bdc9a-143">[レイアウト](xref:mvc/views/layout) テンプレートを使用すると、HTML コンテナーのレイアウトを次のようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="bdc9a-144">1 つの場所で指定される。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-144">Specified in one place.</span></span>
* <span data-ttu-id="bdc9a-145">サイトの複数のページに適用される。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="bdc9a-146">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bdc9a-147">`RenderBody` は、ページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに "*ラップ*" されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bdc9a-148">たとえば、 **[プライバシー]** リンクを選択すると、`RenderBody` メソッド内で *Pages/Privacy.cshtml* ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bdc9a-149">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="bdc9a-149">ViewData and layout</span></span>

<span data-ttu-id="bdc9a-150">*Pages/Movies/Index.cshtml* ファイルの次のマークアップを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bdc9a-151">上の強調表示されたマークアップは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bdc9a-152">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bdc9a-153">`PageModel` 基底クラスには `ViewData` 辞書プロパティが含まれており、これを使用してビューにデータを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="bdc9a-154">オブジェクトは、\***キー/値** _ のパターンを使用して `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-154">Objects are added to the `ViewData` dictionary using a \***key value** _ pattern.</span></span> <span data-ttu-id="bdc9a-155">上のサンプルでは、`Title` プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bdc9a-156">`Title` プロパティは _Pages/Shared/_Layout.cshtml\* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-156">The `Title` property is used in the _Pages/Shared/_Layout.cshtml\* file.</span></span> <span data-ttu-id="bdc9a-157">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="bdc9a-158">`@*Markup removed for brevity.*@` 行は Razor コメントです。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="bdc9a-159">HTML コメント `<!-- -->` とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="bdc9a-160">詳細については、「[MDN Web ドキュメント: HTML の概要](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bdc9a-161">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="bdc9a-161">Update the layout</span></span>

1. <span data-ttu-id="bdc9a-162">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="bdc9a-163">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="bdc9a-164">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="bdc9a-165">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bdc9a-166">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bdc9a-167">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bdc9a-168">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bdc9a-169">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="bdc9a-170">変更内容を保存し、**RpMovie** リンクを選択してアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="bdc9a-171">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="bdc9a-172">**[Home]** 、 **[RpMovie]** 、 **[Create]** 、 **[Edit]** 、 **[Delete]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="bdc9a-173">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bdc9a-174">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bdc9a-175">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="bdc9a-176">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bdc9a-177">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bdc9a-178">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bdc9a-179">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="bdc9a-180">Create ページ モデル</span><span class="sxs-lookup"><span data-stu-id="bdc9a-180">The Create page model</span></span>

<span data-ttu-id="bdc9a-181">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bdc9a-182">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bdc9a-183">[Create] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bdc9a-184">このチュートリアルの後半では、状態を初期化する `OnGet` の例を示します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="bdc9a-185">`Page` メソッドでは、 *Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bdc9a-186">`Movie` プロパティでは [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bdc9a-187">[Create] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bdc9a-188">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bdc9a-189">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bdc9a-190">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bdc9a-191">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bdc9a-192">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bdc9a-193">モデル エラーがない場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-193">If there are no model errors:</span></span>

* <span data-ttu-id="bdc9a-194">データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-194">The data is saved.</span></span>
* <span data-ttu-id="bdc9a-195">ブラウザーは [Index] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-195">The browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="bdc9a-196">[Create] Razor ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-196">The Create Razor Page</span></span>

<span data-ttu-id="bdc9a-197">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bdc9a-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdc9a-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bdc9a-199">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで次のタグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml ページの VS17 ビュー](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bdc9a-201">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bdc9a-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bdc9a-202">上記のマークアップには、次のタグ ヘルパーが示されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="bdc9a-203">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bdc9a-204">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bdc9a-205">スキャフォールディング エンジンでは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bdc9a-206">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bdc9a-207">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bdc9a-208">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `[for]` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="bdc9a-209">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="bdc9a-210">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdc9a-211">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bdc9a-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="bdc9a-212">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次へ:データベースの操作](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bdc9a-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bdc9a-213">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bdc9a-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bdc9a-214">このチュートリアルでは、[前のチュートリアル](xref:tutorials/razor-pages/model)でスキャフォールディングによって作成された Razor ページについて説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-214">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

<span data-ttu-id="bdc9a-215">サンプルを[表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22)します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-215">[View or download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-no-loccreate-no-locdelete-details-and-edit-pages"></a><span data-ttu-id="bdc9a-216">[Create]、[Delete]、[詳細]、[編集] ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-216">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="bdc9a-217">*Pages/Movies/Index.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-217">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="bdc9a-218">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-218">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="bdc9a-219">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-219">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="bdc9a-220">コンストラクターは[依存性の注入](xref:fundamentals/dependency-injection)を使用して、`RazorPagesMovieContext` をページに追加します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-220">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="bdc9a-221">スキャフォールディングされたページでは、このパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-221">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="bdc9a-222">Entity Framework での非同期プログラミングの詳細については、「[非同期コード](xref:data/ef-rp/intro#asynchronous-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-222">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="bdc9a-223">ページに対して要求が行われると、`OnGetAsync` メソッドは Razor ページにムービーのリストを返します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-223">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="bdc9a-224">`OnGetAsync` または `OnGet` が Razor ページで呼び出され、ページの状態が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-224">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="bdc9a-225">この場合、`OnGetAsync` でムービーのリストを取得し、表示します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-225">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="bdc9a-226">`OnGet` から `void` が返される場合、または `OnGetAsync` から `Task` が返される場合、return メソッドは使用されません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-226">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="bdc9a-227">戻り値の型が `IActionResult` または `Task<IActionResult>` の場合は、return ステートメントを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-227">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="bdc9a-228">たとえば、*Pages/Movies/Create.cshtml.cs* `OnPostAsync` メソッドでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-228">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a><span data-ttu-id="bdc9a-229">次のように、*Pages/Movies/Index.cshtml* Razor ページを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-229">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="bdc9a-230">Razor では、HTML から C# または Razor 固有のマークアップに移行できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-230">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="bdc9a-231">`@` シンボルの後に [Razor で予約済みのキーワード](xref:mvc/views/razor#razor-reserved-keywords)が続いている場合は、Razor 固有のマークアップに移行します。それ以外の場合は、C# に移行します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-231">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="bdc9a-232">`@page` Razor ディレクティブを使うと、ファイルが MVC アクションになります。つまり、これで要求を処理できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-232">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="bdc9a-233">`@page` はページ上で最初の Razor ディレクティブである必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-233">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="bdc9a-234">`@page` は、Razor 固有のマークアップへの移行の例です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-234">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="bdc9a-235">詳細については、「[Razor の構文](xref:mvc/views/razor#razor-syntax)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-235">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="bdc9a-236">@model ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-236">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="bdc9a-237">`@model` ディレクティブは、Razor ページに渡されるモデルの型を指定します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-237">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="bdc9a-238">前の例の `@model` 行は、Razor ページで `PageModel` 派生クラスを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-238">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="bdc9a-239">モデルは、ページの `@Html.DisplayNameFor` および `@Html.DisplayFor` [HTML ヘルパーで](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-239">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="bdc9a-240">HTML ヘルパー</span><span class="sxs-lookup"><span data-stu-id="bdc9a-240">HTML Helpers</span></span>

<span data-ttu-id="bdc9a-241">次の HTML ヘルパーで使用されるラムダ式を確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-241">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="bdc9a-242"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML ヘルパーは、ラムダ式で参照される `Title` プロパティを検査し、表示名を判別します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-242">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="bdc9a-243">ラムダ式は評価されるのではなく検査されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-243">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="bdc9a-244">これは、`model`、`model.Movie`、または `model.Movie[0]` が `null` または空である場合、アクセス違反がないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-244">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="bdc9a-245">ラムダ式が (`@Html.DisplayFor(modelItem => item.Title)` などを使用して) 評価される場合は、モデルのプロパティ値が評価されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-245">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="bdc9a-246">レイアウト ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-246">The layout page</span></span>

<span data-ttu-id="bdc9a-247">メニューのリンク ( **[RazorPagesMovie]** 、 **[ホーム]** 、 **[プライバシー]** ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-247">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="bdc9a-248">各ページには同じメニューのレイアウトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-248">Each page shows the same menu layout.</span></span> <span data-ttu-id="bdc9a-249">メニューのレイアウトは、*Pages/Shared/_Layout.cshtml* ファイルに実装されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-249">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bdc9a-250">*Pages/Shared/_Layout.cshtml* ファイルを開いて確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-250">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="bdc9a-251">[[レイアウト]](xref:mvc/views/layout) テンプレートでは、1 か所でサイトの HTML コンテナー レイアウトを指定し、それをサイト内の複数のページに適用できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-251">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="bdc9a-252">`@RenderBody()` という行を見つけます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-252">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="bdc9a-253">`RenderBody` は、作成したページ固有のビューがすべて表示されるプレースホルダーで、レイアウト ページに *ラップ* されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-253">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="bdc9a-254">たとえば、 **[プライバシー]** リンクを選択した場合、`RenderBody` メソッド内で **Pages/Privacy.cshtml** ビューがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-254">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="bdc9a-255">ViewData とレイアウト</span><span class="sxs-lookup"><span data-stu-id="bdc9a-255">ViewData and layout</span></span>

<span data-ttu-id="bdc9a-256">*Pages/Movies/Index.cshtml* ファイルの次のコードを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-256">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="bdc9a-257">上の強調表示されたコードは、Razor の C# への移行例です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-257">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="bdc9a-258">`{` 文字と `}` 文字で C# コードのブロックを囲みます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-258">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="bdc9a-259">`PageModel` 基本クラスには `ViewData` 辞書プロパティがあり、これを使用して、ビューに渡すデータを追加できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-259">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="bdc9a-260">キー/値のパターンを使用して、`ViewData` 辞書にオブジェクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-260">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="bdc9a-261">上のサンプルでは、`Title` プロパティが `ViewData` 辞書に追加されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-261">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="bdc9a-262">`Title` プロパティは *Pages/Shared/_Layout.cshtml* ファイルで使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-262">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="bdc9a-263">次のマークアップは、 *_Layout.cshtml* ファイルの最初の数行を示しています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-263">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="bdc9a-264">`@*Markup removed for brevity.*@` 行は Razor コメントです。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-264">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="bdc9a-265">HTML コメント `<!-- -->` とは異なり、Razor コメントはクライアントには送信されません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-265">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="bdc9a-266">詳細については、「[MDN Web ドキュメント: HTML の概要](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-266">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="bdc9a-267">レイアウトの更新</span><span class="sxs-lookup"><span data-stu-id="bdc9a-267">Update the layout</span></span>

<span data-ttu-id="bdc9a-268">**RazorPagesMovie** ではなく **Movie** が表示されるように、*Pages/Shared/_Layout.cshtml* ファイルの `<title>` 要素を変更します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-268">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="bdc9a-269">*Pages/Shared/_Layout.cshtml* ファイルで次のアンカー要素を見つけます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-269">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="bdc9a-270">上の要素を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-270">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="bdc9a-271">上のアンカー要素は[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-271">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="bdc9a-272">この場合は、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-272">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="bdc9a-273">`asp-page="/Movies/Index"` タグ ヘルパー属性と値で、`/Movies/Index` Razor ページへのリンクが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-273">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="bdc9a-274">`asp-area` 属性の値が空なので、リンクではこの区分が使用されていません。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-274">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="bdc9a-275">詳細については、[区分](xref:mvc/controllers/areas)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-275">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="bdc9a-276">変更内容を保存し、**RpMovie** リンクをクリックしてアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-276">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="bdc9a-277">問題がある場合は、GitHub の [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) ファイルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-277">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="bdc9a-278">その他のリンク ( **[Home]** 、 **[RpMovie]** 、 **[Create]** 、 **[Edit]** 、 **[Delete]** ) をテストします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-278">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="bdc9a-279">各ページで、ブラウザー タブで表示できるタイトルを設定します。ページをブックマークすると、ブックマークでタイトルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-279">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="bdc9a-280">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-280">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="bdc9a-281">小数点にコンマ (",") を使い、英語 (米国) 以外の日付形式を使う英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-281">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="bdc9a-282">小数点のコンマの追加方法については、[こちらの GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-282">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="bdc9a-283">`Layout` プロパティは *Pages/_ViewStart.cshtml* ファイルで設定されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-283">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="bdc9a-284">上のマークアップでは、*Pages* フォルダーの下のすべての Razor ファイルに対して、レイアウト ファイルを *Pages/Shared/_Layout.cshtml* に設定します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-284">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="bdc9a-285">詳細については、「[Layout](xref:razor-pages/index#layout)」 (レイアウト) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-285">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-no-loccreate-page-model"></a><span data-ttu-id="bdc9a-286">Create ページ モデル</span><span class="sxs-lookup"><span data-stu-id="bdc9a-286">The Create page model</span></span>

<span data-ttu-id="bdc9a-287">*Pages/Movies/Create.cshtml.cs* ページ モデルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-287">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="bdc9a-288">`OnGet` メソッドは、ページに必要な状態を初期化します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-288">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="bdc9a-289">[Create] ページには初期化する状態はないため、`Page` が返されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-289">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="bdc9a-290">このチュートリアルで後ほど、`OnGet` メソッドの状態の初期化を確認できます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-290">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="bdc9a-291">`Page` メソッドでは、 *Create.cshtml* ページをレンダリングする `PageResult` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-291">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="bdc9a-292">`Movie` プロパティでは [BindProperty]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 属性を使用して、[モデル バインド](xref:mvc/models/model-binding)にオプトインします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-292">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="bdc9a-293">[Create] フォームでフォーム値が投稿されると、ASP.NET Core ランタイムが投稿された値を `Movie` モデルにバインドします。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-293">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="bdc9a-294">`OnPostAsync` メソッドは、ページでフォーム データが投稿されたときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-294">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="bdc9a-295">モデル エラーがある場合は、投稿されたフォーム データと共にフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-295">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="bdc9a-296">ほとんどのモデル エラーは、フォームが投稿される前にクライアント側でキャッチできます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-296">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="bdc9a-297">モデル エラーの例では、日付に変換できない日付フィールドの値が投稿されています。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-297">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="bdc9a-298">クライアント側の検証とモデルの検証については、チュートリアルの後半で説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-298">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="bdc9a-299">モデル エラーがない場合、データは保存され、ブラウザーは [Index] ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-299">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-no-loccreate-no-locrazor-page"></a><span data-ttu-id="bdc9a-300">[Create] Razor ページ</span><span class="sxs-lookup"><span data-stu-id="bdc9a-300">The Create Razor Page</span></span>

<span data-ttu-id="bdc9a-301">次のように、*Pages/Movies/Create.cshtml* Razor ページ ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-301">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="bdc9a-302">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdc9a-302">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bdc9a-303">Visual Studio に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-303">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml ページの VS17 ビュー](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="bdc9a-305">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bdc9a-305">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bdc9a-306">`<form method="post">` などのタグ ヘルパーについては、「[ASP.NET Core のタグ ヘルパー](xref:mvc/views/tag-helpers/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-306">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bdc9a-307">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bdc9a-307">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="bdc9a-308">Visual Studio for Mac に、タグ ヘルパーで使用される独特な太字のフォントで `<form method="post">` タグが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-308">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="bdc9a-309">`<form method="post">` 要素は[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)です。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-309">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="bdc9a-310">フォーム タグ ヘルパーには自動的に[偽造防止トークン](xref:security/anti-request-forgery)が含まれます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-310">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="bdc9a-311">スキャフォールディング エンジンでは、次のような、(ID を除く) モデルの各フィールドの Razor マークアップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-311">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="bdc9a-312">[検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` と `<span asp-validation-for`) には検証エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-312">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="bdc9a-313">検証については、後で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-313">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="bdc9a-314">[ラベル タグ ヘルパー](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) は、`Title` プロパティのラベル キャプションと `[for]` 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-314">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="bdc9a-315">[入力タグ ヘルパー](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) は [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。</span><span class="sxs-lookup"><span data-stu-id="bdc9a-315">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdc9a-316">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="bdc9a-316">Additional resources</span></span>

* [<span data-ttu-id="bdc9a-317">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="bdc9a-317">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="bdc9a-318">[前へ:モデルの追加](xref:tutorials/razor-pages/model)
> [次へ:データベースの操作](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="bdc9a-318">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
