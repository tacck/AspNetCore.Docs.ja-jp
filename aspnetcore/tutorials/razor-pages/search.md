---
title: パート 6、検索の追加
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 6。
ms.author: riande
ms.date: 12/05/2019
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
uid: tutorials/razor-pages/search
ms.openlocfilehash: 1e571966b78f93e29e7901dd9648fbe3aca52726
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855483"
---
# <a name="part-6-add-search-to-aspnet-core-no-locrazor-pages"></a><span data-ttu-id="52be2-103">パート 6、ASP.NET Core Razor ページへの検索の追加</span><span class="sxs-lookup"><span data-stu-id="52be2-103">Part 6, add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="52be2-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52be2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="52be2-105">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="52be2-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="52be2-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="52be2-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52be2-107">次のセクションでは、*ジャンル* または *名前* による映画検索が追加されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-107">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="52be2-108">強調表示されている次の using ステートメントとプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-108">Add the following highlighted using statement and properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=3,23,24,25,26,27)]

<span data-ttu-id="52be2-109">上のコードでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="52be2-109">In the previous code:</span></span>

* <span data-ttu-id="52be2-110">`SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-110">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="52be2-111">`SearchString` には、[`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性があります。</span><span class="sxs-lookup"><span data-stu-id="52be2-111">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="52be2-112">`[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-112">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="52be2-113">HTTP GET 要求でのバインドには `[BindProperty(SupportsGet = true)]` が必要です。</span><span class="sxs-lookup"><span data-stu-id="52be2-113">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="52be2-114">`Genres`: ジャンル一覧が含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-114">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="52be2-115">`Genres` により、ユーザーは一覧からジャンルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="52be2-115">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="52be2-116">`SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。</span><span class="sxs-lookup"><span data-stu-id="52be2-116">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="52be2-117">`MovieGenre`: ユーザーが選択する特定のジャンルが含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-117">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="52be2-118">たとえば、"Western (西部劇)" です。</span><span class="sxs-lookup"><span data-stu-id="52be2-118">For example, "Western".</span></span>
* <span data-ttu-id="52be2-119">`Genres` と `MovieGenre` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="52be2-119">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="52be2-120">Index ページの `OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-120">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="52be2-121">`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-121">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="52be2-122">このクエリはこの時点では "***定義される** _" だけで、データベースに対して実行されて "_*_いません_\*_"。</span><span class="sxs-lookup"><span data-stu-id="52be2-122">The query is only ***defined** _ at this point, it has _*_not_\*_ been run against the database.</span></span>

<span data-ttu-id="52be2-123">`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-123">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="52be2-124">`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="52be2-124">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="52be2-125">ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-125">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="52be2-126">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="52be2-126">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="52be2-127">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-127">Rather, query execution is deferred.</span></span> <span data-ttu-id="52be2-128">その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-128">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="52be2-129">詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52be2-129">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="52be2-130">[Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-130">The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="52be2-131">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="52be2-131">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="52be2-132">SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-132">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="52be2-133">SQLite では、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-133">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="52be2-134">ムービー ページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-134">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="52be2-135">たとえば、「 `https://localhost:5001/Movies?searchString=Ghost` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="52be2-135">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="52be2-136">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-136">The filtered movies are displayed.</span></span>

![Indexビュー](search/_static/ghost.png)

<span data-ttu-id="52be2-138">次のルート テンプレートが Index ページに追加される場合、検索文字列を URL セグメントとして渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="52be2-138">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="52be2-139">たとえば、「 `https://localhost:5001/Movies/Ghost` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="52be2-139">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="52be2-140">先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。</span><span class="sxs-lookup"><span data-stu-id="52be2-140">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="52be2-141">`"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。</span><span class="sxs-lookup"><span data-stu-id="52be2-141">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![ghost という単語が URL に追加されたIndexビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービー リストが返されています。](search/_static/g2.png)

<span data-ttu-id="52be2-143">ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-143">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="52be2-144">モデル バインドでは、大文字と小文字が区別 "_\*_されません_\*_"。</span><span class="sxs-lookup"><span data-stu-id="52be2-144">Model binding is _*_not_*_ case sensitive.</span></span>

<span data-ttu-id="52be2-145">ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。</span><span class="sxs-lookup"><span data-stu-id="52be2-145">However, users cannot be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="52be2-146">この手順では、ムービーを絞り込むための UI を追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-146">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="52be2-147">ルート制約 `"{searchString?}"` を追加した場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="52be2-147">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="52be2-148">_Pages/Movies/Index.cshtml\* ファイルを開き、次のコードで強調表示されているマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-148">Open the _Pages/Movies/Index.cshtml\* file, and add the markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="52be2-149">HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-149">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="52be2-150">[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="52be2-150">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="52be2-151">フォームが提出されると、フィルター文字列がクエリ文字列経由で *ページ/ムービー/Index* ページに送信されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-151">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="52be2-152">入力タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="52be2-152">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="52be2-153">変更を保存し、フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="52be2-153">Save the changes and test the filter.</span></span>

![タイトル フィルター テキストボックスに ghost という単語が入力されたIndexビュー](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="52be2-155">ジャンルで検索する</span><span class="sxs-lookup"><span data-stu-id="52be2-155">Search by genre</span></span>

<span data-ttu-id="52be2-156">Index ページの `OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-156">Update the Index page's `OnGetAsync` method with the following code:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="52be2-157">次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。</span><span class="sxs-lookup"><span data-stu-id="52be2-157">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="52be2-158">ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-158">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="52be2-159">ジャンル検索を Razor ページに追加する</span><span class="sxs-lookup"><span data-stu-id="52be2-159">Add search by genre to the Razor Page</span></span>

1. <span data-ttu-id="52be2-160">次のマークアップで強調表示されているように、 *Index.cshtml* [`<form>` 要素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ) を更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-160">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

1. <span data-ttu-id="52be2-161">ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="52be2-161">Test the app by searching by genre, by movie title, and by both.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52be2-162">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="52be2-162">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="52be2-163">[前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次へ新しいフィールドの追加](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="52be2-163">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52be2-164">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="52be2-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="52be2-165">次のセクションでは、*ジャンル* または *名前* による映画検索が追加されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-165">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="52be2-166">強調表示されている次のプロパティを *Pages/Movies/Index.cshtml.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-166">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="52be2-167">`SearchString`: ユーザーが検索テキスト ボックスに入力したテキストが含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-167">`SearchString`: Contains the text users enter in the search text box.</span></span> <span data-ttu-id="52be2-168">`SearchString` には、[`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 属性があります。</span><span class="sxs-lookup"><span data-stu-id="52be2-168">`SearchString` has the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute.</span></span> <span data-ttu-id="52be2-169">`[BindProperty]` により、プロパティと同じ名前に基づきフォーム値とクエリ文字列がバインドされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-169">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="52be2-170">HTTP GET 要求でのバインドには `[BindProperty(SupportsGet = true)]` が必要です。</span><span class="sxs-lookup"><span data-stu-id="52be2-170">`[BindProperty(SupportsGet = true)]` is required for binding on HTTP GET requests.</span></span>
* <span data-ttu-id="52be2-171">`Genres`: ジャンル一覧が含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-171">`Genres`: Contains the list of genres.</span></span> <span data-ttu-id="52be2-172">`Genres` により、ユーザーは一覧からジャンルを選択できます。</span><span class="sxs-lookup"><span data-stu-id="52be2-172">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="52be2-173">`SelectList` には `using Microsoft.AspNetCore.Mvc.Rendering;` が必要です。</span><span class="sxs-lookup"><span data-stu-id="52be2-173">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="52be2-174">`MovieGenre`: ユーザーが選択する特定のジャンルが含まれる。</span><span class="sxs-lookup"><span data-stu-id="52be2-174">`MovieGenre`: Contains the specific genre the user selects.</span></span> <span data-ttu-id="52be2-175">たとえば、"Western (西部劇)" です。</span><span class="sxs-lookup"><span data-stu-id="52be2-175">For example, "Western".</span></span>
* <span data-ttu-id="52be2-176">`Genres` と `MovieGenre` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="52be2-176">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="52be2-177">Index ページの `OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-177">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="52be2-178">`OnGetAsync` メソッドの最初の行により、ムービーを選択する [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-178">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="52be2-179">このクエリはこの時点では定義される *だけ* で、データベースに対して **実行されていません**。</span><span class="sxs-lookup"><span data-stu-id="52be2-179">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="52be2-180">`SearchString` プロパティが null でも空でもない場合、検索文字列で絞り込むようにムービークエリが変更されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-180">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="52be2-181">`s => s.Title.Contains()` コードは[ラムダ式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)です。</span><span class="sxs-lookup"><span data-stu-id="52be2-181">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="52be2-182">ラムダは、メソッド ベースの [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) クエリで、[Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) メソッドや `Contains` など、標準クエリ演算子メソッドの引数として使用されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-182">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains`.</span></span> <span data-ttu-id="52be2-183">LINQ クエリは、`Where`、`Contains`、`OrderBy` などのメソッドの呼び出しで定義または変更されたときには実行されません。</span><span class="sxs-lookup"><span data-stu-id="52be2-183">LINQ queries are not executed when they're defined or when they're modified by calling a method, such as `Where`, `Contains`  or `OrderBy`.</span></span> <span data-ttu-id="52be2-184">クエリ実行は先送りされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-184">Rather, query execution is deferred.</span></span> <span data-ttu-id="52be2-185">その具体値が繰り返されるか、`ToListAsync` メソッドが呼び出されるまで、式の評価が延ばされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-185">The evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="52be2-186">詳細については、「[クエリ実行](/dotnet/framework/data/adonet/ef/language-reference/query-execution)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="52be2-186">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="52be2-187">**注:** [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) メソッドは C# コードではなく、データベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-187">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="52be2-188">クエリの大文字と小文字の区別は、データベースや照合順序に依存します。</span><span class="sxs-lookup"><span data-stu-id="52be2-188">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="52be2-189">SQL Server では、`Contains` は大文字/小文字の区別がない [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) にマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="52be2-189">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="52be2-190">SQLite では、既定の照合順序で、大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-190">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="52be2-191">ムービー ページに移動し、`?searchString=Ghost` のようなクエリ文字列を URL に追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-191">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="52be2-192">たとえば、「 `https://localhost:5001/Movies?searchString=Ghost` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="52be2-192">For example, `https://localhost:5001/Movies?searchString=Ghost`.</span></span> <span data-ttu-id="52be2-193">フィルターされたムービーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-193">The filtered movies are displayed.</span></span>

![Indexビュー](search/_static/ghost.png)

<span data-ttu-id="52be2-195">次のルート テンプレートが Index ページに追加される場合、検索文字列を URL セグメントとして渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="52be2-195">If the following route template is added to the Index page, the search string can be passed as a URL segment.</span></span> <span data-ttu-id="52be2-196">たとえば、「 `https://localhost:5001/Movies/Ghost` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="52be2-196">For example, `https://localhost:5001/Movies/Ghost`.</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="52be2-197">先のルート制約では、クエリ文字列値の代わりに、ルート データ (URL セグメント) として題名を検索できます。</span><span class="sxs-lookup"><span data-stu-id="52be2-197">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="52be2-198">`"{searchString?}"` の `?` は、これが任意のルート パラメーターであることを意味します。</span><span class="sxs-lookup"><span data-stu-id="52be2-198">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![ghost という単語が URL に追加されたIndexビュー。Ghostbusters と Ghostbusters 2 という 2 本のムービーからなるムービー リストが返されています。](search/_static/g2.png)

<span data-ttu-id="52be2-200">ASP.NET Core ランタイムでは[モデル バインド](xref:mvc/models/model-binding)を使用し、クエリ文字列 (`?searchString=Ghost`) またはルート データ (`https://localhost:5001/Movies/Ghost`) から `SearchString` プロパティの値が設定されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-200">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="52be2-201">モデル バインドでは、\**_大文字と小文字が区別されません_*。</span><span class="sxs-lookup"><span data-stu-id="52be2-201">Model binding is \**_not_* _ case sensitive.</span></span>

<span data-ttu-id="52be2-202">ただし、URL を変更してムービーを検索することをユーザーに求めることはできません。</span><span class="sxs-lookup"><span data-stu-id="52be2-202">However, users can't be expected to modify the URL to search for a movie.</span></span> <span data-ttu-id="52be2-203">この手順では、ムービーを絞り込むための UI を追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-203">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="52be2-204">ルート制約 `"{searchString?}"` を追加した場合、それを削除します。</span><span class="sxs-lookup"><span data-stu-id="52be2-204">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="52be2-205">_Pages/Movies/Index.cshtml\* ファイルを開き、次のコードで強調表示されている `<form>` マークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="52be2-205">Open the _Pages/Movies/Index.cshtml\* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="52be2-206">HTML `<form>` タグでは、次の[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)が使用されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-206">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="52be2-207">[フォーム タグ ヘルパー](xref:mvc/views/working-with-forms#the-form-tag-helper)</span><span class="sxs-lookup"><span data-stu-id="52be2-207">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="52be2-208">フォームが提出されると、フィルター文字列がクエリ文字列経由で *ページ/ムービー/Index* ページに送信されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-208">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="52be2-209">入力タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="52be2-209">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="52be2-210">変更を保存し、フィルターをテストします。</span><span class="sxs-lookup"><span data-stu-id="52be2-210">Save the changes and test the filter.</span></span>

![タイトル フィルター テキストボックスに ghost という単語が入力されたIndexビュー](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="52be2-212">ジャンルで検索する</span><span class="sxs-lookup"><span data-stu-id="52be2-212">Search by genre</span></span>

<span data-ttu-id="52be2-213">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-213">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="52be2-214">次のコードは、データベースからすべてのジャンルを取得する LINQ クエリです。</span><span class="sxs-lookup"><span data-stu-id="52be2-214">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="52be2-215">ジャンルの `SelectList` は、別個のジャンルを推定することで作成されます。</span><span class="sxs-lookup"><span data-stu-id="52be2-215">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-no-locrazor-page"></a><span data-ttu-id="52be2-216">ジャンル検索を Razor ページに追加する</span><span class="sxs-lookup"><span data-stu-id="52be2-216">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="52be2-217">次のマークアップで強調表示されているように、 *Index.cshtml* [`<form>` 要素] (https://developer.mozilla.org/docs/Web/HTML/Element/form) ) を更新します。</span><span class="sxs-lookup"><span data-stu-id="52be2-217">Update the *Index.cshtml* [`<form>` element] (https://developer.mozilla.org/docs/Web/HTML/Element/form) as highlighted in the following markup:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="52be2-218">ジャンルまたはムービーのタイトル、あるいはその両方で検索して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="52be2-218">Test the app by searching by genre, by movie title, and by both.</span></span>
<span data-ttu-id="52be2-219">前のコードでは、[選択タグ ヘルパー](xref:mvc/views/working-with-forms#the-select-tag-helper)とオプション タグ ヘルパーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="52be2-219">The preceding code uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper) and Option Tag Helper.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="52be2-220">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="52be2-220">Additional resources</span></span>

* [<span data-ttu-id="52be2-221">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="52be2-221">YouTube version of this tutorial</span></span>](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> <span data-ttu-id="52be2-222">[前へ:ページの更新](xref:tutorials/razor-pages/da1)
> [次へ新しいフィールドの追加](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="52be2-222">[Previous: Update the pages](xref:tutorials/razor-pages/da1)
[Next: Add a new field](xref:tutorials/razor-pages/new-field)</span></span>

::: moniker-end
