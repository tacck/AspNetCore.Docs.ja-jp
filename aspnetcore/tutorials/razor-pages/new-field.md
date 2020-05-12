---
title: ASP.NET Core で Razor ページに新しいフィールドを追加する
author: rick-anderson
description: Entity Framework Core を使用した Razor ページへの新しいフィールドの追加方法を示します
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 683d6718f4dcdb73c45cbcf94f6ac4f477b71bcd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769735"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="d0c90-103">ASP.NET Core で Razor ページに新しいフィールドを追加する</span><span class="sxs-lookup"><span data-stu-id="d0c90-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="d0c90-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d0c90-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d0c90-105">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d0c90-106">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="d0c90-106">Add a new field to the model.</span></span>
* <span data-ttu-id="d0c90-107">新しいフィールド スキーマの変更をデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="d0c90-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d0c90-108">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d0c90-109">データベースに `__EFMigrationsHistory` テーブルが追加され、データベースのスキーマが生成元のモデル クラスと同期しているかどうかが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d0c90-110">モデル クラスがデータベースと同期されていない場合、EF は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="d0c90-111">同期中のスキーマ/モデルが自動的に検証されるようにすると、整合性のないデータベース/コードの問題を発見しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d0c90-112">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="d0c90-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d0c90-113">*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d0c90-114">アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-114">Build the app.</span></span>

<span data-ttu-id="d0c90-115">*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="d0c90-116">次のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-116">Update the following pages:</span></span>

* <span data-ttu-id="d0c90-117">[削除] と [詳細] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d0c90-118">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d0c90-119">[編集] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d0c90-120">DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="d0c90-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d0c90-121">データベースを更新せずにアプリを実行すると、次の `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d0c90-122">`SqlException` 例外が発生するのは、更新された Movie モデル クラスが、データベースの Movie テーブルのスキーマと異なるためです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d0c90-123">(データベース テーブルに `Rating` 列はありません)。</span><span class="sxs-lookup"><span data-stu-id="d0c90-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d0c90-124">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d0c90-125">Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d0c90-126">この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d0c90-127">これの欠点は、データベースで既存のデータが失われることです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d0c90-128">実稼働データベースでこの方法は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="d0c90-129">スキーマの変更時に DB を削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d0c90-130">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d0c90-131">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d0c90-132">この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d0c90-133">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d0c90-134">このチュートリアルでは、Code First Migrations を利用します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d0c90-135">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d0c90-136">下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d0c90-137">[完成した SeedData.cs ファイル](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d0c90-138">ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0c90-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0c90-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d0c90-140">評価フィールドの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="d0c90-140">Add a migration for the rating field</span></span>

<span data-ttu-id="d0c90-141">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d0c90-142">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d0c90-143">`Add-Migration` コマンドにより、フレームワークに次の指示があります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d0c90-144">`Movie` モデルを、`Movie` DB のスキーマと比較します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d0c90-145">DB スキーマを新しいモデルに移行するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d0c90-146">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d0c90-147">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="d0c90-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d0c90-148">`Update-Database` コマンドを使うと、データベースにスキーマ変更を適用し、既存のデータを保持するようにフレームワークに指示できます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d0c90-149">DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d0c90-150">これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d0c90-151">別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d0c90-152">SSOX 内でデータベースを削除するには:</span><span class="sxs-lookup"><span data-stu-id="d0c90-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d0c90-153">SSOX でデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="d0c90-154">データベースを右クリックし、 *[削除]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="d0c90-155">**[既存の接続を閉じる]** をチェックします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d0c90-156">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-156">Select **OK**.</span></span>
* <span data-ttu-id="d0c90-157">[PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d0c90-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d0c90-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d0c90-159">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="d0c90-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d0c90-160">移行フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-160">Delete the migration folder.</span></span>  <span data-ttu-id="d0c90-161">次のコマンドを使用して、データベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="d0c90-162">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d0c90-163">データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0c90-164">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d0c90-164">Additional resources</span></span>

* [<span data-ttu-id="d0c90-165">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="d0c90-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d0c90-166">[前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ: 検証の追加](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d0c90-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d0c90-167">このセクションでは [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations を次の目的で使用します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d0c90-168">モデルに新しいフィールドを追加する。</span><span class="sxs-lookup"><span data-stu-id="d0c90-168">Add a new field to the model.</span></span>
* <span data-ttu-id="d0c90-169">新しいフィールド スキーマの変更をデータベースに移行する。</span><span class="sxs-lookup"><span data-stu-id="d0c90-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d0c90-170">EF Code First を使用してデータベースを自動的に作成する場合、Code First では次が実行されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d0c90-171">テーブルをデータベースに追加し、データベースのスキーマが生成元のモデル クラスと同期しているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d0c90-172">モデル クラスがデータベースと同期されていない場合、EF は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="d0c90-173">同期中のスキーマ/モデルが自動的に検証されるようにすると、整合性のないデータベース/コードの問題を発見しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d0c90-174">ムービー モデルへの評価プロパティの追加</span><span class="sxs-lookup"><span data-stu-id="d0c90-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d0c90-175">*Models/Movie.cs* ファイルを開き、`Rating` プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d0c90-176">アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-176">Build the app.</span></span>

<span data-ttu-id="d0c90-177">*Pages/Movies/Index.cshtml* を編集し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="d0c90-178">次のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-178">Update the following pages:</span></span>

* <span data-ttu-id="d0c90-179">[削除] と [詳細] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d0c90-180">[Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) を `Rating` フィールドを使用して更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d0c90-181">[編集] ページに、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d0c90-182">DB を更新して新しいフィールドが含まれるようになるまでアプリは動作しません。</span><span class="sxs-lookup"><span data-stu-id="d0c90-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d0c90-183">ここで実行すると、アプリによって `SqlException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d0c90-184">このエラーが表示されるのは、更新された Movie モデル クラスがデータベースの Movie テーブルのスキーマと異なるためです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d0c90-185">(データベース テーブルに `Rating` 列はありません)。</span><span class="sxs-lookup"><span data-stu-id="d0c90-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d0c90-186">このエラーを解決するための手法がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d0c90-187">Entity Framework に、新しいモデル クラス スキーマを使用してデータベースを自動的にドロップさせ、再作成させます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d0c90-188">この手法は、開発周期の早い段階で便利です。モデルとデータベース スキーマを一緒に短期間で発展させることができます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d0c90-189">これの欠点は、データベースで既存のデータが失われることです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d0c90-190">実稼働データベースでこの方法は使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="d0c90-191">スキーマの変更時に DB を削除し、初期化子を使用して、データベースにテスト データを自動的にシードすることは、多くの場合、アプリケーションを開発する上で有益な方法となります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d0c90-192">モデル クラスに一致するように、既存のデータベースのスキーマを明示的に変更します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d0c90-193">この手法の長所は、データが維持されることです。</span><span class="sxs-lookup"><span data-stu-id="d0c90-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d0c90-194">この変更は手動で行うことも、データベース変更スクリプトを作成して行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d0c90-195">Code First Migrations を使用して、データベース スキーマを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d0c90-196">このチュートリアルでは、Code First Migrations を利用します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d0c90-197">新しい列に値を提供するように、`SeedData` クラスを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d0c90-198">下に変更のサンプルがありますが、`new Movie` ブロックごとにこの変更を行ってください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d0c90-199">[完成した SeedData.cs ファイル](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d0c90-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d0c90-200">ソリューションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d0c90-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d0c90-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d0c90-202">評価フィールドの移行を追加する</span><span class="sxs-lookup"><span data-stu-id="d0c90-202">Add a migration for the rating field</span></span>

<span data-ttu-id="d0c90-203">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d0c90-204">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d0c90-205">`Add-Migration` コマンドにより、フレームワークに次の指示があります。</span><span class="sxs-lookup"><span data-stu-id="d0c90-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d0c90-206">`Movie` モデルを、`Movie` DB のスキーマと比較します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d0c90-207">DB スキーマを新しいモデルに移行するコードを作成します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d0c90-208">"Rating (評価)" という名前は任意です。移行ファイルに名前を付けるために利用されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d0c90-209">移行ファイルには意味のある名前を使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="d0c90-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d0c90-210">フレームワークは、データベースにスキーマ変更を適用するように、`Update-Database` コマンドから指示されます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d0c90-211">DB 内のすべてのレコードを削除すると、初期化子は DB にデータを初期投入し、`Rating` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d0c90-212">これはブラウザーの削除リンクで行うか、[Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) から行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d0c90-213">別のオプションとしては、データベースを削除してから、移行を使用することで、データベースを再作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="d0c90-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d0c90-214">SSOX 内でデータベースを削除するには:</span><span class="sxs-lookup"><span data-stu-id="d0c90-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d0c90-215">SSOX でデータベースを選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="d0c90-216">データベースを右クリックし、 *[削除]* を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="d0c90-217">**[既存の接続を閉じる]** をチェックします。</span><span class="sxs-lookup"><span data-stu-id="d0c90-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d0c90-218">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-218">Select **OK**.</span></span>
* <span data-ttu-id="d0c90-219">[PMC](xref:tutorials/razor-pages/new-field#pmc) でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d0c90-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d0c90-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d0c90-221">データベースを削除して再作成する</span><span class="sxs-lookup"><span data-stu-id="d0c90-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d0c90-222">データベースを削除し、移行を使ってデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d0c90-223">データベースを削除するには、データベース ファイル (*MvcMovie.db*) を削除します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d0c90-224">次に、`ef database update` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="d0c90-225">アプリを実行し、`Rating` フィールドでムービーを作成、編集、表示できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d0c90-226">データベースがシード処理されない場合は、`SeedData.Initialize` メソッドでブレークポイントを設定します。</span><span class="sxs-lookup"><span data-stu-id="d0c90-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d0c90-227">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d0c90-227">Additional resources</span></span>

* [<span data-ttu-id="d0c90-228">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="d0c90-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d0c90-229">[前へ:検索の追加](xref:tutorials/razor-pages/search)
> [次へ: 検証の追加](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d0c90-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
