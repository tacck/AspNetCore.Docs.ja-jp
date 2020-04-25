---
title: ASP.NET Core での Razor ページ アプリへのモデルの追加
author: rick-anderson
description: Entity Framework Core (EF Core) を利用し、データベースでムービーを管理するためのクラスを追加する方法について説明します。
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7f7c2a09b74e6007ee3ea9c038398bac54988186
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488872"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a><span data-ttu-id="893dd-103">ASP.NET Core での Razor ページ アプリへのモデルの追加</span><span class="sxs-lookup"><span data-stu-id="893dd-103">Add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="893dd-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="893dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="893dd-105">このセクションでは、映画を管理するクラスが追加されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="893dd-106">アプリのモデル クラスでは、[Entity Framework Core (EF Core)](/ef/core) を使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="893dd-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="893dd-107">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッパー (O/RM) です。</span><span class="sxs-lookup"><span data-stu-id="893dd-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="893dd-108">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="893dd-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="893dd-109">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="893dd-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="893dd-110">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="893dd-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-112">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="893dd-113">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-113">Name the folder *Models*.</span></span>

<span data-ttu-id="893dd-114">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="893dd-114">Right click the *Models* folder.</span></span> <span data-ttu-id="893dd-115">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="893dd-116">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="893dd-118">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="893dd-119">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="893dd-121">Solution Pad で、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="893dd-122">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="893dd-123">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="893dd-124">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="893dd-125">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="893dd-126">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="893dd-127">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="893dd-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="893dd-128">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="893dd-128">Scaffold the movie model</span></span>

<span data-ttu-id="893dd-129">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="893dd-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="893dd-130">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-132">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="893dd-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="893dd-133">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="893dd-134">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-134">Name the folder *Movies*</span></span>

<span data-ttu-id="893dd-135">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="893dd-137">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="893dd-139">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="893dd-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="893dd-140">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="893dd-141">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 RazorPagesMovie.**Models**.RazorPagesMovieContext を RazorPagesMovie.**Data**.RazorPagesMovieContext に変更します。</span><span class="sxs-lookup"><span data-stu-id="893dd-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="893dd-142">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="893dd-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="893dd-143">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="893dd-144">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="893dd-144">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/3/arp.png)

<span data-ttu-id="893dd-146">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="893dd-148">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="893dd-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="893dd-149">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="893dd-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="893dd-150">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="893dd-151">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="893dd-153">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="893dd-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="893dd-154">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="893dd-155">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-155">Name the folder *Movies*</span></span>

<span data-ttu-id="893dd-156">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[新しいスキャフォールディング]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="893dd-158">**[新しいスキャフォールディング]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="893dd-160">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="893dd-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="893dd-161">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="893dd-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="893dd-162">**データ コンテキスト クラス**行で、新しいクラスの名前「RazorPagesMovie.**Data**.RazorPagesMovieContext」を入力します。</span><span class="sxs-lookup"><span data-stu-id="893dd-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="893dd-163">[この変更](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)は必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="893dd-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="893dd-164">これにより、正しい名前空間を使用してデータベース コンテキスト クラスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="893dd-165">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="893dd-165">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="893dd-167">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="893dd-168">EF ツールの追加</span><span class="sxs-lookup"><span data-stu-id="893dd-168">Add EF tools</span></span>

<span data-ttu-id="893dd-169">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="893dd-170">上記のコマンドを実行すると、.NET Core CLI の Entity Framework Core ツールが追加されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="893dd-171">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="893dd-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-173">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="893dd-174">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="893dd-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="893dd-175">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="893dd-176">更新済み</span><span class="sxs-lookup"><span data-stu-id="893dd-176">Updated</span></span>

* <span data-ttu-id="893dd-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-177">*Startup.cs*</span></span>

<span data-ttu-id="893dd-178">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="893dd-180">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="893dd-181">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="893dd-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="893dd-182">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="893dd-183">更新済み</span><span class="sxs-lookup"><span data-stu-id="893dd-183">Updated</span></span>

* <span data-ttu-id="893dd-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-184">*Startup.cs*</span></span>

<span data-ttu-id="893dd-185">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="893dd-187">スキャフォールディングのプロセスでは、次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="893dd-188">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="893dd-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="893dd-189">作成されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="893dd-190">最初の移行</span><span class="sxs-lookup"><span data-stu-id="893dd-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-192">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="893dd-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="893dd-193">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-193">Add an initial migration.</span></span>
* <span data-ttu-id="893dd-194">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="893dd-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="893dd-195">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="893dd-197">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="893dd-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="893dd-200">上記のコマンドで次の警告が生成されます。"エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="893dd-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="893dd-201">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="893dd-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="893dd-202">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。"</span><span class="sxs-lookup"><span data-stu-id="893dd-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="893dd-203">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="893dd-204">移行コマンドによって、最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="893dd-205">このスキーマは、`DbContext` で指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="893dd-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="893dd-206">`InitialCreate` 引数は移行の命名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="893dd-207">任意の名前を使用できますが、規則により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="893dd-208">`update` コマンドにより、適用されていない移行で `Up` メソッドが実行されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="893dd-209">この場合、`update` により、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="893dd-211">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="893dd-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="893dd-212">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="893dd-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="893dd-213">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="893dd-214">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="893dd-215">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="893dd-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="893dd-216">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="893dd-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="893dd-217">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="893dd-218">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="893dd-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="893dd-219">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="893dd-220">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="893dd-221">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="893dd-222">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="893dd-223">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="893dd-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="893dd-224">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="893dd-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="893dd-225">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="893dd-226">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="893dd-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="893dd-228">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="893dd-230">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="893dd-231">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="893dd-231">Test the app</span></span>

* <span data-ttu-id="893dd-232">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="893dd-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="893dd-233">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="893dd-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="893dd-234">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="893dd-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="893dd-235">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="893dd-235">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="893dd-237">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="893dd-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="893dd-238">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="893dd-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="893dd-239">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="893dd-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="893dd-240">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="893dd-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="893dd-241">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="893dd-242">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="893dd-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="893dd-243">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="893dd-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="893dd-244">このセクションでは、クロスプラットフォーム [SQLite データベース](https://www.sqlite.org/index.html)でムービーを管理するためのクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="893dd-245">ASP.NET Core テンプレートから作成されたアプリでは、SQLite データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="893dd-246">このアプリのモデル クラスは、データベースを操作するために [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core データベース プロバイダー](/ef/core/providers/sqlite)) で使用されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="893dd-247">EF Core は、データ アクセスを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="893dd-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="893dd-248">このモデル クラスは、EF Core に対する依存関係がないために、POCO クラス (plain-old CLR オブジェクト、つまり単純な従来の CLR) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="893dd-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="893dd-249">これらは、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="893dd-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="893dd-250">データ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="893dd-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-252">**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="893dd-253">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-253">Name the folder *Models*.</span></span>

<span data-ttu-id="893dd-254">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="893dd-254">Right click the *Models* folder.</span></span> <span data-ttu-id="893dd-255">**[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="893dd-256">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="893dd-258">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="893dd-259">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="893dd-261">ソリューション エクスプローラーで、**RazorPagesMovie** プロジェクトを右クリックし、 **[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="893dd-262">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="893dd-263">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="893dd-264">**[新しいファイル]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="893dd-265">左側のウィンドウで **[全般]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="893dd-266">中央ウィンドウで **[空のクラス]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="893dd-267">クラスに **Movie** という名前を付け、 **[新規]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="893dd-268">プロジェクトをビルドして、コンパイル エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="893dd-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="893dd-269">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="893dd-269">Scaffold the movie model</span></span>

<span data-ttu-id="893dd-270">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="893dd-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="893dd-271">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-273">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="893dd-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="893dd-274">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="893dd-275">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-275">Name the folder *Movies*</span></span>

<span data-ttu-id="893dd-276">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/sca.png)

<span data-ttu-id="893dd-278">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffold.png)

<span data-ttu-id="893dd-280">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="893dd-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="893dd-281">**[モデル クラス]** ドロップ ダウンで、 **[Movie (RazorPagesMovie.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="893dd-282">**データ コンテキスト クラス**行で、 **+** (プラス) 記号を選択し、生成された名前 **RazorPagesMovie.Models.RazorPagesMovieContext** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="893dd-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="893dd-283">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="893dd-283">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arp.png)

<span data-ttu-id="893dd-285">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="893dd-287">プロジェクト ディレクトリ (*Program.cs*、*Startup.cs*、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="893dd-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="893dd-288">**Windows の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="893dd-289">**macOS および Linux の場合**:次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="893dd-291">*Pages/Movies* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="893dd-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="893dd-292">*Pages* フォルダーを右クリックし、 **[追加]** > **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="893dd-293">フォルダーに *Movies* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="893dd-293">Name the folder *Movies*</span></span>

<span data-ttu-id="893dd-294">*Pages/Movies* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![前の手順からのイメージ。](model/_static/scaMac.png)

<span data-ttu-id="893dd-296">**[新しいスキャフォールディングの追加]** ダイアログで、 **[Entity Framework を使用する Razor Pages (CRUD)]** > **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![前の手順からのイメージ。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="893dd-298">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="893dd-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="893dd-299">**[モデル クラス]** ドロップ ダウンで、 **[Movie]** を選択するか、または入力します。</span><span class="sxs-lookup"><span data-stu-id="893dd-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="893dd-300">**データ コンテキスト クラス** 行で、 **[RazorPagesMovieContext]** を選択または入力します。これにより、新しい db コンテキスト クラスが正しい名前空間で作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="893dd-301">このクラスでは、**RazorPagesMovie.Models.RazorPagesMovieContext** となります。</span><span class="sxs-lookup"><span data-stu-id="893dd-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="893dd-302">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="893dd-302">Select **Add**.</span></span>

![前の手順からのイメージ。](model/_static/arpMac.png)

<span data-ttu-id="893dd-304">*appsettings.json* ファイルは、ローカル データベースへの接続に使用される接続文字列を使用して更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="893dd-305">スキャフォールディングのプロセスが作成され、次のファイルが更新されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="893dd-306">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="893dd-306">Files created</span></span>

* <span data-ttu-id="893dd-307">*Pages/Movies*: 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="893dd-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="893dd-308">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="893dd-309">更新されたファイル</span><span class="sxs-lookup"><span data-stu-id="893dd-309">File updated</span></span>

* <span data-ttu-id="893dd-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="893dd-310">*Startup.cs*</span></span>

<span data-ttu-id="893dd-311">作成および更新されたファイルについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="893dd-312">最初の移行</span><span class="sxs-lookup"><span data-stu-id="893dd-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="893dd-314">このセクションでは、パッケージ マネージャー コンソール (PMC) を使用して、次の作業を行います。</span><span class="sxs-lookup"><span data-stu-id="893dd-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="893dd-315">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="893dd-315">Add an initial migration.</span></span>
* <span data-ttu-id="893dd-316">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="893dd-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="893dd-317">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="893dd-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC メニュー](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="893dd-319">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="893dd-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="893dd-320">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="893dd-321">このスキーマは、`DbContext` で指定されたモデルに基づきます (*RazorPagesMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="893dd-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="893dd-322">`InitialCreate` 引数は、移行の名前を指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="893dd-323">任意の名前を使用できますが、規則により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="893dd-324">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="893dd-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="893dd-325">`Update-Database` コマンドは、*Migrations/\<time-stamp>_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="893dd-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="893dd-326">`Up` メソッドにより、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="893dd-329">上記のコマンドで次の警告が生成されます。"*エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。* " この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="893dd-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="893dd-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="893dd-331">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="893dd-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="893dd-332">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="893dd-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="893dd-333">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="893dd-334">これらのサービスを必要とするコンポーネント (Razor ページなど) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="893dd-335">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="893dd-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="893dd-336">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="893dd-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="893dd-337">`Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="893dd-338">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="893dd-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="893dd-339">`RazorPagesMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="893dd-340">データ コンテキスト (`RazorPagesMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="893dd-341">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="893dd-342">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="893dd-343">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="893dd-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="893dd-344">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="893dd-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="893dd-345">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="893dd-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="893dd-346">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="893dd-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="893dd-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="893dd-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="893dd-348">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="893dd-349">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="893dd-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="893dd-350">`Up` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="893dd-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="893dd-351">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="893dd-351">Test the app</span></span>

* <span data-ttu-id="893dd-352">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="893dd-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="893dd-353">エラーが発生した場合は、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="893dd-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="893dd-354">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="893dd-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="893dd-355">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="893dd-355">Test the **Create** link.</span></span>

  ![[作成] ページ](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="893dd-357">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="893dd-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="893dd-358">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="893dd-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="893dd-359">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="893dd-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="893dd-360">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="893dd-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="893dd-361">次のチュートリアルでは、スキャフォールディングによって作成されるファイルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="893dd-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="893dd-362">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="893dd-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="893dd-363">[前へ:はじめに](xref:tutorials/razor-pages/razor-pages-start)
> [次: スキャフォールディングされた Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="893dd-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
