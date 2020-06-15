---
title: パート 6、ASP.NET Core の Razor ページと EF Core - 関連データの読み込み
author: rick-anderson
description: Razor ページと Entity Framework チュートリアル シリーズのパート 6。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e67738015f64ca7077c2f87a8f7eabe722aac9d8
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652616"
---
# <a name="part-6-razor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="7224a-103">パート 6、ASP.NET Core の Razor ページと EF Core - 関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="7224a-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7224a-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7224a-105">このチュートリアルでは、関連データを読み取って表示する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="7224a-106">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="7224a-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="7224a-107">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="7224a-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="7224a-110">一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="7224a-111">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="7224a-112">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="7224a-113">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="7224a-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="7224a-114">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="7224a-115">これは通常、必要なデータをすべて取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="7224a-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="7224a-116">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7224a-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="7224a-117">複数のクエリを発行することは、1 つの巨大なクエリよりも効率的である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="7224a-118">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="7224a-120">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="7224a-121">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="7224a-121">One query for the main query</span></span> 
  * <span data-ttu-id="7224a-122">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="7224a-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="7224a-123">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="7224a-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="7224a-124">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7224a-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="7224a-125">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="7224a-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="7224a-127">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="7224a-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7224a-128">ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="7224a-129">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="7224a-130">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7224a-131">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="7224a-132">分離したクエリによる明示的読み込みにより、複数のクエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="7224a-133">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="7224a-134">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="7224a-135">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-135">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="7224a-137">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7224a-138">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7224a-139">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7224a-140">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="7224a-141">初めてナビゲーション プロパティにアクセスされるたびに、クエリがデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="7224a-142">Course ページの作成</span><span class="sxs-lookup"><span data-stu-id="7224a-142">Create Course pages</span></span>

<span data-ttu-id="7224a-143">`Course` エンティティには、関連する `Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7224a-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="7224a-145">コースに割り当てられている部署の名前を表示するには</span><span class="sxs-lookup"><span data-stu-id="7224a-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="7224a-146">関連する `Department` エンティティを `Course.Department` ナビゲーション プロパティに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="7224a-147">`Department` エンティティの `Name` プロパティから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="7224a-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="7224a-148">Course ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="7224a-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7224a-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7224a-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7224a-150">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="7224a-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="7224a-151">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="7224a-152">モデル クラスに `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="7224a-153">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7224a-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7224a-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7224a-155">*Pages/Courses* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="7224a-156">次のコマンドを実行して、Course モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="7224a-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="7224a-157">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="7224a-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="7224a-158">**Linux または macOS の場合:**</span><span class="sxs-lookup"><span data-stu-id="7224a-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="7224a-159">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="7224a-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="7224a-160">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="7224a-161">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="7224a-162">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="7224a-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="7224a-163">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="7224a-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="7224a-164">部署名の表示</span><span class="sxs-lookup"><span data-stu-id="7224a-164">Display the department name</span></span>

<span data-ttu-id="7224a-165">次のコードを使用して Pages/Courses/Index.cshtml.cs を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="7224a-166">上記のコードでは、`Course` プロパティを `Courses` に変更し、`AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="7224a-167">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="7224a-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="7224a-168">エンティティは現在のコンテキストでは更新されないため、追跡する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7224a-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="7224a-169">次のコードを使用して *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="7224a-170">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="7224a-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="7224a-171">`Course` プロパティ名は `Courses` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="7224a-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="7224a-172">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="7224a-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="7224a-173">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="7224a-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="7224a-174">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="7224a-175">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="7224a-176">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="7224a-177">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="7224a-179">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-179">Loading related data with Select</span></span>

<span data-ttu-id="7224a-180">`OnGetAsync` メソッドでは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="7224a-181">`Select` メソッドは、必要な関連データだけを読み込む代替手段です。</span><span class="sxs-lookup"><span data-stu-id="7224a-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="7224a-182">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="7224a-183">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="7224a-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="7224a-184">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="7224a-185">上記のコードではエンティティ型が返されないため、追跡は行われません。</span><span class="sxs-lookup"><span data-stu-id="7224a-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="7224a-186">EF の追跡の詳細については、「[追跡と追跡なしのクエリ](/ef/core/querying/tracking)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="7224a-187">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="7224a-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="7224a-188">完全な例については、[IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="7224a-189">Instructor ページの作成</span><span class="sxs-lookup"><span data-stu-id="7224a-189">Create Instructor pages</span></span>

<span data-ttu-id="7224a-190">このセクションでは、Instructor ページをスキャフォールディングし、関連する Courses と Enrollments を Instructors Index ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="7224a-191">![Instructors/Index ページ](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="7224a-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="7224a-192">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="7224a-193">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="7224a-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="7224a-194">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="7224a-195">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="7224a-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="7224a-196">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="7224a-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="7224a-197">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="7224a-198">ユーザーがインストラクターを選択すると、関連する `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="7224a-199">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="7224a-200">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="7224a-201">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="7224a-202">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="7224a-203">ユーザーがコースを選択すると、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="7224a-204">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="7224a-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="7224a-205">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="7224a-206">ビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="7224a-206">Create a view model</span></span>

<span data-ttu-id="7224a-207">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="7224a-208">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="7224a-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="7224a-209">次のコードを使用して、*SchoolViewModels/InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="7224a-210">Instructor ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="7224a-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7224a-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7224a-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7224a-212">次の例外を除き、「[Student ページのスキャフォールディング](xref:data/ef-rp/intro#scaffold-student-pages)」の指示に従います。</span><span class="sxs-lookup"><span data-stu-id="7224a-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="7224a-213">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="7224a-214">モデル クラスに `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="7224a-215">新しいコンテキスト クラスを作成するのではなく、既存のコンテキスト クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7224a-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7224a-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7224a-217">*Pages/Instructors* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="7224a-218">次のコマンドを実行して、Instructor モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="7224a-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="7224a-219">**Windows の場合:**</span><span class="sxs-lookup"><span data-stu-id="7224a-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="7224a-220">**Linux または macOS の場合**:</span><span class="sxs-lookup"><span data-stu-id="7224a-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="7224a-221">更新する前にスキャフォールディング ページがどのように表示されるかを確認するには、アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="7224a-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="7224a-222">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="7224a-223">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7224a-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="7224a-224">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="7224a-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="7224a-225">このコードでは、次のナビゲーション プロパティに一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="7224a-226">`CourseAssignments` と `Course` に対する `Include` メソッドと `ThenInclude` メソッドの繰り返しに注意してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="7224a-227">この繰り返しは、`Course` エンティティの 2 つのナビゲーション プロパティに一括読み込みを指定するために必要です。</span><span class="sxs-lookup"><span data-stu-id="7224a-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="7224a-228">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="7224a-229">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="7224a-230">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7224a-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="7224a-231">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="7224a-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="7224a-232">しかし、この場合は、フィルターによって 1 つのエンティティが選択されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-232">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="7224a-233">そのため、`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換するために呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-233">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="7224a-234">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7224a-234">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="7224a-235">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7224a-235">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="7224a-237">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-237">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="7224a-238">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="7224a-238">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="7224a-239">代わりに、コレクションが空の場合に既定値 (この場合は null) を返す `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-239">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="7224a-240">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-240">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="7224a-241">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7224a-241">Update the instructors Index page</span></span>

<span data-ttu-id="7224a-242">次のコードを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-242">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="7224a-243">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="7224a-243">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="7224a-244">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-244">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="7224a-245">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="7224a-245">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="7224a-246">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="7224a-246">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="7224a-247">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-247">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="7224a-248">ページ ディレクティブが `@page "{id:int?}"` の場合、URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7224a-248">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="7224a-249">`item.OfficeAssignment` が null ではない場合にのみ、`item.OfficeAssignment.Location` を表示する **Office** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-249">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="7224a-250">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-250">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="7224a-251">インストラクターごとに担当したコースを表示する **Courses** 列を追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-251">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="7224a-252">この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-252">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="7224a-253">選択したインストラクターとコースの `tr` 要素に `class="success"` を動的に追加するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-253">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="7224a-254">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-254">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="7224a-255">**Select** とラベル付けされる新しいハイパーリンクを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-255">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="7224a-256">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-256">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="7224a-257">選択した Instructor のコースのテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-257">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="7224a-258">選択したコースの学生登録のテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-258">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="7224a-259">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-259">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="7224a-260">`OfficeAssignment` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-260">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="7224a-261">インストラクターの **Select** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7224a-261">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="7224a-262">行スタイルの変更とそのインストラクターに割り当てられたコースが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-262">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="7224a-263">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-263">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="7224a-265">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="7224a-265">Using Single</span></span>

<span data-ttu-id="7224a-266">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-266">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="7224a-267">Where 条件を使った `Single` の使用は、個人の好みの問題です。</span><span class="sxs-lookup"><span data-stu-id="7224a-267">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="7224a-268">`Where` メソッドを使用しても利点はありません。</span><span class="sxs-lookup"><span data-stu-id="7224a-268">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="7224a-269">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-269">Explicit loading</span></span>

<span data-ttu-id="7224a-270">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-270">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="7224a-271">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="7224a-271">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="7224a-272">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="7224a-272">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="7224a-273">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-273">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="7224a-274">次のコードを使用して *Pages/Instructors/Index.cshtml.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-274">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="7224a-275">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="7224a-275">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="7224a-276">コースが選択されると、明示的読み込みコードで以下が取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-276">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="7224a-277">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="7224a-277">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="7224a-278">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="7224a-278">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="7224a-279">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-279">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="7224a-280">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-280">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="7224a-281">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="7224a-281">Test the app.</span></span> <span data-ttu-id="7224a-282">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="7224a-282">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7224a-283">次の手順</span><span class="sxs-lookup"><span data-stu-id="7224a-283">Next steps</span></span>

<span data-ttu-id="7224a-284">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-284">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="7224a-285">[前のチュートリアル](xref:data/ef-rp/complex-data-model)
>[次のチュートリアル](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="7224a-285">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7224a-286">このチュートリアルでは、関連データが読み取られ、表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-286">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="7224a-287">関連データとは、EF Core がナビゲーション プロパティに読み込むデータのことです。</span><span class="sxs-lookup"><span data-stu-id="7224a-287">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="7224a-288">解決できない問題が発生した場合は、[完成したアプリをダウンロードまたは表示](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-288">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="7224a-289">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7224a-289">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7224a-290">以下の図は、このチュートリアルの完成したページを示しています。</span><span class="sxs-lookup"><span data-stu-id="7224a-290">The following illustrations show the completed pages for this tutorial:</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

![Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="7224a-293">関連データの一括読み込み、明示的読み込み、遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-293">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="7224a-294">EF Core がエンティティのナビゲーション プロパティに関連データを読み込むには、複数の方法があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-294">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="7224a-295">[一括読み込み](/ef/core/querying/related-data#eager-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-295">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="7224a-296">一括読み込みは、エンティティの 1 つの型に対するクエリが関連エンティティも読み込む場合です。</span><span class="sxs-lookup"><span data-stu-id="7224a-296">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="7224a-297">エンティティが読み取られるときに、その関連データが取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-297">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="7224a-298">これは通常、必要なすべてのデータを取得する 1 つの結合クエリになります。</span><span class="sxs-lookup"><span data-stu-id="7224a-298">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="7224a-299">EF Core は、一部の型の一括読み込みに対して複数のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="7224a-299">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="7224a-300">複数のクエリを発行することで、1 つのクエリしかなかった EF6 の一部のクエリよりも、効率を高めることができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-300">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="7224a-301">一括読み込みは、`Include` メソッドと `ThenInclude` メソッドを使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-301">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![一括読み込みの例](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="7224a-303">一括読み込みでは、コレクション ナビゲーションが含まれるときに、複数のクエリが送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-303">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="7224a-304">メイン クエリに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="7224a-304">One query for the main query</span></span> 
  * <span data-ttu-id="7224a-305">読み込みツリー内のコレクション "エッジ" ごとに 1 つのクエリ</span><span class="sxs-lookup"><span data-stu-id="7224a-305">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="7224a-306">`Load` で分離したクエリ:データは分離したクエリで取得でき、EF Core がナビゲーション プロパティを "修正" します。</span><span class="sxs-lookup"><span data-stu-id="7224a-306">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="7224a-307">"修正" は、ナビゲーション プロパティが EF Core によって自動的に入力されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7224a-307">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="7224a-308">`Load` で分離したクエリは、一括読み込みよりも明示的読み込みに似ています。</span><span class="sxs-lookup"><span data-stu-id="7224a-308">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![分離したクエリの例](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="7224a-310">メモ:EF Core は、コンテキスト インスタンスに以前に読み込まれたその他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="7224a-310">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7224a-311">ナビゲーション プロパティのデータが明示的に含まれ*ない*場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-311">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="7224a-312">[明示的読み込み](/ef/core/querying/related-data#explicit-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-312">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="7224a-313">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-313">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7224a-314">必要なときに関連するデータを取得するコードを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-314">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="7224a-315">分離したクエリによる明示的読み込みにより、複数のクエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-315">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="7224a-316">明示的読み込みでは、コードで読み込まれるナビゲーション プロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-316">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="7224a-317">明示的読み込みを行うには、`Load` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-317">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="7224a-318">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-318">For example:</span></span>

  ![明示的読み込みの例](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="7224a-320">[遅延読み込み](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-320">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7224a-321">[遅延読み込みがバージョン 2.1 内の EF Core に追加されました](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="7224a-321">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="7224a-322">エンティティが最初に読み込まれるときに、関連データは取得されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-322">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="7224a-323">ナビゲーション プロパティに初めてアクセスすると、そのナビゲーション プロパティに必要なデータが自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-323">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="7224a-324">初めてナビゲーション プロパティにアクセスされるたびに、クエリが DB に送信されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-324">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="7224a-325">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-325">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="7224a-326">部門名を表示する Course ページを作成する</span><span class="sxs-lookup"><span data-stu-id="7224a-326">Create a Course page that displays department name</span></span>

<span data-ttu-id="7224a-327">Course エンティティには、`Department` エンティティを含むナビゲーション プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7224a-327">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="7224a-328">`Department` エンティティには、コースが割り当てられる部門が含まれています。</span><span class="sxs-lookup"><span data-stu-id="7224a-328">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="7224a-329">コースの一覧で割り当てられている部門の名前を表示するには:</span><span class="sxs-lookup"><span data-stu-id="7224a-329">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="7224a-330">`Department` エンティティから `Name` プロパティを取得します。</span><span class="sxs-lookup"><span data-stu-id="7224a-330">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="7224a-331">`Department` エンティティは `Course.Department` ナビゲーション プロパティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-331">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="7224a-333">Course モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="7224a-333">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7224a-334">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7224a-334">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7224a-335">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Course` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-335">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7224a-336">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7224a-336">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="7224a-337">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7224a-337">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="7224a-338">上記のコマンドは、`Course` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="7224a-338">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="7224a-339">Visual Studio でプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="7224a-339">Open the project in Visual Studio.</span></span>

<span data-ttu-id="7224a-340">*Pages/Courses/Index.cshtml.cs* を開き、`OnGetAsync` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="7224a-340">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="7224a-341">スキャフォールディング エンジンは、`Department` ナビゲーション プロパティに一括読み込みを指定しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-341">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="7224a-342">`Include` メソッドが一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-342">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="7224a-343">アプリを実行し、 **[Courses]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="7224a-343">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="7224a-344">Department 列に `DepartmentID` が表示されますが、これには役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="7224a-344">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="7224a-345">`OnGetAsync` メソッドを次のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-345">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="7224a-346">上のコードは `AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-346">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="7224a-347">`AsNoTracking` は、返されるエンティティが追跡されないため、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="7224a-347">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="7224a-348">これらのエンティティは現在のコンテキストでは更新されないため、追跡されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-348">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="7224a-349">次の強調表示されているマークアップで *Pages/Courses/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-349">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="7224a-350">スキャフォールディング コードに、次の変更が行われました。</span><span class="sxs-lookup"><span data-stu-id="7224a-350">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="7224a-351">見出しが Index から Courses に変更されました。</span><span class="sxs-lookup"><span data-stu-id="7224a-351">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="7224a-352">`CourseID` プロパティ値を示す **Number** 列が追加されました。</span><span class="sxs-lookup"><span data-stu-id="7224a-352">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="7224a-353">既定では、主キーは、通常、エンドユーザーにとって意味がないため、スキャフォールディングされません。</span><span class="sxs-lookup"><span data-stu-id="7224a-353">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="7224a-354">ただし、このケースでは、主キーは意味があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-354">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="7224a-355">部門名が表示されるように、**Department** 列を変更しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-355">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="7224a-356">コードは、`Department` ナビゲーション プロパティに読み込まれる `Department` エンティティの `Name` プロパティを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-356">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="7224a-357">アプリを実行し、 **[Courses]** タブを選択して部門名のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-357">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Courses/Index ページ](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="7224a-359">Select を使用した関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-359">Loading related data with Select</span></span>

<span data-ttu-id="7224a-360">`OnGetAsync` メソッドは、`Include` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-360">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7224a-361">`Select` 演算子は必要な関連データのみを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-361">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="7224a-362">`Department.Name` のような単一の項目の場合、SQL INNER JOIN が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-362">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="7224a-363">コレクションの場合は、別のデータベース アクセスが使用されますが、コレクションの `Include` 演算子でも同じです。</span><span class="sxs-lookup"><span data-stu-id="7224a-363">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="7224a-364">次のコードは、`Select` メソッドを使用して関連データを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-364">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="7224a-365">`CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="7224a-365">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="7224a-366">完全な例については、[IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) と [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-366">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="7224a-367">コース登録を示す Instructors ページを作成する</span><span class="sxs-lookup"><span data-stu-id="7224a-367">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="7224a-368">このセクションでは、Instructors ページが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-368">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="7224a-369">![Instructors/Index ページ](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="7224a-369">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="7224a-370">このページは、次の方法で関連データを読み取って表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-370">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="7224a-371">インストラクターのリストには `OfficeAssignment` エンティティからの関連データが表示されます (上の図の Office)。</span><span class="sxs-lookup"><span data-stu-id="7224a-371">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="7224a-372">`Instructor` エンティティと `OfficeAssignment` エンティティは、一対ゼロまたは一対一のリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-372">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="7224a-373">`OfficeAssignment` エンティティには一括読み込みが使用されています。</span><span class="sxs-lookup"><span data-stu-id="7224a-373">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="7224a-374">一括読み込みは一般的に、関連データを表示する必要がある場合により効率的です。</span><span class="sxs-lookup"><span data-stu-id="7224a-374">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="7224a-375">この場合、インストラクターへのオフィスの割り当てが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-375">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="7224a-376">ユーザーがインストラクターを選択 (上の図では Harui) すると、関連 `Course` エンティティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-376">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="7224a-377">`Instructor` エンティティと `Course` エンティティは多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-377">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="7224a-378">`Course` エンティティとその関連 `Department` エンティティには一括読み込みが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-378">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="7224a-379">このケースでは、選択したインストラクターのコースのみが必要なため、分離したクエリの方が効率的な場合があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-379">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="7224a-380">この例では、ナビゲーション プロパティ内のエンティティのナビゲーション プロパティに一括読み込みを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-380">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="7224a-381">ユーザーがコースを選択すると (上の図では Chemistry (化学))、`Enrollments` エンティティからの関連データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-381">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="7224a-382">上の図では、受講者名とグレードが表示されています。</span><span class="sxs-lookup"><span data-stu-id="7224a-382">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="7224a-383">`Course` エンティティと `Enrollment` エンティティは一対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="7224a-383">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="7224a-384">Instructor インデックス ビューのビュー モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="7224a-384">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="7224a-385">Instructors ページには、3 つの異なるテーブルからのデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-385">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="7224a-386">3 つのテーブルを表す 3 つのエンティティを含むビュー モデルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-386">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="7224a-387">次のコードを使用して、*SchoolViewModels* フォルダー内に *InstructorIndexData.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-387">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="7224a-388">Instructor モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="7224a-388">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7224a-389">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7224a-389">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7224a-390">「[Student モデルをスキャホールディングする](xref:data/ef-rp/intro#scaffold-the-student-model)」の手順に従い、モデル クラスの `Instructor` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-390">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7224a-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7224a-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="7224a-392">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7224a-392">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="7224a-393">上記のコマンドは、`Instructor` モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="7224a-393">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="7224a-394">アプリを実行し、Instructors ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="7224a-394">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="7224a-395">*Pages/Instructors/Index.cshtml.cs* を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7224a-395">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="7224a-396">`OnGetAsync` メソッドは、選択したインストラクターの ID の任意のルート データを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7224a-396">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="7224a-397">*Pages/Instructors/Index.cshtml.cs* ファイルでクエリを調べます。</span><span class="sxs-lookup"><span data-stu-id="7224a-397">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7224a-398">クエリには次の 2 つが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7224a-398">The query has two includes:</span></span>

* <span data-ttu-id="7224a-399">`OfficeAssignment`:[Instructors ビュー](#IP)に表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-399">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="7224a-400">`CourseAssignments`:担当したコースを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="7224a-400">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="7224a-401">Instructors/Index ページを更新する</span><span class="sxs-lookup"><span data-stu-id="7224a-401">Update the instructors Index page</span></span>

<span data-ttu-id="7224a-402">次のマークアップを使用して *Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-402">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="7224a-403">上記のマークアップは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="7224a-403">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="7224a-404">`page` ディレクティブを `@page` から `@page "{id:int?}"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-404">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="7224a-405">`"{id:int?}"` はルート テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="7224a-405">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="7224a-406">ルート テンプレートは、URL 内の整数クエリ文字列をルート データに変更します。</span><span class="sxs-lookup"><span data-stu-id="7224a-406">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="7224a-407">たとえば、`@page` ディレクティブのみのインストラクターで **[Select]** リンクをクリックすると、次のような URL を生成します。</span><span class="sxs-lookup"><span data-stu-id="7224a-407">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="7224a-408">ページ ディレクティブが `@page "{id:int?}"` の場合、上記の URL は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7224a-408">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="7224a-409">ページ タイトルは **Instructors** です。</span><span class="sxs-lookup"><span data-stu-id="7224a-409">Page title is **Instructors**.</span></span>
* <span data-ttu-id="7224a-410">`item.OfficeAssignment` が null ではない場合にのみ `item.OfficeAssignment.Location` を表示する **Office** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-410">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="7224a-411">これは、一対ゼロまたは一対一のリレーションシップであるため、関連する OfficeAssignment エンティティがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-411">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="7224a-412">インストラクターごとに担当したコースを表示する **Courses** 列を追加しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-412">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="7224a-413">この Razor 構文の詳細については、「[明示的な行の遷移](xref:mvc/views/razor#explicit-line-transition)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-413">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="7224a-414">選択したインストラクターの `tr` 要素に `class="success"` を動的に追加するコードを追加しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-414">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="7224a-415">これは、ブートストラップ クラスを使用して、選択した行の背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-415">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="7224a-416">**Select** とラベル付けされるハイパーリンクを追加しました。</span><span class="sxs-lookup"><span data-stu-id="7224a-416">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="7224a-417">このリンクは、選択したインストラクターの ID を `Index` メソッドに送信し、背景色を設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-417">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="7224a-418">アプリを実行し、 **[Instructors]** タブを選択します。関連する `OfficeAssignment` エンティティから `Location` (オフィス) がページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-418">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="7224a-419">OfficeAssignment\` が null の場合、空のテーブル セルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-419">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="7224a-420">**[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7224a-420">Click on the **Select** link.</span></span> <span data-ttu-id="7224a-421">行のスタイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-421">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="7224a-422">選択したインストラクターが担当するコースを追加する</span><span class="sxs-lookup"><span data-stu-id="7224a-422">Add courses taught by selected instructor</span></span>

<span data-ttu-id="7224a-423">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-423">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="7224a-424">`public int CourseID { get; set; }` を追加します</span><span class="sxs-lookup"><span data-stu-id="7224a-424">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="7224a-425">更新されたクエリを確認します。</span><span class="sxs-lookup"><span data-stu-id="7224a-425">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="7224a-426">上記のクエリは `Department` エンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-426">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="7224a-427">次のコードは、インストラクターが選択されたとき (`id != null`) に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-427">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="7224a-428">選択されたインストラクターがビュー モデルのインストラクターのリストから取得されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-428">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="7224a-429">ビュー モデルの `Courses` プロパティが `Course` エンティティと共にそのインストラクターの `CourseAssignments` ナビゲーション プロパティから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7224a-429">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="7224a-430">`Where` メソッドはコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="7224a-430">The `Where` method returns a collection.</span></span> <span data-ttu-id="7224a-431">上記の `Where` メソッドでは、1 つの `Instructor` エンティティのみが返されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-431">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="7224a-432">`Single` メソッドはコレクションを 1 つの `Instructor` エンティティに変換します。</span><span class="sxs-lookup"><span data-stu-id="7224a-432">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="7224a-433">`Instructor` エンティティは `CourseAssignments` プロパティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7224a-433">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="7224a-434">`CourseAssignments` は関連する `Course` エンティティへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="7224a-434">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![インストラクター対コース m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="7224a-436">コレクションに 1 つの項目しかない場合は、`Single` メソッドがコレクションで使用されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-436">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="7224a-437">コレクションが空の場合、または複数の項目がある場合、`Single` メソッドは例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="7224a-437">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="7224a-438">代わりに、コレクションが空の場合に既定値を返す (この場合は null) `SingleOrDefault` を使用します。</span><span class="sxs-lookup"><span data-stu-id="7224a-438">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="7224a-439">空のコレクションで `SingleOrDefault` を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7224a-439">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="7224a-440">(null 参照で `Courses` プロパティを見つけようとすると) 例外になります。</span><span class="sxs-lookup"><span data-stu-id="7224a-440">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="7224a-441">例外メッセージに問題の原因が明確に示されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7224a-441">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="7224a-442">次のコードは、コースが選択されたときにビュー モデルの `Enrollments` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-442">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="7224a-443">*Pages/Instructors/Index.cshtml* Razor ページの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-443">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="7224a-444">上記のマークアップは、インストラクターが選択されたときに、インストラクターに関連するコースのリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-444">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="7224a-445">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="7224a-445">Test the app.</span></span> <span data-ttu-id="7224a-446">Instructors ページの **[Select]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7224a-446">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="7224a-447">受講者データを表示する</span><span class="sxs-lookup"><span data-stu-id="7224a-447">Show student data</span></span>

<span data-ttu-id="7224a-448">このセクションでは、選択したコースの受講者データを表示するため、アプリが更新されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-448">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="7224a-449">次のコードを使用して、*Pages/Instructors/Index.cshtml.cs* 内の `OnGetAsync` メソッドのクエリを更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-449">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7224a-450">*Pages/Instructors/Index.cshtml* を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-450">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="7224a-451">ファイルの末尾に次のマークアップを追加します。</span><span class="sxs-lookup"><span data-stu-id="7224a-451">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="7224a-452">上記のマークアップは、選択したコースに登録されている受講者のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-452">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="7224a-453">ページを更新し、インストラクターを選択します。</span><span class="sxs-lookup"><span data-stu-id="7224a-453">Refresh the page and select an instructor.</span></span> <span data-ttu-id="7224a-454">コースを選択して、登録済みの受講者とその成績のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-454">Select a course to see the list of enrolled students and their grades.</span></span>

![インストラクターとコースが選択された Instructors/Index ページ](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="7224a-456">Single を使用する</span><span class="sxs-lookup"><span data-stu-id="7224a-456">Using Single</span></span>

<span data-ttu-id="7224a-457">`Single` メソッドは、`Where` メソッドを別に呼び出す代わりに、`Where` 条件で渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-457">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="7224a-458">上記の `Single` アプローチでは、`Where` を使用すること以上のメリットは提供されません。</span><span class="sxs-lookup"><span data-stu-id="7224a-458">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="7224a-459">一部の開発者は、`Single` アプローチ スタイルを選択します。</span><span class="sxs-lookup"><span data-stu-id="7224a-459">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="7224a-460">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="7224a-460">Explicit loading</span></span>

<span data-ttu-id="7224a-461">現在のコードは、`Enrollments` と `Students` に一括読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="7224a-461">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="7224a-462">ユーザーがコースの登録を表示することはほとんどないとします。</span><span class="sxs-lookup"><span data-stu-id="7224a-462">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="7224a-463">その場合、最適化は要求された場合にのみ登録データを読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="7224a-463">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="7224a-464">このセクションでは、`Enrollments` と `Students` の明示的読み込みを使用するために `OnGetAsync` が更新されます。</span><span class="sxs-lookup"><span data-stu-id="7224a-464">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="7224a-465">次のコードを使用して `OnGetAsync` を更新します。</span><span class="sxs-lookup"><span data-stu-id="7224a-465">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="7224a-466">上記のコードは、登録と学生データの *ThenInclude* メソッド呼び出しを破棄します。</span><span class="sxs-lookup"><span data-stu-id="7224a-466">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="7224a-467">コースが選択されると、強調表示されたコードが以下を取得します。</span><span class="sxs-lookup"><span data-stu-id="7224a-467">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="7224a-468">選択したコースの `Enrollment` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="7224a-468">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="7224a-469">各 `Enrollment` の `Student` エンティティ。</span><span class="sxs-lookup"><span data-stu-id="7224a-469">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="7224a-470">上記のコードでは、`.AsNoTracking()` がコメント アウトされていることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="7224a-470">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="7224a-471">追跡対象のエンティティに対して、ナビゲーション プロパティのみを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="7224a-471">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="7224a-472">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="7224a-472">Test the app.</span></span> <span data-ttu-id="7224a-473">ユーザーの観点からは、アプリの動作は以前のバージョンと同じです。</span><span class="sxs-lookup"><span data-stu-id="7224a-473">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="7224a-474">次のチュートリアルでは、関連データの更新方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7224a-474">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7224a-475">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7224a-475">Additional resources</span></span>

* [<span data-ttu-id="7224a-476">このチュートリアルの YouTube バージョン (part1)</span><span class="sxs-lookup"><span data-stu-id="7224a-476">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="7224a-477">このチュートリアルの YouTube バージョン (part2)</span><span class="sxs-lookup"><span data-stu-id="7224a-477">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="7224a-478">[前へ](xref:data/ef-rp/complex-data-model)
>[次へ](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="7224a-478">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
