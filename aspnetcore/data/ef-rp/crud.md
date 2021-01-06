---
title: パート 2、ASP.NET Core の Razor Pages と EF Core - CRUD
author: rick-anderson
description: Razor Pages と Entity Framework チュートリアル シリーズのパート 2。
ms.author: riande
ms.date: 07/22/2019
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
uid: data/ef-rp/crud
ms.openlocfilehash: 4a48fb094888d51aa6f881c82e4f20ffbc84c8e2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901172"
---
# <a name="part-2-no-locrazor-pages-with-ef-core-in-aspnet-core---crud"></a><span data-ttu-id="2e609-103">パート 2、ASP.NET Core の Razor Pages と EF Core - CRUD</span><span class="sxs-lookup"><span data-stu-id="2e609-103">Part 2, Razor Pages with EF Core in ASP.NET Core - CRUD</span></span>

<span data-ttu-id="2e609-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2e609-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2e609-105">このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。</span><span class="sxs-lookup"><span data-stu-id="2e609-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="2e609-106">リポジトリがない</span><span class="sxs-lookup"><span data-stu-id="2e609-106">No repository</span></span>

<span data-ttu-id="2e609-107">一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-107">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="2e609-108">このチュートリアルでは、これは行いません。</span><span class="sxs-lookup"><span data-stu-id="2e609-108">This tutorial doesn't do that.</span></span> <span data-ttu-id="2e609-109">複雑さを最小限に抑え、チュートリアルの焦点を EF Core に置くために、EF Core コードがページ モデル クラスに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-109">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="2e609-110">Details ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-110">Update the Details page</span></span>

<span data-ttu-id="2e609-111">Students ページのスキャフォールディング コードには、登録データが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-111">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="2e609-112">このセクションでは、Details ページに登録を追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-112">In this section, you add enrollments to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="2e609-113">登録の読み取り</span><span class="sxs-lookup"><span data-stu-id="2e609-113">Read enrollments</span></span>

<span data-ttu-id="2e609-114">学生の登録データをページに表示するには、そのデータを読み取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-114">To display a student's enrollment data on the page, you need to read it.</span></span> <span data-ttu-id="2e609-115">*Pages/Students/Details.cshtml.cs* のスキャフォールディング コードでは、Enrollment データを含まない Student データのみが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2e609-115">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="2e609-116">`OnGetAsync` メソッドを次のコードに置き換えて、選択した学生の登録データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-116">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="2e609-117">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-117">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="2e609-118">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2e609-118">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="2e609-119">これらのメソッドは、[関連データの読み込み](xref:data/ef-rp/read-related-data)のチュートリアルで詳しく検討します。</span><span class="sxs-lookup"><span data-stu-id="2e609-119">These methods are examined in detail in the [Read related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="2e609-120">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されないシナリオでパフォーマンスを改善します。</span><span class="sxs-lookup"><span data-stu-id="2e609-120">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="2e609-121">`AsNoTracking` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="2e609-121">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="2e609-122">登録の表示</span><span class="sxs-lookup"><span data-stu-id="2e609-122">Display enrollments</span></span>

<span data-ttu-id="2e609-123">*Pages/Students/Details.cshtml* 内のコードを次のコードに置き換えて、登録のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-123">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="2e609-124">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-124">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="2e609-125">上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="2e609-125">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="2e609-126">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-126">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="2e609-127">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-127">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="2e609-128">アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e609-128">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="2e609-129">選択した受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-129">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="2e609-130">1 つのエンティティを読み取る方法</span><span class="sxs-lookup"><span data-stu-id="2e609-130">Ways to read one entity</span></span>

<span data-ttu-id="2e609-131">生成されたコードでは、[FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用して 1 つのエンティティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-131">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="2e609-132">このメソッドでは、何も見つからない場合は null が返されます。それ以外の場合は、クエリのフィルター条件を満たす最初の行が返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-132">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="2e609-133">`FirstOrDefaultAsync` は、通常、次の代替手段よりも適しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-133">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="2e609-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - クエリ フィルターを満たす複数のエンティティがある場合に、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="2e609-134">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="2e609-135">クエリによって複数の行が返される可能性があるかどうかを判断するため、`SingleOrDefaultAsync` は複数の行をフェッチしようとします。</span><span class="sxs-lookup"><span data-stu-id="2e609-135">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="2e609-136">一意のキーを検索する場合と同様に、クエリが 1 つのエンティティだけを返すことができる場合は、この追加作業は不要です。</span><span class="sxs-lookup"><span data-stu-id="2e609-136">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="2e609-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 主キー (PK) を持つエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e609-137">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="2e609-138">PK を持つエンティティがコンテキストによって追跡されている場合、データベースに対する要求がなくても該当するエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-138">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="2e609-139">このメソッドは、単一のエンティティを検索するように最適化されていますが、`FindAsync` を使用して `Include` を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="2e609-139">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="2e609-140">したがって、関連データが必要な場合は、`FirstOrDefaultAsync` を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e609-140">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="2e609-141">ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="2e609-141">Route data vs. query string</span></span>

<span data-ttu-id="2e609-142">Details ページの URL は `https://localhost:<port>/Students/Details?id=1` です。</span><span class="sxs-lookup"><span data-stu-id="2e609-142">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="2e609-143">エンティティの主キー値がクエリ文字列に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-143">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="2e609-144">ルート データのキー値を渡すことを好む開発者もいます。`https://localhost:<port>/Students/Details/1`</span><span class="sxs-lookup"><span data-stu-id="2e609-144">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="2e609-145">詳細については、「[生成されたコードの更新](xref:tutorials/razor-pages/da1#update-the-generated-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e609-145">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="2e609-146">Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-146">Update the Create page</span></span>

<span data-ttu-id="2e609-147">Create ページのスキャフォールディングされた `OnPostAsync` コードは、[過剰ポスティング](#overposting)に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="2e609-147">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="2e609-148">*Pages/Students/Create.cshtml.cs* の `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-148">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="2e609-149">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="2e609-149">TryUpdateModelAsync</span></span>

<span data-ttu-id="2e609-150">上記のコードでは、Student オブジェクトを作成し、ポストされたフォーム フィールドを使用して Student オブジェクトのプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e609-150">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="2e609-151">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) メソッド:</span><span class="sxs-lookup"><span data-stu-id="2e609-151">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="2e609-152">[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからポストされたフォーム値を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-152">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="2e609-153">リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="2e609-153">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="2e609-154">"student" のプレフィックスを持つフォーム フィールドを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e609-154">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="2e609-155">たとえば、`Student.FirstMidName` のようにします。</span><span class="sxs-lookup"><span data-stu-id="2e609-155">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="2e609-156">大文字と小文字の区別はありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-156">It's not case sensitive.</span></span>
* <span data-ttu-id="2e609-157">[モデル バインド](xref:mvc/models/model-binding) システムを使用して、文字列からフォーム値を `Student` モデル内の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="2e609-157">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="2e609-158">たとえば、`EnrollmentDate` は `DateTime` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-158">For example, `EnrollmentDate` is converted to `DateTime`.</span></span>

<span data-ttu-id="2e609-159">アプリを実行し、Create ページをテストする student エンティティを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e609-159">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="2e609-160">過剰ポスティング</span><span class="sxs-lookup"><span data-stu-id="2e609-160">Overposting</span></span>

<span data-ttu-id="2e609-161">ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-161">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="2e609-162">たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-162">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="2e609-163">アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-163">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="2e609-164">ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-164">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="2e609-165">元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。</span><span class="sxs-lookup"><span data-stu-id="2e609-165">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="2e609-166">`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、データベース内で更新されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-166">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="2e609-167">次の図は、ポストされたフォームの値に、値 "OverPost" が含まれる `Secret` フィールドを追加している Fiddler ツールを示しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-167">The following image shows the Fiddler tool adding the `Secret` field, with the value "OverPost", to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="2e609-169">挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-169">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="2e609-170">これは、アプリ デザイナーで、Create ページで `Secret` プロパティが設定されることを想定していない場合でも発生します。</span><span class="sxs-lookup"><span data-stu-id="2e609-170">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="2e609-171">ビュー モデル</span><span class="sxs-lookup"><span data-stu-id="2e609-171">View model</span></span>

<span data-ttu-id="2e609-172">ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e609-172">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="2e609-173">アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2e609-173">The application model is often called the domain model.</span></span> <span data-ttu-id="2e609-174">ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-174">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="2e609-175">ビュー モデルには、Create ページなどの UI ページで必要なプロパティのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-175">The view model contains only the properties needed for the UI page, for example, the Create page.</span></span>

<span data-ttu-id="2e609-176">一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-176">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="2e609-177">次の `StudentVM` ビュー モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2e609-177">Consider the following `StudentVM` view model:</span></span>

[!code-csharp[Main](intro/samples/cu50/ViewModels/StudentVM.cs?name=snippet)]

<span data-ttu-id="2e609-178">次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e609-178">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml.cs?name=snippet)]

<span data-ttu-id="2e609-179">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-179">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="2e609-180">`SetValues` では一致するプロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-180">`SetValues` uses property name matching.</span></span> <span data-ttu-id="2e609-181">ビューモデルには、次の種類があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-181">The view model type:</span></span>

* <span data-ttu-id="2e609-182">モデルの種類に関連付けられる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-182">Doesn't need to be related to the model type.</span></span>
* <span data-ttu-id="2e609-183">一致するプロパティを持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-183">Needs to have properties that match.</span></span>

<span data-ttu-id="2e609-184">`StudentVM` を使用するには、Create ページで、`Student` ではなく `StudentVM` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-184">Using `StudentVM` requires the Create page use `StudentVM` rather than `Student`:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Students/CreateVM.cshtml)]

## <a name="update-the-edit-page"></a><span data-ttu-id="2e609-185">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-185">Update the Edit page</span></span>

<span data-ttu-id="2e609-186">*Pages/Students/Edit.cshtml.cs* で、`OnGetAsync` メソッドと `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-186">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="2e609-187">コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-187">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="2e609-188">`FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-188">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="2e609-189">関連データを含める必要がない場合は、`FindAsync` の方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="2e609-189">When you don't have to include related data, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="2e609-190">`OnPostAsync` には `id` パラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="2e609-190">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="2e609-191">現在の学生は、空の学生を作成するのではなく、データベースからフェッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-191">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="2e609-192">アプリを実行し、学生を作成して編集することでアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e609-192">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="2e609-193">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="2e609-193">Entity States</span></span>

<span data-ttu-id="2e609-194">データベース コンテキストは、メモリ内のエンティティが、データベース内の対応する行と同期状態にあるかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="2e609-194">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="2e609-195">この追跡情報により、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。</span><span class="sxs-lookup"><span data-stu-id="2e609-195">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="2e609-196">たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-196">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="2e609-197">`SaveChangesAsync` が呼び出されると、データベース コンテキストによって SQL の `INSERT` コマンドが発行されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-197">When `SaveChangesAsync` is called, the database context issues a SQL `INSERT` command.</span></span>

<span data-ttu-id="2e609-198">エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-198">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="2e609-199">`Added`:エンティティはデータベースにまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="2e609-199">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="2e609-200">`SaveChanges` メソッドでは、`INSERT` ステートメントが発行されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-200">The `SaveChanges` method issues an `INSERT` statement.</span></span>

* <span data-ttu-id="2e609-201">`Unchanged`:このエンティティでは変更を保存する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-201">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="2e609-202">エンティティがこの状態になるのは、エンティティがデータベースから読み取られた場合です。</span><span class="sxs-lookup"><span data-stu-id="2e609-202">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="2e609-203">`Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-203">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="2e609-204">`SaveChanges` メソッドでは、`UPDATE` ステートメントが発行されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-204">The `SaveChanges` method issues an `UPDATE` statement.</span></span>

* <span data-ttu-id="2e609-205">`Deleted`:エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="2e609-205">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="2e609-206">`SaveChanges` メソッドでは、`DELETE` ステートメントが発行されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-206">The `SaveChanges` method issues a `DELETE` statement.</span></span>

* <span data-ttu-id="2e609-207">`Detached`:エンティティはデータベース コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-207">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="2e609-208">デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-208">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="2e609-209">エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-209">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="2e609-210">`SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL `UPDATE` ステートメントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-210">Calling `SaveChanges` generates a SQL `UPDATE` statement that updates only the changed properties.</span></span>

<span data-ttu-id="2e609-211">Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-211">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="2e609-212">ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-212">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="2e609-213">その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-213">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2e609-214">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-214">Update the Delete page</span></span>

<span data-ttu-id="2e609-215">このセクションでは、`SaveChanges` の呼び出しが失敗すると、カスタム エラー メッセージが実装されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-215">In this section, a custom error message is implemented when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="2e609-216">*Pages/Students/Delete.cshtml.cs* のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-216">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="2e609-217">変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-217">The changes are highlighted:</span></span>

[!code-csharp[Main](intro/samples/cu50/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=12-14,22,30-33,45-99)]

<span data-ttu-id="2e609-218">上記のコードでは、省略可能なパラメーター `saveChangesError` を `OnGetAsync` メソッド シグネチャに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-218">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="2e609-219">`saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-219">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="2e609-220">一時的なネットワークの問題により、削除操作が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-220">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="2e609-221">データベースがクラウド内にある場合は、一時的なネットワーク エラーが発生する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-221">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="2e609-222">Delete ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` パラメーターは `false` です。</span><span class="sxs-lookup"><span data-stu-id="2e609-222">The `saveChangesError` parameter is `false` when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="2e609-223">削除操作が失敗したために、`OnPostAsync` によって `OnGetAsync` が呼び出された場合、`saveChangesError` パラメーターは `true` です。</span><span class="sxs-lookup"><span data-stu-id="2e609-223">When `OnGetAsync` is called by `OnPostAsync` because the delete operation failed, the `saveChangesError` parameter is `true`.</span></span>

<span data-ttu-id="2e609-224">`OnPostAsync` メソッドは、選択されたエンティティを取得し、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2e609-224">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="2e609-225">`SaveChanges` が呼び出された場合、SQL の `DELETE` コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-225">When `SaveChanges` is called, a SQL `DELETE` command is generated.</span></span> <span data-ttu-id="2e609-226">`Remove` が失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="2e609-226">If `Remove` fails:</span></span>

* <span data-ttu-id="2e609-227">データベース例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-227">The database exception is caught.</span></span>
* <span data-ttu-id="2e609-228">[削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-228">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="2e609-229">Delete Razor ページ (*Pages/Students/Delete.cshtml*) にエラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-229">Add an error message to *Pages/Students/Delete.cshtml*:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="2e609-230">アプリを実行し、学生を削除して、Delete ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e609-230">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2e609-231">次の手順</span><span class="sxs-lookup"><span data-stu-id="2e609-231">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2e609-232">[前のチュートリアル](xref:data/ef-rp/intro)
> [次のチュートリアル](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="2e609-232">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2e609-233">このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。</span><span class="sxs-lookup"><span data-stu-id="2e609-233">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

## <a name="no-repository"></a><span data-ttu-id="2e609-234">リポジトリがない</span><span class="sxs-lookup"><span data-stu-id="2e609-234">No repository</span></span>

<span data-ttu-id="2e609-235">一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-235">Some developers use a service layer or repository pattern to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span> <span data-ttu-id="2e609-236">このチュートリアルでは、これは行いません。</span><span class="sxs-lookup"><span data-stu-id="2e609-236">This tutorial doesn't do that.</span></span> <span data-ttu-id="2e609-237">複雑さを最小限に抑え、チュートリアルの焦点を EF Core に置くために、EF Core コードがページ モデル クラスに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-237">To minimize complexity and keep the tutorial focused on EF Core, EF Core code is added directly to the page model classes.</span></span> 

## <a name="update-the-details-page"></a><span data-ttu-id="2e609-238">Details ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-238">Update the Details page</span></span>

<span data-ttu-id="2e609-239">Students ページのスキャフォールディング コードには、登録データが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-239">The scaffolded code for the Students pages doesn't include enrollment data.</span></span> <span data-ttu-id="2e609-240">このセクションでは、Details ページに登録が追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-240">In this section, enrollments are added to the Details page.</span></span>

### <a name="read-enrollments"></a><span data-ttu-id="2e609-241">登録の読み取り</span><span class="sxs-lookup"><span data-stu-id="2e609-241">Read enrollments</span></span>

<span data-ttu-id="2e609-242">このページに学生の登録データを表示するには、その登録データの読み取りが行われる必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-242">To display a student's enrollment data on the page, the enrollement data needs to be read.</span></span> <span data-ttu-id="2e609-243">*Pages/Students/Details.cshtml.cs* のスキャフォールディング コードでは、Enrollment データを含まない Student データのみが読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2e609-243">The scaffolded code in *Pages/Students/Details.cshtml.cs* reads only the Student data, without the Enrollment data:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/Details1.cshtml.cs?name=snippet_OnGetAsync&highlight=8)]

<span data-ttu-id="2e609-244">`OnGetAsync` メソッドを次のコードに置き換えて、選択した学生の登録データを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-244">Replace the `OnGetAsync` method with the following code to read enrollment data for the selected student.</span></span> <span data-ttu-id="2e609-245">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-245">The changes are highlighted.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Details.cshtml.cs?name=snippet_OnGetAsync&highlight=8-12)]

<span data-ttu-id="2e609-246">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2e609-246">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="2e609-247">これらのメソッドは、[関連データの読み込み](xref:data/ef-rp/read-related-data)のチュートリアルで詳しく検討します。</span><span class="sxs-lookup"><span data-stu-id="2e609-247">These methods are examined in detail in the [Reading related data](xref:data/ef-rp/read-related-data) tutorial.</span></span>

<span data-ttu-id="2e609-248">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されないシナリオでパフォーマンスを改善します。</span><span class="sxs-lookup"><span data-stu-id="2e609-248">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios where the entities returned are not updated in the current context.</span></span> <span data-ttu-id="2e609-249">`AsNoTracking` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="2e609-249">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-enrollments"></a><span data-ttu-id="2e609-250">登録の表示</span><span class="sxs-lookup"><span data-stu-id="2e609-250">Display enrollments</span></span>

<span data-ttu-id="2e609-251">*Pages/Students/Details.cshtml* 内のコードを次のコードに置き換えて、登録のリストを表示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-251">Replace the code in *Pages/Students/Details.cshtml* with the following code to display a list of enrollments.</span></span> <span data-ttu-id="2e609-252">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-252">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="2e609-253">上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="2e609-253">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="2e609-254">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-254">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="2e609-255">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-255">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="2e609-256">アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e609-256">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="2e609-257">選択した受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-257">The list of courses and grades for the selected student is displayed.</span></span>

### <a name="ways-to-read-one-entity"></a><span data-ttu-id="2e609-258">1 つのエンティティを読み取る方法</span><span class="sxs-lookup"><span data-stu-id="2e609-258">Ways to read one entity</span></span>

<span data-ttu-id="2e609-259">生成されたコードでは、[FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用して 1 つのエンティティを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-259">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) to read one entity.</span></span> <span data-ttu-id="2e609-260">このメソッドでは、何も見つからない場合は null が返されます。それ以外の場合は、クエリのフィルター条件を満たす最初の行が返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-260">This method returns null if nothing is found; otherwise, it returns the first row found that satisfies the query filter criteria.</span></span> <span data-ttu-id="2e609-261">`FirstOrDefaultAsync` は、通常、次の代替手段よりも適しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-261">`FirstOrDefaultAsync` is generally a better choice than the following alternatives:</span></span>

* <span data-ttu-id="2e609-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - クエリ フィルターを満たす複数のエンティティがある場合に、例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="2e609-262">[SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) - Throws an exception if there's more than one entity that satisfies the query filter.</span></span> <span data-ttu-id="2e609-263">クエリによって複数の行が返される可能性があるかどうかを判断するため、`SingleOrDefaultAsync` は複数の行をフェッチしようとします。</span><span class="sxs-lookup"><span data-stu-id="2e609-263">To determine if more than one row could be returned by the query, `SingleOrDefaultAsync` tries to fetch multiple rows.</span></span> <span data-ttu-id="2e609-264">一意のキーを検索する場合と同様に、クエリが 1 つのエンティティだけを返すことができる場合は、この追加作業は不要です。</span><span class="sxs-lookup"><span data-stu-id="2e609-264">This extra work is unnecessary if the query can only return one entity, as when it searches on a unique key.</span></span>
* <span data-ttu-id="2e609-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - 主キー (PK) を持つエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e609-265">[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) - Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="2e609-266">PK を持つエンティティがコンテキストによって追跡されている場合、データベースに対する要求がなくても該当するエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-266">If an entity with the PK is being tracked by the context, it's returned without a request to the database.</span></span> <span data-ttu-id="2e609-267">このメソッドは、単一のエンティティを検索するように最適化されていますが、`FindAsync` を使用して `Include` を呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="2e609-267">This method is optimized to look up a single entity, but you can't call `Include` with `FindAsync`.</span></span>  <span data-ttu-id="2e609-268">したがって、関連データが必要な場合は、`FirstOrDefaultAsync` を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e609-268">So if related data is needed, `FirstOrDefaultAsync` is the better choice.</span></span>

### <a name="route-data-vs-query-string"></a><span data-ttu-id="2e609-269">ルート データとクエリ文字列</span><span class="sxs-lookup"><span data-stu-id="2e609-269">Route data vs. query string</span></span>

<span data-ttu-id="2e609-270">Details ページの URL は `https://localhost:<port>/Students/Details?id=1` です。</span><span class="sxs-lookup"><span data-stu-id="2e609-270">The URL for the Details page is `https://localhost:<port>/Students/Details?id=1`.</span></span> <span data-ttu-id="2e609-271">エンティティの主キー値がクエリ文字列に含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-271">The entity's primary key value is in the query string.</span></span> <span data-ttu-id="2e609-272">ルート データのキー値を渡すことを好む開発者もいます。`https://localhost:<port>/Students/Details/1`</span><span class="sxs-lookup"><span data-stu-id="2e609-272">Some developers prefer to pass the key value in route data: `https://localhost:<port>/Students/Details/1`.</span></span> <span data-ttu-id="2e609-273">詳細については、「[生成されたコードの更新](xref:tutorials/razor-pages/da1#update-the-generated-code)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e609-273">For more information, see [Update the generated code](xref:tutorials/razor-pages/da1#update-the-generated-code).</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="2e609-274">Create ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-274">Update the Create page</span></span>

<span data-ttu-id="2e609-275">Create ページのスキャフォールディングされた `OnPostAsync` コードは、[過剰ポスティング](#overposting)に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="2e609-275">The scaffolded `OnPostAsync` code for the Create page is vulnerable to [overposting](#overposting).</span></span> <span data-ttu-id="2e609-276">*Pages/Students/Create.cshtml.cs* の `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-276">Replace the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="2e609-277">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="2e609-277">TryUpdateModelAsync</span></span>

<span data-ttu-id="2e609-278">上記のコードでは、Student オブジェクトを作成し、ポストされたフォーム フィールドを使用して Student オブジェクトのプロパティを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e609-278">The preceding code creates a Student object and then uses posted form fields to update the Student object's properties.</span></span> <span data-ttu-id="2e609-279">[TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) メソッド:</span><span class="sxs-lookup"><span data-stu-id="2e609-279">The [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) method:</span></span>

* <span data-ttu-id="2e609-280">[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからポストされたフォーム値を使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-280">Uses the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span>
* <span data-ttu-id="2e609-281">リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="2e609-281">Updates only the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>
* <span data-ttu-id="2e609-282">"student" のプレフィックスを持つフォーム フィールドを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e609-282">Looks for form fields with a "student" prefix.</span></span> <span data-ttu-id="2e609-283">たとえば、`Student.FirstMidName` のようにします。</span><span class="sxs-lookup"><span data-stu-id="2e609-283">For example, `Student.FirstMidName`.</span></span> <span data-ttu-id="2e609-284">大文字と小文字の区別はありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-284">It's not case sensitive.</span></span>
* <span data-ttu-id="2e609-285">[モデル バインド](xref:mvc/models/model-binding) システムを使用して、文字列からフォーム値を `Student` モデル内の型に変換します。</span><span class="sxs-lookup"><span data-stu-id="2e609-285">Uses the [model binding](xref:mvc/models/model-binding) system to convert form values from strings to the types in the `Student` model.</span></span> <span data-ttu-id="2e609-286">たとえば、`EnrollmentDate` は DateTime に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-286">For example, `EnrollmentDate` has to be converted to DateTime.</span></span>

<span data-ttu-id="2e609-287">アプリを実行し、Create ページをテストする student エンティティを作成します。</span><span class="sxs-lookup"><span data-stu-id="2e609-287">Run the app, and create a student entity to test the Create page.</span></span>

## <a name="overposting"></a><span data-ttu-id="2e609-288">過剰ポスティング</span><span class="sxs-lookup"><span data-stu-id="2e609-288">Overposting</span></span>

<span data-ttu-id="2e609-289">ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-289">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="2e609-290">たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-290">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="2e609-291">アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-291">Even if the app doesn't have a `Secret` field on the create or update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="2e609-292">ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-292">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="2e609-293">元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。</span><span class="sxs-lookup"><span data-stu-id="2e609-293">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="2e609-294">`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、データベース内で更新されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-294">Whatever value the hacker specified for the `Secret` form field is updated in the database.</span></span> <span data-ttu-id="2e609-295">次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-295">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="2e609-297">挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-297">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="2e609-298">これは、アプリ デザイナーで、Create ページで `Secret` プロパティが設定されることを想定していない場合でも発生します。</span><span class="sxs-lookup"><span data-stu-id="2e609-298">That happens even though the app designer never intended the `Secret` property to be set with the Create page.</span></span>

### <a name="view-model"></a><span data-ttu-id="2e609-299">ビュー モデル</span><span class="sxs-lookup"><span data-stu-id="2e609-299">View model</span></span>

<span data-ttu-id="2e609-300">ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e609-300">View models provide an alternative way to prevent overposting.</span></span>

<span data-ttu-id="2e609-301">アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2e609-301">The application model is often called the domain model.</span></span> <span data-ttu-id="2e609-302">ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-302">The domain model typically contains all the properties required by the corresponding entity in the database.</span></span> <span data-ttu-id="2e609-303">ビュー モデルには、使用する UI に必要なプロパティのみが含まれています (たとえば、Create ページ)。</span><span class="sxs-lookup"><span data-stu-id="2e609-303">The view model contains only the properties needed for the UI that it is used for (for example, the Create page).</span></span>

<span data-ttu-id="2e609-304">一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-304">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> 

<span data-ttu-id="2e609-305">次の `Student` ビュー モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2e609-305">Consider the following `Student` view model:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Models/StudentVM.cs)]

<span data-ttu-id="2e609-306">次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e609-306">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="2e609-307">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-307">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="2e609-308">`SetValues` では一致するプロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-308">`SetValues` uses property name matching.</span></span> <span data-ttu-id="2e609-309">ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="2e609-309">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="2e609-310">`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-310">Using `StudentVM` requires [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/2-crud/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="2e609-311">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-311">Update the Edit page</span></span>

<span data-ttu-id="2e609-312">*Pages/Students/Edit.cshtml.cs* で、`OnGetAsync` メソッドと `OnPostAsync` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-312">In *Pages/Students/Edit.cshtml.cs*, replace the `OnGetAsync` and `OnPostAsync` methods with the following code.</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Edit.cshtml.cs?name=snippet_OnGetPost)]

<span data-ttu-id="2e609-313">コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-313">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="2e609-314">`FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-314">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="2e609-315">関連データを含める必要がない場合は、`FindAsync` の方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="2e609-315">When included related data is not needed, `FindAsync` is more efficient.</span></span>
* <span data-ttu-id="2e609-316">`OnPostAsync` には `id` パラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="2e609-316">`OnPostAsync` has an `id` parameter.</span></span>
* <span data-ttu-id="2e609-317">現在の学生は、空の学生を作成するのではなく、データベースからフェッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-317">The current student is fetched from the database, rather than creating an empty student.</span></span>

<span data-ttu-id="2e609-318">アプリを実行し、学生を作成して編集することでアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e609-318">Run the app, and test it by creating and editing a student.</span></span>

## <a name="entity-states"></a><span data-ttu-id="2e609-319">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="2e609-319">Entity States</span></span>

<span data-ttu-id="2e609-320">データベース コンテキストは、メモリ内のエンティティが、データベース内の対応する行と同期状態にあるかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="2e609-320">The database context keeps track of whether entities in memory are in sync with their corresponding rows in the database.</span></span> <span data-ttu-id="2e609-321">この追跡情報により、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。</span><span class="sxs-lookup"><span data-stu-id="2e609-321">This tracking information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="2e609-322">たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-322">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="2e609-323">`SaveChangesAsync` が呼び出されると、データベース コンテキストは SQL の INSERT コマンドを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-323">When `SaveChangesAsync` is called, the database context issues a SQL INSERT command.</span></span>

<span data-ttu-id="2e609-324">エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-324">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="2e609-325">`Added`:エンティティはデータベースにまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="2e609-325">`Added`: The entity doesn't yet exist in the database.</span></span> <span data-ttu-id="2e609-326">`SaveChanges` メソッドは INSERT ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-326">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="2e609-327">`Unchanged`:このエンティティでは変更を保存する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-327">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="2e609-328">エンティティがこの状態になるのは、エンティティがデータベースから読み取られた場合です。</span><span class="sxs-lookup"><span data-stu-id="2e609-328">An entity has this status when it's read from the database.</span></span>

* <span data-ttu-id="2e609-329">`Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-329">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="2e609-330">`SaveChanges` メソッドは UPDATE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-330">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="2e609-331">`Deleted`:エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="2e609-331">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="2e609-332">`SaveChanges` メソッドは DELETE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-332">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="2e609-333">`Detached`:エンティティはデータベース コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-333">`Detached`: The entity isn't being tracked by the database context.</span></span>

<span data-ttu-id="2e609-334">デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-334">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="2e609-335">エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-335">An entity is read, changes are made, and the entity state is automatically changed to `Modified`.</span></span> <span data-ttu-id="2e609-336">`SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-336">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="2e609-337">Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-337">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="2e609-338">ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-338">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="2e609-339">その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-339">Rereading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2e609-340">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-340">Update the Delete page</span></span>

<span data-ttu-id="2e609-341">このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装します。</span><span class="sxs-lookup"><span data-stu-id="2e609-341">In this section, you implement a custom error message when the call to `SaveChanges` fails.</span></span>

<span data-ttu-id="2e609-342">*Pages/Students/Delete.cshtml.cs* のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-342">Replace the code in *Pages/Students/Delete.cshtml.cs* with the following code.</span></span> <span data-ttu-id="2e609-343">変更が強調表示されます (`using` ステートメントのクリーンアップ以外)。</span><span class="sxs-lookup"><span data-stu-id="2e609-343">The changes are highlighted (other than cleanup of `using` statements).</span></span>

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Delete.cshtml.cs?name=snippet_All&highlight=20,22,30,38-41,53-71)]

<span data-ttu-id="2e609-344">上記のコードでは、省略可能なパラメーター `saveChangesError` を `OnGetAsync` メソッド シグネチャに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-344">The preceding code adds the optional parameter `saveChangesError` to the `OnGetAsync` method signature.</span></span> <span data-ttu-id="2e609-345">`saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-345">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="2e609-346">一時的なネットワークの問題により、削除操作が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-346">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="2e609-347">データベースがクラウド内にある場合は、一時的なネットワーク エラーが発生する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-347">Transient network errors are more likely when the database is in the cloud.</span></span> <span data-ttu-id="2e609-348">Delete ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` パラメーターは false です。</span><span class="sxs-lookup"><span data-stu-id="2e609-348">The `saveChangesError` parameter is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="2e609-349">`OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。</span><span class="sxs-lookup"><span data-stu-id="2e609-349">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

<span data-ttu-id="2e609-350">`OnPostAsync` メソッドは、選択されたエンティティを取得し、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出して、エンティティの状態を `Deleted` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2e609-350">The `OnPostAsync` method retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="2e609-351">`SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-351">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="2e609-352">`Remove` が失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="2e609-352">If `Remove` fails:</span></span>

* <span data-ttu-id="2e609-353">データベース例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-353">The database exception is caught.</span></span>
* <span data-ttu-id="2e609-354">[削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-354">The Delete page's `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

<span data-ttu-id="2e609-355">[削除] Razor ページ (*Pages/Students/Delete.cshtml*) にエラー メッセージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-355">Add an error message to the Delete Razor Page (*Pages/Students/Delete.cshtml*):</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Delete.cshtml?highlight=10)]

<span data-ttu-id="2e609-356">アプリを実行し、学生を削除して、Delete ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="2e609-356">Run the app and delete a student to test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2e609-357">次の手順</span><span class="sxs-lookup"><span data-stu-id="2e609-357">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2e609-358">[前のチュートリアル](xref:data/ef-rp/intro)
> [次のチュートリアル](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="2e609-358">[Previous tutorial](xref:data/ef-rp/intro)
[Next tutorial](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e609-359">このチュートリアルでは、スキャフォールディング CRUD (作成、読み取り、更新、削除) コードのレビューとカスタマイズを行います。</span><span class="sxs-lookup"><span data-stu-id="2e609-359">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="2e609-360">複雑さを最小限に抑え、これらのチュートリアルを通して主眼を EF Core に置くために、EF Core コードはページ モデルで使用されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-360">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="2e609-361">一部の開発者は、サービス レイヤーまたはリポジトリ パターンを使用して、UI (Razor Pages) とデータ アクセス層との間に抽象化レイヤーを作成しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-361">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="2e609-362">このチュートリアルでは、*Students* フォルダー内の [作成]、[編集]、[削除]、[詳細] の各 Razor Pages を確認します。</span><span class="sxs-lookup"><span data-stu-id="2e609-362">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="2e609-363">スキャフォールディング コードでは、[作成]、[編集]、[削除] ページに対して次のパターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-363">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="2e609-364">要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-364">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="2e609-365">データに加えた変更を HTTP POST メソッド `OnPostAsync` を使用して保存します。</span><span class="sxs-lookup"><span data-stu-id="2e609-365">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="2e609-366">[インデックス] および [詳細] ページでは、要求されたデータを HTTP GET メソッド `OnGetAsync` を使用して取得および表示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-366">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="2e609-367">SingleOrDefaultAsync と FirstOrDefaultAsync の比較</span><span class="sxs-lookup"><span data-stu-id="2e609-367">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="2e609-368">生成されたコードでは [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_) を使用しています。これは一般に [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) よりも好まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-368">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="2e609-369">`FirstOrDefaultAsync` は、1 つのエンティティをフェッチする際に `SingleOrDefaultAsync` よりも効率的です。</span><span class="sxs-lookup"><span data-stu-id="2e609-369">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="2e609-370">クエリから返されるエンティティが 1 つ以下であることを、コードで検証する必要がない。</span><span class="sxs-lookup"><span data-stu-id="2e609-370">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="2e609-371">`SingleOrDefaultAsync` がより多くのデータをフェッチし、不要な作業を行う。</span><span class="sxs-lookup"><span data-stu-id="2e609-371">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="2e609-372">フィルター部に適合するエンティティが複数存在する場合に `SingleOrDefaultAsync` によって例外がスローされる。</span><span class="sxs-lookup"><span data-stu-id="2e609-372">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="2e609-373">フィルター部に適合するエンティティが複数存在する場合に `FirstOrDefaultAsync` によって例外がスローされない。</span><span class="sxs-lookup"><span data-stu-id="2e609-373">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="2e609-374">FindAsync</span><span class="sxs-lookup"><span data-stu-id="2e609-374">FindAsync</span></span>

<span data-ttu-id="2e609-375">スキャフォールディング コードの多くでは、`FirstOrDefaultAsync` ではなく、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="2e609-375">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="2e609-376">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="2e609-376">`FindAsync`:</span></span>

* <span data-ttu-id="2e609-377">主キー (PK) を持つエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="2e609-377">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="2e609-378">PK を持つエンティティがコンテキストによって追跡されている場合、DB に対する要求がなくても該当するエンティティが返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-378">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="2e609-379">単純で簡潔です。</span><span class="sxs-lookup"><span data-stu-id="2e609-379">Is simple and concise.</span></span>
* <span data-ttu-id="2e609-380">単一のエンティティを検索するように最適化されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-380">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="2e609-381">状況によってはパフォーマンス上の利点がありますが、通常の Web アプリではほとんど利点がありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-381">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="2e609-382">[SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) ではなく、[FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) を暗黙的に使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-382">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="2e609-383">ただし、`Include` で他のエンティティを含める場合、`FindAsync` は適切ではなくなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-383">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="2e609-384">つまり、アプリの進行状況に応じて、`FindAsync` を止めてクエリに移行することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-384">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="2e609-385">Details ページをカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="2e609-385">Customize the Details page</span></span>

<span data-ttu-id="2e609-386">`Pages/Students` ページを参照します。</span><span class="sxs-lookup"><span data-stu-id="2e609-386">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="2e609-387">**[編集]** 、 **[詳細]** 、 **[削除]** の各リンクは、*Pages/Students/Index.cshtml* ファイルで [アンカー タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-387">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="2e609-388">アプリを実行し、 **[詳細]** リンクを選択します。</span><span class="sxs-lookup"><span data-stu-id="2e609-388">Run the app and select a **Details** link.</span></span> <span data-ttu-id="2e609-389">URL の形式は、 `http://localhost:5000/Students/Details?id=2` です。</span><span class="sxs-lookup"><span data-stu-id="2e609-389">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="2e609-390">クエリ文字列 (`?id=2`) によって受講者 ID が渡されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-390">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="2e609-391">`"{id:int}"` ルート テンプレートを使用するには、[編集]、[詳細]、[削除] の Razor ページを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e609-391">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="2e609-392">これらの各ページのページ ディレクティブを `@page` から `@page "{id:int}"` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2e609-392">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="2e609-393">整数ルート値を **含まない**、"{id:int}" ルート テンプレートを使用するページへの要求では、HTTP 404 (見つかりません) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-393">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="2e609-394">たとえば、 `http://localhost:5000/Students/Details` は 404 エラーを返します。</span><span class="sxs-lookup"><span data-stu-id="2e609-394">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="2e609-395">ID を省略するには、次のように `?` をルート制約に追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-395">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="2e609-396">アプリを実行し、[詳細] リンクをクリックし、URL がルート データとして ID を渡していることを確認します ( `http://localhost:5000/Students/Details/2` )。</span><span class="sxs-lookup"><span data-stu-id="2e609-396">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="2e609-397">`@page` から `@page "{id:int}"` への変更をグローバルに行わないでください。これを行うと、[ホーム] ページと [作成] ページへのリンクが解除されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-397">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="2e609-398">関連データを追加する</span><span class="sxs-lookup"><span data-stu-id="2e609-398">Add related data</span></span>

<span data-ttu-id="2e609-399">Students インデックス ページのスキャフォールディング コードには、`Enrollments` プロパティが含まれていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-399">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="2e609-400">このセクションでは、`Enrollments` コレクションのコンテンツが [詳細] ページに表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-400">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="2e609-401">*Pages/Students/Details.cshtml.cs* の `OnGetAsync` メソッドでは、`FirstOrDefaultAsync` メソッドを使用して単一の `Student` エンティティを取得します。</span><span class="sxs-lookup"><span data-stu-id="2e609-401">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="2e609-402">次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-402">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="2e609-403">[Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) メソッドと [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) メソッドにより、コンテキストは `Student.Enrollments` ナビゲーション プロパティと、各登録内の `Enrollment.Course` ナビゲーション プロパティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="2e609-403">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="2e609-404">これらのメソッドは、データの読み取りに関連するチュートリアルで詳しく検討します。</span><span class="sxs-lookup"><span data-stu-id="2e609-404">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="2e609-405">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) メソッドは、返されたエンティティが現在のコンテキストで更新されない場合のシナリオでパフォーマンスを改善します。</span><span class="sxs-lookup"><span data-stu-id="2e609-405">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="2e609-406">`AsNoTracking` は、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="2e609-406">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="2e609-407">[詳細] ページに関連する登録を表示する</span><span class="sxs-lookup"><span data-stu-id="2e609-407">Display related enrollments on the Details page</span></span>

<span data-ttu-id="2e609-408">*Pages/Students/Details.cshtml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="2e609-408">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="2e609-409">登録の一覧を表示するために、次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-409">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="2e609-410">コードを貼り付けた後でコードのインデントが乱れた場合は、CTRL + D + K キーを押してそれを修正します。</span><span class="sxs-lookup"><span data-stu-id="2e609-410">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="2e609-411">上記のコードは、`Enrollments` ナビゲーション プロパティ内でエンティティをループ処理します。</span><span class="sxs-lookup"><span data-stu-id="2e609-411">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="2e609-412">登録ごとに、コースのタイトルとグレードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-412">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="2e609-413">コース タイトルは、Enrollments エンティティの `Course` ナビゲーション プロパティに格納されている Course エンティティから取得されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-413">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="2e609-414">アプリを実行し、 **[Students]** タブを選択し、学生用の **[詳細]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2e609-414">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="2e609-415">選択した受講者のコースとグレードの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-415">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="2e609-416">[作成] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-416">Update the Create page</span></span>

<span data-ttu-id="2e609-417">次のコードを使用して、*Pages/Students/Create.cshtml.cs* 内の `OnPostAsync` メソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e609-417">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="2e609-418">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="2e609-418">TryUpdateModelAsync</span></span>

<span data-ttu-id="2e609-419">次の [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) コードを検討します。</span><span class="sxs-lookup"><span data-stu-id="2e609-419">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="2e609-420">上記のコードで、`TryUpdateModelAsync<Student>` は、[PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) の [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) プロパティからのポストされたフォームの値を使用して `emptyStudent` オブジェクトの更新を試みます。</span><span class="sxs-lookup"><span data-stu-id="2e609-420">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="2e609-421">`TryUpdateModelAsync` は、リストされたプロパティのみを更新します (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`)。</span><span class="sxs-lookup"><span data-stu-id="2e609-421">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="2e609-422">上記のサンプルについて:</span><span class="sxs-lookup"><span data-stu-id="2e609-422">In the preceding sample:</span></span>

* <span data-ttu-id="2e609-423">2 番目の引数 (`"student", // Prefix`) には、値を検索するためのプレフィックスが使用されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-423">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="2e609-424">大文字と小文字の区別はありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-424">It's not case sensitive.</span></span>
* <span data-ttu-id="2e609-425">ポストされたフォームの値は、[モデル バインディング](xref:mvc/models/model-binding) を使用して `Student` モデル内の型に変換されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-425">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="2e609-426">過剰ポスティング</span><span class="sxs-lookup"><span data-stu-id="2e609-426">Overposting</span></span>

<span data-ttu-id="2e609-427">ポストされた値を持つフィールドを更新するために `TryUpdateModel` を使用することは、過剰ポスティングの防止につながり、セキュリティ上のベスト プラクティスとなります。</span><span class="sxs-lookup"><span data-stu-id="2e609-427">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="2e609-428">たとえば、Student エンティティには、この Web ページで更新または追加できない `Secret` プロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-428">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="2e609-429">アプリの作成または更新の Razor ページに `Secret` フィールドが含まれていない場合でも、ハッカーは過剰ポスティングによって `Secret` 値を設定することが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-429">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="2e609-430">ハッカーは、Fiddler などのツールを使用するか、または何らかの JavaScript を作成して、`Secret` フォーム値をポストすることが可能です。</span><span class="sxs-lookup"><span data-stu-id="2e609-430">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="2e609-431">元のコードでは、Student インスタンスの作成時にモデル バインダーによって使用されるフィールドを制限していません。</span><span class="sxs-lookup"><span data-stu-id="2e609-431">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="2e609-432">`Secret` フォーム フィールドに対してハッカーが指定した値はいずれも、DB 内で更新されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-432">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="2e609-433">次の図では、Fiddler ツールを使用して、ポストされたフォームの値に `Secret` フィールド (値 "OverPost" を含む) が追加されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-433">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler による Secret フィールドの追加](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="2e609-435">挿入された行の `Secret` プロパティに値 "OverPost" が正常に追加されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-435">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="2e609-436">アプリ デザイナーは、[作成] ページで `Secret` プロパティが設定されることを想定していませんでした。</span><span class="sxs-lookup"><span data-stu-id="2e609-436">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="2e609-437">ビュー モデル</span><span class="sxs-lookup"><span data-stu-id="2e609-437">View model</span></span>

<span data-ttu-id="2e609-438">ビュー モデルには、通常、アプリケーションで使用されるモデルに含まれるプロパティのサブセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-438">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="2e609-439">アプリケーション モデルは、しばしばドメイン モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2e609-439">The application model is often called the domain model.</span></span> <span data-ttu-id="2e609-440">ドメイン モデルには、通常、データベース内の対応するエンティティによって必要とされるすべてのプロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-440">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="2e609-441">ビュー モデルには、UI レイヤーで必要なプロパティのみが含まれています (たとえば、[作成] ページ)。</span><span class="sxs-lookup"><span data-stu-id="2e609-441">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="2e609-442">一部のアプリでは、ビュー モデルに加えて、Razor Pages のページ モデル クラスとブラウザーとの間でデータを渡すためにバインド モデルまたは入力モデルも使用します。</span><span class="sxs-lookup"><span data-stu-id="2e609-442">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="2e609-443">次の `Student` ビュー モデルを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="2e609-443">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="2e609-444">ビュー モデルは、過剰ポスティングを防ぐもう 1 つの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="2e609-444">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="2e609-445">ビュー モデルには、表示または更新されるプロパティのみが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-445">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="2e609-446">次のコードでは、`StudentVM` ビュー モデルを使用して新しい受講生を作成します。</span><span class="sxs-lookup"><span data-stu-id="2e609-446">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="2e609-447">[SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) メソッドでは、このオブジェクトの値を設定するために、別の [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) オブジェクトから値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2e609-447">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="2e609-448">`SetValues` では一致するプロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-448">`SetValues` uses property name matching.</span></span> <span data-ttu-id="2e609-449">ビュー モデルの型はモデルの型に関連している必要はなく、プロパティが一致している必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="2e609-449">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="2e609-450">`StudentVM` を使用するには、`Student` ではなく `StudentVM` を使用するように [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-450">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="2e609-451">Razor Pages で、`PageModel` 派生クラスはビュー モデルです。</span><span class="sxs-lookup"><span data-stu-id="2e609-451">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="2e609-452">[編集] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-452">Update the Edit page</span></span>

<span data-ttu-id="2e609-453">[編集] ページのページ モデルを更新します。</span><span class="sxs-lookup"><span data-stu-id="2e609-453">Update the page model for the Edit page.</span></span> <span data-ttu-id="2e609-454">大きな変更点が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-454">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="2e609-455">コードの変更は [作成] ページに似ています。ただし、次のようないくつかの例外があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-455">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="2e609-456">`OnPostAsync` には省略可能な `id` パラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="2e609-456">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="2e609-457">現在の受講者は、空の受講者を作成するのではなく、DB からフェッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-457">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="2e609-458">`FirstOrDefaultAsync` は、[FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) に置換されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-458">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="2e609-459">主キーからエンティティを選択する場合は、`FindAsync` をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2e609-459">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="2e609-460">詳細については、「[FindAsync](#FindAsync)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2e609-460">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="2e609-461">[編集] ページと [作成] ページをテストする</span><span class="sxs-lookup"><span data-stu-id="2e609-461">Test the Edit and Create pages</span></span>

<span data-ttu-id="2e609-462">いくつかの受講者エンティティを作成して編集します。</span><span class="sxs-lookup"><span data-stu-id="2e609-462">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="2e609-463">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="2e609-463">Entity States</span></span>

<span data-ttu-id="2e609-464">DB コンテキストは、メモリ内のエンティティが、DB 内の対応する行と同期状態にあるかどうかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="2e609-464">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="2e609-465">DB コンテキストの同期情報から、[SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) が呼び出されたときに何が起こっているかを特定できます。</span><span class="sxs-lookup"><span data-stu-id="2e609-465">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="2e609-466">たとえば、新しいエンティティが [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) メソッドに渡されたとき、そのエンティティの状態は [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-466">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="2e609-467">`SaveChangesAsync` が呼び出されると、DB コンテキストは SQL の INSERT コマンドを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-467">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="2e609-468">エンティティは、[次のいずれかの状態](/dotnet/api/microsoft.entityframeworkcore.entitystate)になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-468">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="2e609-469">`Added`:エンティティは DB にまだ存在しません。</span><span class="sxs-lookup"><span data-stu-id="2e609-469">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="2e609-470">`SaveChanges` メソッドは INSERT ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-470">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="2e609-471">`Unchanged`:このエンティティでは変更を保存する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="2e609-471">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="2e609-472">エンティティがこの状態になるのは、エンティティが DB から読み取られた場合です。</span><span class="sxs-lookup"><span data-stu-id="2e609-472">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="2e609-473">`Modified`:エンティティのプロパティ値の一部またはすべてが変更されています。</span><span class="sxs-lookup"><span data-stu-id="2e609-473">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="2e609-474">`SaveChanges` メソッドは UPDATE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-474">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="2e609-475">`Deleted`:エンティティには削除のマークが付けられています。</span><span class="sxs-lookup"><span data-stu-id="2e609-475">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="2e609-476">`SaveChanges` メソッドは DELETE ステートメントを発行します。</span><span class="sxs-lookup"><span data-stu-id="2e609-476">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="2e609-477">`Detached`:エンティティは DB コンテキストによって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="2e609-477">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="2e609-478">デスクトップ アプリにおいて、通常、状態の変更は自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-478">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="2e609-479">エンティティが読み取られ、変更が加えられると、エンティティの状態は自動的に `Modified` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-479">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="2e609-480">`SaveChanges` を呼び出すと、変更されたプロパティのみを更新する SQL UPDATE ステートメントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-480">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="2e609-481">Web アプリにおいて、エンティティを読み取り、データを表示する `DbContext` は、ページが表示された後で破棄されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-481">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="2e609-482">ページの `OnPostAsync` メソッドが呼び出されると、新しい Web 要求が行われ、`DbContext` の新しいインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-482">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="2e609-483">その新しいコンテキスト内のエンティティの再読み取りを行うと、デスクトップの処理がシミュレートされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-483">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2e609-484">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-484">Update the Delete page</span></span>

<span data-ttu-id="2e609-485">このセクションでは、`SaveChanges` の呼び出しが失敗したときにカスタム エラー メッセージを実装するためのコードが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-485">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="2e609-486">考えられるエラー メッセージを含められるように文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-486">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="2e609-487">`OnGetAsync` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-487">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="2e609-488">上記のコードには、省略可能なパラメーター `saveChangesError` が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2e609-488">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="2e609-489">`saveChangesError` は、受講者オブジェクトの削除に失敗した後で、メソッドが呼び出されたかどうかを示します。</span><span class="sxs-lookup"><span data-stu-id="2e609-489">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="2e609-490">一時的なネットワークの問題により、削除操作が失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-490">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="2e609-491">一時的なネットワーク エラーが高い可能性でクラウド内に発生しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-491">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="2e609-492">[削除] ページの `OnGetAsync` が UI から呼び出された場合、`saveChangesError` は false です。</span><span class="sxs-lookup"><span data-stu-id="2e609-492">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="2e609-493">`OnPostAsync` によって `OnGetAsync` が呼び出された場合 (削除操作が失敗したため)、`saveChangesError` パラメーターは true です。</span><span class="sxs-lookup"><span data-stu-id="2e609-493">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="2e609-494">[削除] ページの OnPostAsync メソッド</span><span class="sxs-lookup"><span data-stu-id="2e609-494">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="2e609-495">`OnPostAsync` を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2e609-495">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="2e609-496">上記のコードでは、選択されたエンティティを取得してから、[Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) メソッドを呼び出してエンティティの状態を `Deleted` に設定しています。</span><span class="sxs-lookup"><span data-stu-id="2e609-496">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="2e609-497">`SaveChanges` が呼び出されると、SQL DELETE コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-497">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="2e609-498">`Remove` が失敗した場合:</span><span class="sxs-lookup"><span data-stu-id="2e609-498">If `Remove` fails:</span></span>

* <span data-ttu-id="2e609-499">DB 例外がキャッチされます。</span><span class="sxs-lookup"><span data-stu-id="2e609-499">The DB exception is caught.</span></span>
* <span data-ttu-id="2e609-500">[削除] ページの `OnGetAsync` メソッドが、`saveChangesError=true` を指定して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2e609-500">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-no-locrazor-page"></a><span data-ttu-id="2e609-501">[削除] Razor ページを更新する</span><span class="sxs-lookup"><span data-stu-id="2e609-501">Update the Delete Razor Page</span></span>

<span data-ttu-id="2e609-502">次の強調表示されたエラー メッセージを [削除] Razor ページに追加します。</span><span class="sxs-lookup"><span data-stu-id="2e609-502">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="2e609-503">[削除] をテストします。</span><span class="sxs-lookup"><span data-stu-id="2e609-503">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="2e609-504">一般的なエラー</span><span class="sxs-lookup"><span data-stu-id="2e609-504">Common errors</span></span>

<span data-ttu-id="2e609-505">[受講者]/[インデックス] またはその他のリンクが機能しません。</span><span class="sxs-lookup"><span data-stu-id="2e609-505">Students/Index or other links don't work:</span></span>

<span data-ttu-id="2e609-506">Razor ページに正しい `@page` ディレクティブが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2e609-506">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="2e609-507">たとえば、Students/Index Razor ページにルート テンプレートを含めることは **できません**。</span><span class="sxs-lookup"><span data-stu-id="2e609-507">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="2e609-508">各 Razor ページには、`@page` ディレクティブを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="2e609-508">Each Razor Page must include the `@page` directive.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="2e609-509">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2e609-509">Additional resources</span></span>

* [<span data-ttu-id="2e609-510">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="2e609-510">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="2e609-511">[前へ](xref:data/ef-rp/intro)
> [次へ](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="2e609-511">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>

::: moniker-end
