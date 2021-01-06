---
title: パート 5、生成されたページの更新
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 5。
ms.author: riande
ms.date: 09/20/2020
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
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 46fbfb50afd03f918f9e02bcc8c1dbde9a080ca4
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485941"
---
# <a name="part-5-update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="b7248-103">パート 5、ASP.NET Core アプリでの生成済みページの更新</span><span class="sxs-lookup"><span data-stu-id="b7248-103">Part 5, update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="b7248-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7248-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7248-105">スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="b7248-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="b7248-106">**ReleaseDate** は 2 つの単語 (**Release Date**) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b7248-106">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Chrome で開かれているムービー アプリケーション](sql/_static/5/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="b7248-108">生成されたコードの更新</span><span class="sxs-lookup"><span data-stu-id="b7248-108">Update the generated code</span></span>

<span data-ttu-id="b7248-109">*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="b7248-110">上のコードでは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b7248-110">In the previous code:</span></span>

* <span data-ttu-id="b7248-111">`[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="b7248-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="b7248-112">詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7248-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>
* <span data-ttu-id="b7248-113">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) 属性では、フィールドの表示名を指定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-113">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies the display name of a field.</span></span> <span data-ttu-id="b7248-114">上のコードでは、"ReleaseDate" ではなく、"Release Date" を指定しています。</span><span class="sxs-lookup"><span data-stu-id="b7248-114">In the preceding code, "Release Date" instead of "ReleaseDate".</span></span>
* <span data-ttu-id="b7248-115">[[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-115">The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="b7248-116">フィールドに格納されている時刻情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="b7248-116">The time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="b7248-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。</span><span class="sxs-lookup"><span data-stu-id="b7248-117">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span>

<span data-ttu-id="b7248-118">*Pages/Movies* を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-118">Browse to *Pages/Movies* and hover over an **Edit** link to see the target URL.</span></span>

![[編集] リンクがマウスでポイントされ、リンク URL として https://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="b7248-120">**[Edit]\(編集\)** 、 **[Details]\(詳細\)** 、および **[Delete]\(削除\)** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-120">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="b7248-121">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="b7248-121">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="b7248-122">上のコードでは、[アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)で動的に Razor ページから HTML `href` 属性値 (ルートは相対)、`asp-page`、ルート ID (`asp-route-id`) が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-122">In the preceding code, the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route identifier (`asp-route-id`).</span></span> <span data-ttu-id="b7248-123">詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b7248-123">For more information, see [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages).</span></span>

<span data-ttu-id="b7248-124">ブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-124">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="b7248-125">生成された HTML の部分を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="b7248-125">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

   <span data-ttu-id="b7248-126">動的に生成されたリンクでは、クエリ文字列を含むムービー ID が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-126">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="b7248-127">たとえば、`https://localhost:5001/Movies/Details?id=1` の `?id=1` です。</span><span class="sxs-lookup"><span data-stu-id="b7248-127">For example, the `?id=1` in `https://localhost:5001/Movies/Details?id=1`.</span></span>

### <a name="add-route-template"></a><span data-ttu-id="b7248-128">ルート テンプレートの追加</span><span class="sxs-lookup"><span data-stu-id="b7248-128">Add route template</span></span>

<span data-ttu-id="b7248-129">`{id:int}` ルート テンプレートを使用するには、[編集]、[詳細]、[削除] の Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="b7248-129">Update the Edit, Details, and Delete Razor Pages to use the `{id:int}` route template.</span></span> <span data-ttu-id="b7248-130">これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b7248-130">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="b7248-131">アプリを実行してから、ソースを表示します。</span><span class="sxs-lookup"><span data-stu-id="b7248-131">Run the app and then view source.</span></span>

<span data-ttu-id="b7248-132">生成される HTML では、次のように URL のパス部分に ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-132">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="b7248-133">整数を **含まない**、`{id:int}` ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-133">A request to the page with the `{id:int}` route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="b7248-134">たとえば、`https://localhost:5001/Movies/Details` の場合は 404 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-134">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="b7248-135">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-135">To make the ID optional, append `?` to the route constraint:</span></span>

```cshtml
@page "{id:int?}"
```

<span data-ttu-id="b7248-136">`@page "{id:int?}"` の動作をテストするには:</span><span class="sxs-lookup"><span data-stu-id="b7248-136">Test the behavior of `@page "{id:int?}"`:</span></span>

1. <span data-ttu-id="b7248-137">*Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-137">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
1. <span data-ttu-id="b7248-138">*Pages/Movies/Details.cshtml.cs* で、`public async Task<IActionResult> OnGetAsync(int? id)` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-138">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
1. <span data-ttu-id="b7248-139">`https://localhost:5001/Movies/Details/` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7248-139">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="b7248-140">`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。</span><span class="sxs-lookup"><span data-stu-id="b7248-140">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="b7248-141">ルーティング エンジンは、HTTP 404 を返します。</span><span class="sxs-lookup"><span data-stu-id="b7248-141">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="b7248-142">`@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドから `NotFound` (HTTP 404) が返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-142">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="b7248-143">コンカレンシーの例外処理の確認</span><span class="sxs-lookup"><span data-stu-id="b7248-143">Review concurrency exception handling</span></span>

<span data-ttu-id="b7248-144">*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-144">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="b7248-145">上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。</span><span class="sxs-lookup"><span data-stu-id="b7248-145">The previous code detects concurrency exceptions when one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="b7248-146">`catch` ブロックをテストするには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="b7248-146">To test the `catch` block:</span></span>

1. <span data-ttu-id="b7248-147">`catch (DbUpdateConcurrencyException)` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-147">Set a breakpoint on `catch (DbUpdateConcurrencyException)`.</span></span>
1. <span data-ttu-id="b7248-148">ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。</span><span class="sxs-lookup"><span data-stu-id="b7248-148">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
1. <span data-ttu-id="b7248-149">別のブラウザー ウィンドウで、同じムービーの **[削除]** リンクを選択してから、ムービーを削除します。</span><span class="sxs-lookup"><span data-stu-id="b7248-149">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
1. <span data-ttu-id="b7248-150">前のブラウザー ウィンドウで、ムービーに変更を投稿します。</span><span class="sxs-lookup"><span data-stu-id="b7248-150">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="b7248-151">実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b7248-151">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="b7248-152">詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7248-152">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="b7248-153">レビューの投稿とバインディング</span><span class="sxs-lookup"><span data-stu-id="b7248-153">Posting and binding review</span></span>

<span data-ttu-id="b7248-154">*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-154">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/SnapShots/Edit.cshtml.cs?name=snippet2)]

<span data-ttu-id="b7248-155">HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="b7248-155">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="b7248-156">`OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。</span><span class="sxs-lookup"><span data-stu-id="b7248-156">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span>
* <span data-ttu-id="b7248-157">`Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b7248-157">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="b7248-158">*Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b7248-158">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="b7248-159">[編集] フォームには、ムービーからの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-159">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="b7248-160">Movies/Edit ページが投稿された場合:</span><span class="sxs-lookup"><span data-stu-id="b7248-160">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="b7248-161">ページのフォーム値は `Movie` プロパティにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b7248-161">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="b7248-162">`[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="b7248-162">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="b7248-163">モデルの状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、送信された値を含むフォームが再表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-163">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is redisplayed with the submitted values.</span></span>
* <span data-ttu-id="b7248-164">モデル エラーがない場合、ムービーは保存されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-164">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="b7248-165">Index、[Create]/(作成/)、[Delete]\(削除\) Razor ページの HTTP GET メソッドも同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b7248-165">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="b7248-166">[作成] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b7248-166">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7248-167">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="b7248-167">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b7248-168">[前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次へ:検索の追加](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="b7248-168">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7248-169">スキャフォールディングされたムービー アプリは上々の滑り出しでしたが、表示が理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="b7248-169">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="b7248-170">**ReleaseDate** は 2 つの単語 (**Release Date**) にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b7248-170">**ReleaseDate** should be two words, **Release Date**.</span></span>

![Chrome で開かれているムービー アプリケーション](sql/_static/m55https.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="b7248-172">生成されたコードの更新</span><span class="sxs-lookup"><span data-stu-id="b7248-172">Update the generated code</span></span>

<span data-ttu-id="b7248-173">*Models/Movie.cs* ファイルを開き、下のコードで強調表示されている行を追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-173">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=3,12,17)]

<span data-ttu-id="b7248-174">`[Column(TypeName = "decimal(18, 2)")]` データ注釈により、Entity Framework Core でデータベースの通貨と `Price` が正しくマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="b7248-174">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="b7248-175">詳細については、「[Data Types](/ef/core/modeling/relational/data-types)」(データ型) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7248-175">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="b7248-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) については、次のチュートリアルで説明します。</span><span class="sxs-lookup"><span data-stu-id="b7248-176">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="b7248-177">[[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) 属性では、フィールドの名前として表示する内容 (ここでは、"ReleaseDate" ではなく、"Release Date") を指定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-177">The [[Display]](xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DisplayMetadata) attribute specifies what to display for the name of a field, in this case "Release Date" instead of "ReleaseDate".</span></span> <span data-ttu-id="b7248-178">[DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性ではデータ型 (`Date`) を指定するため、フィールドに格納される時刻情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="b7248-178">The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="b7248-179">Pages/Movies を参照し、 **[編集]** リンクをポイントしてターゲット URL を確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-179">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![[編集] リンクがマウスでポイントされ、リンク URL として http://localhost:1234/Movies/Edit/5 が表示されている状態のブラウザー ウィンドウ](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="b7248-181">**[Edit]\(編集\)** 、 **[Details]\(詳細\)** 、および **[Delete]\(削除\)** の各リンクは、*Pages/Movies/Index.cshtml* ファイルで [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-181">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="b7248-182">[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="b7248-182">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="b7248-183">上のコードでは、`AnchorTagHelper` で動的に Razor ページから HTML `href` 属性値 (ルートは相対)、`asp-page`、ルート ID (`asp-route-id`) が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-183">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`, and the route id (`asp-route-id`).</span></span> <span data-ttu-id="b7248-184">詳細については、「[ページの URL の生成](xref:razor-pages/index#url-generation-for-pages)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7248-184">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="b7248-185">ブラウザーから **[ソースの表示]** を使用して、生成されたマークアップを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-185">Use **View Source** from a browser to examine the generated markup.</span></span> <span data-ttu-id="b7248-186">生成された HTML の部分を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="b7248-186">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="b7248-187">動的に生成されたリンクでは、クエリ文字列を含むムービー ID が渡されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-187">The dynamically generated links pass the movie ID with a query string.</span></span> <span data-ttu-id="b7248-188">たとえば、`https://localhost:5001/Movies/Details?id=1` の `?id=1` です。</span><span class="sxs-lookup"><span data-stu-id="b7248-188">For example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`.</span></span>

<span data-ttu-id="b7248-189">"{id:int}" ルート テンプレートを使用するには、[編集]、[詳細]、[削除] Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="b7248-189">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="b7248-190">これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b7248-190">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="b7248-191">アプリを実行してから、ソースを表示します。</span><span class="sxs-lookup"><span data-stu-id="b7248-191">Run the app and then view source.</span></span> <span data-ttu-id="b7248-192">生成される HTML では、次のように URL のパス部分に ID を追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-192">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="b7248-193">整数を **含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-193">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="b7248-194">たとえば、`https://localhost:5001/Movies/Details` の場合は 404 エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-194">For example, `https://localhost:5001/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="b7248-195">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-195">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="b7248-196">`@page "{id:int?}"` の動作をテストするには</span><span class="sxs-lookup"><span data-stu-id="b7248-196">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="b7248-197">*Pages/Movies/Details.cshtml* の page ディレクティブを `@page "{id:int?}"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-197">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="b7248-198">*Pages/Movies/Details.cshtml.cs* で、`public async Task<IActionResult> OnGetAsync(int? id)` にブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="b7248-198">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)`, in *Pages/Movies/Details.cshtml.cs*.</span></span>
* <span data-ttu-id="b7248-199">`https://localhost:5001/Movies/Details/` に移動します。</span><span class="sxs-lookup"><span data-stu-id="b7248-199">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="b7248-200">`@page "{id:int}"` ディレクティブでは、ブレークポイントがヒットすることはありません。</span><span class="sxs-lookup"><span data-stu-id="b7248-200">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="b7248-201">ルーティング エンジンは、HTTP 404 を返します。</span><span class="sxs-lookup"><span data-stu-id="b7248-201">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="b7248-202">`@page "{id:int?}"` を使用すると、`OnGetAsync` メソッドから `NotFound` (HTTP 404) が返されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-202">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404):</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Details.cshtml.cs?name=snippet1&highlight=10-13)]

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="b7248-203">コンカレンシーの例外処理の確認</span><span class="sxs-lookup"><span data-stu-id="b7248-203">Review concurrency exception handling</span></span>

<span data-ttu-id="b7248-204">*Pages/Movies/Edit.cshtml.cs* ファイルで `OnPostAsync` メソッドを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-204">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="b7248-205">上のコードでは、一方のクライアントがムービーを削除し、もう一方のクライアントがムービーに変更を投稿した場合に、コンカレンシーの例外を検出します。</span><span class="sxs-lookup"><span data-stu-id="b7248-205">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="b7248-206">`catch` ブロックをテストするには、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="b7248-206">To test the `catch` block:</span></span>

* <span data-ttu-id="b7248-207">`catch (DbUpdateConcurrencyException)` へのブレークポイントの設定</span><span class="sxs-lookup"><span data-stu-id="b7248-207">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="b7248-208">ムービーの **[編集]** を選択し、変更を行います。ただし、 **[保存]** はしないでください。</span><span class="sxs-lookup"><span data-stu-id="b7248-208">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="b7248-209">別のブラウザー ウィンドウで、同じムービーの **[削除]** リンクを選択してから、ムービーを削除します。</span><span class="sxs-lookup"><span data-stu-id="b7248-209">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="b7248-210">前のブラウザー ウィンドウで、ムービーに変更を投稿します。</span><span class="sxs-lookup"><span data-stu-id="b7248-210">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="b7248-211">実稼働環境のコードが、コンカレンシーの競合を検出する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b7248-211">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="b7248-212">詳細については、[コンカレンシーの競合の処理](xref:data/ef-rp/concurrency)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b7248-212">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="b7248-213">レビューの投稿とバインディング</span><span class="sxs-lookup"><span data-stu-id="b7248-213">Posting and binding review</span></span>

<span data-ttu-id="b7248-214">*Pages/Movies/Edit.cshtml.cs* ファイルを確認します。</span><span class="sxs-lookup"><span data-stu-id="b7248-214">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="b7248-215">HTTP GET 要求が Movies/Edit ページに対して行われた場合 (例: `https://localhost:5001/Movies/Edit/3`):</span><span class="sxs-lookup"><span data-stu-id="b7248-215">When an HTTP GET request is made to the Movies/Edit page, for example, `https://localhost:5001/Movies/Edit/3`:</span></span>

* <span data-ttu-id="b7248-216">`OnGetAsync` メソッドはデータベースからムービーをフェッチし、`Page` メソッドを返します。</span><span class="sxs-lookup"><span data-stu-id="b7248-216">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="b7248-217">`Page` メソッドは *Pages/Movies/Edit.cshtml* Razor ページをレンダリングします。</span><span class="sxs-lookup"><span data-stu-id="b7248-217">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="b7248-218">*Pages/Movies/Edit.cshtml* ファイルにはモデルのディレクティブ (`@model RazorPagesMovie.Pages.Movies.EditModel`) が含まれています。これにより、ページでムービー モデルが使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b7248-218">The *Pages/Movies/Edit.cshtml* file contains the model directive `@model RazorPagesMovie.Pages.Movies.EditModel`, which makes the movie model available on the page.</span></span>
* <span data-ttu-id="b7248-219">[編集] フォームには、ムービーからの値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-219">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="b7248-220">Movies/Edit ページが投稿された場合:</span><span class="sxs-lookup"><span data-stu-id="b7248-220">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="b7248-221">ページのフォーム値は `Movie` プロパティにバインドされます。</span><span class="sxs-lookup"><span data-stu-id="b7248-221">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="b7248-222">`[BindProperty]` 属性により、[モデル バインド](xref:mvc/models/model-binding)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="b7248-222">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="b7248-223">モデル状態にエラーがある (たとえば、`ReleaseDate` を日付に変換できない) 場合、フォームは送信された値で表示されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-223">If there are errors in the model state, for example, `ReleaseDate` cannot be converted to a date, the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="b7248-224">モデル エラーがない場合、ムービーは保存されます。</span><span class="sxs-lookup"><span data-stu-id="b7248-224">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="b7248-225">Index、[Create]/(作成/)、[Delete]\(削除\) Razor ページの HTTP GET メソッドも同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b7248-225">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="b7248-226">[作成] Razor ページの HTTP POST `OnPostAsync` メソッドも [編集] Razor ページの `OnPostAsync` メソッドと同様のパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="b7248-226">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="b7248-227">次のチュートリアルでは検索を追加します。</span><span class="sxs-lookup"><span data-stu-id="b7248-227">Search is added in the next tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7248-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b7248-228">Additional resources</span></span>

* [<span data-ttu-id="b7248-229">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="b7248-229">YouTube version of this tutorial</span></span>](https://youtu.be/yLnnleREMtQ)

> [!div class="step-by-step"]
> <span data-ttu-id="b7248-230">[前へ:データベースの操作](xref:tutorials/razor-pages/sql)
> [次へ:検索の追加](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="b7248-230">[Previous: Work with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>

::: moniker-end
