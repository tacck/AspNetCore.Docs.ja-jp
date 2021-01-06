---
title: 'パート 2: モデルを追加する'
author: rick-anderson
description: Razor ページのチュートリアル シリーズのパート 2。 このセクションでは、モデル クラスを追加します。
ms.author: riande
ms.date: 11/11/2020
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485993"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="2fe29-104">パート 2: ASP.NET Core で Razor ページ アプリにモデルを追加する</span><span class="sxs-lookup"><span data-stu-id="2fe29-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="2fe29-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fe29-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2fe29-106">このセクションでは、データベースで映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="2fe29-107">アプリのモデル クラスでは、[Entity Framework Core (EF Core)](/ef/core) を使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2fe29-108">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッパー (O/RM) です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="2fe29-109">モデル クラスを記述すると、EF Core によってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="2fe29-110">モデル クラスは EF Core に対する依存関係がないため、**P** lain-**O** ld **C** LR **O** bjects (単純な従来の CLR) の頭文字を取って POCO クラスと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="2fe29-111">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2fe29-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2fe29-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2fe29-113">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2fe29-115">**ソリューション エクスプローラー** で、 *RazorPagesMovie* プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2fe29-116">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="2fe29-117">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="2fe29-118">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="2fe29-119">クラスに *Movie* と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="2fe29-120">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-121">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-122">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-123">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの種類 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-124">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-124">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-125">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-126">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2fe29-128">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="2fe29-129">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2fe29-130">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-131">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-132">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-133">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの種類 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-134">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-134">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-135">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-136">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2fe29-137">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2fe29-138">データベース コンテキスト クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-138">Add a database context class</span></span>

1. <span data-ttu-id="2fe29-139">*RazorPagesMovie* プロジェクト内に、*Data* という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="2fe29-140">*Data* フォルダーに、次のコードを使用して *RazorPagesMovieContext.cs* という名前のファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="2fe29-141">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2fe29-142">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2fe29-143">このコードは、後の手順で依存関係が追加されるまでコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2fe29-144">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-144">Add a database connection string</span></span>

<span data-ttu-id="2fe29-145">次の強調表示されたコードに示されているように、 *appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2fe29-146">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2fe29-146">Register the database context</span></span>

1. <span data-ttu-id="2fe29-147">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="2fe29-148">データベース コンテキストを `Startup.ConfigureServices` の[依存関係の挿入](xref:fundamentals/dependency-injection)コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-149">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2fe29-150">**ソリューション ツール ウィンドウ** で、Ctrl キーを押したまま *RazorPagesMovie* プロジェクトをクリックしてから、 **[追加]** > **[新しいフォルダー...]** を選択します。フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="2fe29-151">Ctrl キーを押したまま *Models* フォルダーをクリックし、 **[追加]** > **[新しいファイル...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="2fe29-152">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="2fe29-153">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="2fe29-154">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="2fe29-155">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="2fe29-156">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-157">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-158">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-159">`[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの種類 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-160">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-160">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-161">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-162">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="2fe29-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="2fe29-164">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2fe29-165">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2fe29-165">Scaffold the movie model</span></span>

<span data-ttu-id="2fe29-166">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2fe29-167">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2fe29-169">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="2fe29-170">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="2fe29-171">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="2fe29-172">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![前の手順からのイメージ。](model/_static/5/sca.png)

1. <span data-ttu-id="2fe29-174">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![前の手順からのイメージ。](model/_static/add_scaffold.png)

1. <span data-ttu-id="2fe29-176">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="2fe29-177">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="2fe29-178">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="2fe29-179">**[データ コンテキストの追加]** ダイアログで、クラス名 *RazorPagesMovie.Data.RazorPagesMovieContext* が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="2fe29-180">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-180">Select **Add**.</span></span>

   ![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="2fe29-182">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2fe29-184">*Program.cs*、*Startup.cs*、および *.csproj* ファイルが含まれているプロジェクト ディレクトリでコマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2fe29-185">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2fe29-186">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2fe29-187">次の表で、ASP.NET Core コード ジェネレーターのオプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="2fe29-188">オプション</span><span class="sxs-lookup"><span data-stu-id="2fe29-188">Option</span></span>               | <span data-ttu-id="2fe29-189">説明</span><span class="sxs-lookup"><span data-stu-id="2fe29-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2fe29-190">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="2fe29-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2fe29-191">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2fe29-192">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2fe29-193">ビューを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2fe29-194">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2fe29-195">`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2fe29-196">詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2fe29-197">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="2fe29-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2fe29-198">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2fe29-199">次のコードは、`Startup` に <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="2fe29-200">`IWebHostEnvironment` が挿入されているので、アプリでは、開発で SQLite を、運用環境で SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-201">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2fe29-202">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="2fe29-203">Ctrl キーを押したまま *Pages* フォルダーをクリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="2fe29-204">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="2fe29-205">Ctrl キーを押したまま *Pages/Movies* フォルダーをクリックし、 **[追加]** > **[新しいスキャフォールディング...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![前の手順からのイメージ。](model/_static/scaMac.png)

1. <span data-ttu-id="2fe29-207">**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="2fe29-209">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="2fe29-210">**DbContext Class to use:** 行で、クラスに *RazorPagesMovie.Data.RazorPagesMovieContext* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="2fe29-211">**[完了]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-211">Select **Finish**.</span></span>

   ![前の手順からのイメージ。](model/_static/5/arpMac.png)

<span data-ttu-id="2fe29-213">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2fe29-214">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="2fe29-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2fe29-215">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2fe29-216">次のコードは、`Startup` に <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="2fe29-217">`IWebHostEnvironment` が挿入されているので、アプリでは、開発で SQLite を、運用環境で SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="2fe29-218">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="2fe29-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-220">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2fe29-221">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2fe29-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2fe29-223">更新済み</span><span class="sxs-lookup"><span data-stu-id="2fe29-223">Updated</span></span>

* <span data-ttu-id="2fe29-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-224">*Startup.cs*</span></span>

<span data-ttu-id="2fe29-225">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fe29-227">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2fe29-228">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2fe29-229">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-230">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fe29-231">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2fe29-232">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2fe29-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2fe29-234">更新済み</span><span class="sxs-lookup"><span data-stu-id="2fe29-234">Updated</span></span>

* <span data-ttu-id="2fe29-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-235">*Startup.cs*</span></span>

<span data-ttu-id="2fe29-236">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="2fe29-237">EF の移行機能を使用して初期データベース スキーマを作成する</span><span class="sxs-lookup"><span data-stu-id="2fe29-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="2fe29-238">Entity Framework Core の移行機能を使用すると、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="2fe29-239">初期データベース スキーマを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-239">Create the initial database schema.</span></span>
* <span data-ttu-id="2fe29-240">データベース スキーマを増分方式で更新して、アプリケーションのデータ モデルとの同期を維持する。</span><span class="sxs-lookup"><span data-stu-id="2fe29-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="2fe29-241">データベース内の既存のデータは保持されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-243">このセクションでは、**パッケージ マネージャー コンソール** (PMC) ウィンドウを使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="2fe29-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="2fe29-244">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-244">Add an initial migration.</span></span>
* <span data-ttu-id="2fe29-245">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="2fe29-246">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC メニュー](model/_static/5/pmc.png)

1. <span data-ttu-id="2fe29-248">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="2fe29-250">次の .NET CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="2fe29-251">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2fe29-252">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2fe29-253">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="2fe29-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2fe29-254">この警告は、後の手順で解決されるため、無視してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="2fe29-255">`migrations` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2fe29-256">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2fe29-257">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2fe29-258">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2fe29-259">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2fe29-260">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2fe29-262">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="2fe29-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2fe29-263">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2fe29-264">アプリケーションの起動時に、EF Core データベース コンテキストなどのサービスが依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2fe29-265">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2fe29-266">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2fe29-267">スキャフォールディング ツールによってデータベース コンテキストが自動的に作成され、依存関係挿入コンテナーに登録されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2fe29-268">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2fe29-269">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2fe29-270">`RazorPagesMovieContext` によって、`Movie` モデル用の EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2fe29-271">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2fe29-272">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2fe29-273">上記のコードによって、エンティティ セットの [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="2fe29-274">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2fe29-275">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2fe29-276">[DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2fe29-277">ローカル開発の場合、[構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-278">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2fe29-279">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="2fe29-280">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2fe29-280">Test the app</span></span>

1. <span data-ttu-id="2fe29-281">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="2fe29-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="2fe29-282">次のエラー メッセージが表示される場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="2fe29-283">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="2fe29-284">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-284">Test the **Create** link.</span></span>

   ![[作成] ページ](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="2fe29-286">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2fe29-287">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2fe29-288">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="2fe29-289">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2fe29-290">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe29-291">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2fe29-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2fe29-292">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2fe29-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="2fe29-293">このセクションでは、映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="2fe29-294">アプリのモデル クラスでは、[Entity Framework Core (EF Core)](/ef/core) を使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="2fe29-295">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッパー (O/RM) です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="2fe29-296">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2fe29-297">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2fe29-298">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2fe29-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2fe29-299">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-301">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2fe29-302">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-302">Name the folder *Models*.</span></span>

<span data-ttu-id="2fe29-303">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="2fe29-304">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="2fe29-305">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-305">Name the class **Movie**.</span></span>

<span data-ttu-id="2fe29-306">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-307">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-308">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-309">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-310">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-310">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-311">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-312">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2fe29-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2fe29-315">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2fe29-316">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2fe29-317">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-318">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-319">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-320">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-321">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-321">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-322">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-323">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2fe29-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2fe29-325">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2fe29-326">データベース コンテキスト クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-326">Add a database context class</span></span>

* <span data-ttu-id="2fe29-327">*RazorPagesMovie* プロジェクト内に、*Data* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="2fe29-328">次の `RazorPagesMovieContext` クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2fe29-329">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2fe29-330">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2fe29-331">このコードは、後の手順で依存関係が追加されるまでコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2fe29-332">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-332">Add a database connection string</span></span>

<span data-ttu-id="2fe29-333">次の強調表示されたコードに示されているように、 *appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2fe29-334">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2fe29-334">Register the database context</span></span>

<span data-ttu-id="2fe29-335">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2fe29-336">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-337">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2fe29-338">**ソリューション ツール ウィンドウ** で、Ctrl キーを押したまま **RazorPagesMovie** プロジェクトをクリックしてから、 **[追加]** > **[新しいフォルダー...]** を選択します。フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="2fe29-339">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="2fe29-340">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2fe29-341">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2fe29-342">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2fe29-343">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="2fe29-344">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-345">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-346">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-347">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-348">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-348">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-349">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-350">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="2fe29-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="2fe29-352">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2fe29-353">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2fe29-353">Scaffold the movie model</span></span>

<span data-ttu-id="2fe29-354">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2fe29-355">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-357">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2fe29-358">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2fe29-359">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="2fe29-360">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="2fe29-362">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="2fe29-364">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2fe29-365">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2fe29-366">**データ コンテキスト クラス** 行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2fe29-367">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2fe29-368">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2fe29-369">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-369">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="2fe29-371">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2fe29-373">*Program.cs*、*Startup.cs*、および *.csproj* ファイルが含まれるプロジェクト ディレクトリでコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2fe29-374">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2fe29-375">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2fe29-376">次の表で、ASP.NET Core コード ジェネレーターのオプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="2fe29-377">オプション</span><span class="sxs-lookup"><span data-stu-id="2fe29-377">Option</span></span>               | <span data-ttu-id="2fe29-378">説明</span><span class="sxs-lookup"><span data-stu-id="2fe29-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2fe29-379">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="2fe29-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2fe29-380">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2fe29-381">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2fe29-382">ビューを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2fe29-383">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2fe29-384">`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2fe29-385">詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2fe29-386">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="2fe29-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2fe29-387">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2fe29-388">次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="2fe29-389">`IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-390">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fe29-391">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2fe29-392">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2fe29-393">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="2fe29-394">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[新しいスキャフォールディング...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="2fe29-396">**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2fe29-398">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2fe29-399">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2fe29-400">**データ コンテキスト クラス** 行で、新しいクラスの名前「RazorPagesMovie.**Data**.RazorPagesMovieContext」と入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="2fe29-401">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="2fe29-402">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="2fe29-403">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-403">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="2fe29-405">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="2fe29-406">EF ツールの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-406">Add EF tools</span></span>

<span data-ttu-id="2fe29-407">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="2fe29-408">上記のコマンドを実行すると、.NET Core CLI の Entity Framework Core ツールが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="2fe29-409">詳細については、「[Entity Framework Core ツール リファレンス - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="2fe29-410">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="2fe29-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="2fe29-411">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="2fe29-412">次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="2fe29-413">`IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="2fe29-414">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="2fe29-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-416">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2fe29-417">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2fe29-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2fe29-419">更新済み</span><span class="sxs-lookup"><span data-stu-id="2fe29-419">Updated</span></span>

* <span data-ttu-id="2fe29-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-420">*Startup.cs*</span></span>

<span data-ttu-id="2fe29-421">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fe29-423">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="2fe29-424">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2fe29-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="2fe29-426">更新済み</span><span class="sxs-lookup"><span data-stu-id="2fe29-426">Updated</span></span>

* <span data-ttu-id="2fe29-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-427">*Startup.cs*</span></span>

<span data-ttu-id="2fe29-428">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2fe29-430">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="2fe29-431">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="2fe29-432">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2fe29-433">最初の移行</span><span class="sxs-lookup"><span data-stu-id="2fe29-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-435">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="2fe29-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2fe29-436">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-436">Add an initial migration.</span></span>
* <span data-ttu-id="2fe29-437">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="2fe29-438">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2fe29-440">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-441">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="2fe29-442">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="2fe29-443">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2fe29-444">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2fe29-445">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="2fe29-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2fe29-446">この警告は、後の手順で解決されるため、無視してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="2fe29-447">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="2fe29-448">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="2fe29-449">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="2fe29-450">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="2fe29-451">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="2fe29-452">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2fe29-454">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="2fe29-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2fe29-455">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2fe29-456">アプリケーションの起動時に、EF Core データベース コンテキスト context などのサービスが依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2fe29-457">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="2fe29-458">この後のチュートリアルで、データベース コンテキスト context インスタンスを取得するコンストラクター コードを示します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2fe29-459">スキャフォールディング ツールによってデータベース コンテキスト context が自動的に作成され、依存関係挿入コンテナーに登録されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2fe29-460">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2fe29-461">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="2fe29-462">`RazorPagesMovieContext` によって、`Movie` モデル用の EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2fe29-463">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2fe29-464">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2fe29-465">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2fe29-466">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2fe29-467">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2fe29-468">[DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2fe29-469">ローカル開発の場合、[構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2fe29-471">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2fe29-472">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2fe29-472">Test the app</span></span>

* <span data-ttu-id="2fe29-473">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="2fe29-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2fe29-474">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2fe29-475">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2fe29-476">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-476">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="2fe29-478">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2fe29-479">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2fe29-480">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2fe29-481">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2fe29-482">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe29-483">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2fe29-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2fe29-484">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2fe29-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fe29-485">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="2fe29-486">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="2fe29-487">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="2fe29-488">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="2fe29-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="2fe29-489">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="2fe29-490">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="2fe29-491">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="2fe29-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="2fe29-492">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-494">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** 、 **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="2fe29-495">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-495">Name the folder *Models*.</span></span>

<span data-ttu-id="2fe29-496">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="2fe29-497">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="2fe29-498">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-498">Name the class **Movie**.</span></span>

<span data-ttu-id="2fe29-499">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-500">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-501">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-502">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-503">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-503">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-504">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-505">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2fe29-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2fe29-508">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="2fe29-509">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="2fe29-510">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-511">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-512">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-513">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-514">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-514">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-515">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-516">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2fe29-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="2fe29-518">NuGet パッケージと EF ツールを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="2fe29-519">データベース コンテキスト クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-519">Add a database context class</span></span>

<span data-ttu-id="2fe29-520">Razor PagesMovie プロジェクト内に、*Data* という名前の新しいフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="2fe29-521">次の `RazorPagesMovieContext` クラスを *Data* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2fe29-522">上記のコードによって、エンティティ セットの `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="2fe29-523">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="2fe29-524">このコードは、後の手順で依存関係が追加されるまでコンパイルされません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="2fe29-525">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-525">Add a database connection string</span></span>

<span data-ttu-id="2fe29-526">次の強調表示されたコードに示されているように、 *appsettings.json* ファイルに接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="2fe29-527">必要な NuGet パッケージの追加</span><span class="sxs-lookup"><span data-stu-id="2fe29-527">Add required NuGet packages</span></span>

<span data-ttu-id="2fe29-528">次の .NET Core CLI コマンドを実行し、SQLite と CodeGeneration.Design をプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="2fe29-529">スキャフォールディングには `Microsoft.VisualStudio.Web.CodeGeneration.Design` パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="2fe29-530">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2fe29-530">Register the database context</span></span>

<span data-ttu-id="2fe29-531">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="2fe29-532">`Startup.ConfigureServices` で[依存性の挿入](xref:fundamentals/dependency-injection)コンテナーを使用し、データベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="2fe29-533">エラー チェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-534">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2fe29-535">**ソリューション ツール ウィンドウ** で、Ctrl キーを押したまま *RazorPagesMovie* プロジェクトをクリックしてから、 **[追加]**  >  **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="2fe29-536">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="2fe29-537">Ctrl キーを押したまま *Models* フォルダーをクリックし、 **[追加]** > **[新しいファイル]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="2fe29-538">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="2fe29-539">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="2fe29-540">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="2fe29-541">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="2fe29-542">`Movie` クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="2fe29-543">`Movie` クラスには次が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="2fe29-544">`ID` フィールドは、データベースで主キー用に必要です。</span><span class="sxs-lookup"><span data-stu-id="2fe29-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="2fe29-545">`[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) 属性では、データの型 (`Date`) を指定します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="2fe29-546">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="2fe29-546">With this attribute:</span></span>

  * <span data-ttu-id="2fe29-547">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="2fe29-548">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="2fe29-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="2fe29-550">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="2fe29-551">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2fe29-551">Scaffold the movie model</span></span>

<span data-ttu-id="2fe29-552">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="2fe29-553">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-555">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2fe29-556">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2fe29-557">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="2fe29-558">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="2fe29-560">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="2fe29-562">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="2fe29-563">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="2fe29-564">**データ コンテキスト クラス** 行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2fe29-565">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-565">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="2fe29-567">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2fe29-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2fe29-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="2fe29-569">*Program.cs*、*Startup.cs*、および *.csproj* ファイルが含まれるプロジェクト ディレクトリでコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="2fe29-570">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="2fe29-571">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="2fe29-572">次の表で、ASP.NET Core コード ジェネレーターのオプションについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="2fe29-573">オプション</span><span class="sxs-lookup"><span data-stu-id="2fe29-573">Option</span></span>               | <span data-ttu-id="2fe29-574">説明</span><span class="sxs-lookup"><span data-stu-id="2fe29-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="2fe29-575">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="2fe29-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="2fe29-576">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="2fe29-577">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="2fe29-578">ビューを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="2fe29-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="2fe29-579">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="2fe29-580">`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得するには、`-h` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="2fe29-581">詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-582">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2fe29-583">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="2fe29-584">Ctrl キーを押したまま *Pages* フォルダーをクリックし、 **[追加]** > **[新しいフォルダー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="2fe29-585">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="2fe29-586">Ctrl キーを押したまま *Pages/Movies* フォルダーをクリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="2fe29-588">**[新しいスキャフォールディングの追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="2fe29-590">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="2fe29-591">**[モデル クラス]** ドロップ ダウンで、 **[Movie]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="2fe29-592">**データ コンテキスト クラス** 行で、 **RazorPagesMovieContext** を選択します。これにより、新しいデータベース コンテキスト クラスが正しい名前空間で作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="2fe29-593">このクラスでは、 **RazorPagesMovie.Models.RazorPagesMovieContext** となります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="2fe29-594">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-594">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="2fe29-596">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="2fe29-597">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="2fe29-598">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="2fe29-598">Files created</span></span>

* <span data-ttu-id="2fe29-599">*Pages/Movies*: Create、Delete、Details、Edit、および Index。</span><span class="sxs-lookup"><span data-stu-id="2fe29-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="2fe29-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="2fe29-601">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="2fe29-601">File updated</span></span>

* <span data-ttu-id="2fe29-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="2fe29-602">*Startup.cs*</span></span>

<span data-ttu-id="2fe29-603">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="2fe29-604">最初の移行</span><span class="sxs-lookup"><span data-stu-id="2fe29-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2fe29-606">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="2fe29-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="2fe29-607">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-607">Add an initial migration.</span></span>
* <span data-ttu-id="2fe29-608">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="2fe29-609">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="2fe29-611">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="2fe29-612">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="2fe29-613">スキーマは、 *RazorPagesMovieContext.cs* ファイルの `DbContext` に指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="2fe29-614">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="2fe29-615">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="2fe29-616">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="2fe29-617">`Update-Database` コマンドによって、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="2fe29-618">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-619">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="2fe29-620">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="2fe29-621">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="2fe29-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="2fe29-622">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="2fe29-623">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="2fe29-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="2fe29-624">この警告は、後の手順で解決されるため、無視してください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2fe29-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2fe29-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="2fe29-626">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="2fe29-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="2fe29-627">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="2fe29-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2fe29-628">アプリケーションの起動時に、EF Core データベース コンテキスト context などのサービスが依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2fe29-629">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2fe29-630">この後のチュートリアルで、データベース コンテキスト contextB コンテキスト インスタンスを取得するコンストラクター コードを示します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="2fe29-631">スキャフォールディング ツールによってデータベース コンテキスト context が自動的に作成され、依存関係挿入コンテナーに登録されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="2fe29-632">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2fe29-633">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="2fe29-634">`RazorPagesMovieContext` によって、`Movie` モデル用の EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="2fe29-635">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="2fe29-636">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="2fe29-637">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="2fe29-638">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="2fe29-639">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2fe29-640">[DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="2fe29-641">ローカル開発の場合、[構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2fe29-642">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2fe29-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2fe29-643">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="2fe29-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="2fe29-644">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="2fe29-644">Test the app</span></span>

* <span data-ttu-id="2fe29-645">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="2fe29-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="2fe29-646">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="2fe29-647">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="2fe29-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="2fe29-648">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-648">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="2fe29-650">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="2fe29-651">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fe29-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="2fe29-652">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2fe29-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="2fe29-653">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2fe29-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="2fe29-654">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2fe29-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe29-655">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2fe29-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2fe29-656">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor ページ](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="2fe29-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
