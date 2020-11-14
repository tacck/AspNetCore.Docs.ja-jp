---
title: パート 4、ASP.NET Core MVC アプリにモデルを追加する
author: rick-anderson
description: ASP.NET Core MVC のチュートリアル シリーズのパート 4。
ms.author: riande
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422756"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="20515-103">パート 4、ASP.NET Core MVC アプリにモデルを追加する</span><span class="sxs-lookup"><span data-stu-id="20515-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="20515-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT) および [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="20515-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="20515-105">このセクションでは、データベースのムービーを管理するクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="20515-106">これらのクラスは、 **M** VC アプリの " **モ** デル" 部分です。</span><span class="sxs-lookup"><span data-stu-id="20515-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="20515-107">[Entity Framework Core](/ef/core) (EF Core) でこれらのクラスを使用して、データベースを操作します。</span><span class="sxs-lookup"><span data-stu-id="20515-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="20515-108">EF Core は、記述する必要があるデータ アクセス コードを簡略化するオブジェクト リレーショナル マッピング (ORM) フレームワークです。</span><span class="sxs-lookup"><span data-stu-id="20515-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="20515-109">作成するモデル クラスは、EF Core に対する依存関係がないために、POCO クラス ( **P** lain- **O** ld **C** LR **O** bjects から) と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="20515-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="20515-110">これらは単に、データベースに格納されるデータのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="20515-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="20515-111">このチュートリアルでは、まずモデル クラスを記述し、EF コアによってデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="20515-112">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="20515-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-114">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="20515-115">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="20515-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20515-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20515-117">*Movie.cs* という名前のファイルを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20515-119">*Models* フォルダーを右クリックし、 **[追加]**  >  **[新しいクラス]**  >  **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="20515-120">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="20515-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="20515-121">次のコードで *Movie.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="20515-122">`Movie` クラスには、データベースで主キー用に必要となる `Id` フィールドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="20515-123">`ReleaseDate` の <xref:System.ComponentModel.DataAnnotations.DataType> 属性により、データの型 (`Date`) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="20515-124">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="20515-124">With this attribute:</span></span>

* <span data-ttu-id="20515-125">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="20515-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="20515-126">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="20515-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="20515-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="20515-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="20515-128">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="20515-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-130">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="20515-132">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="20515-133">上記のコマンドによって EF Core SQL Server プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="20515-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="20515-134">プロバイダー パッケージによって、依存関係として EF Core パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="20515-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="20515-135">追加のパッケージは、このチュートリアルの後半で、スキャフォールディングの手順で自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="20515-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20515-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-137">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20515-138">**[プロジェクト]** メニューから、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="20515-139">右上の **[検索]** フィールドに「`Microsoft.EntityFrameworkCore.SQLite`」と入力し、 **Return** キーを押して検索します。</span><span class="sxs-lookup"><span data-stu-id="20515-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="20515-140">一致する NuGet パッケージを選択し、 **[パッケージの追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet パッケージを追加する](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="20515-142">**[プロジェクトの選択]** ダイアログが、`MvcMovie` プロジェクトが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="20515-143">**[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-143">Press the **Ok** button.</span></span>

<span data-ttu-id="20515-144">**[ライセンスの同意]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="20515-145">必要に応じてライセンスを確認し、 **[同意する]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="20515-146">上記の手順を繰り返して、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="20515-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="20515-147">データベース コンテキスト クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="20515-147">Create a database context class</span></span>

<span data-ttu-id="20515-148">データベース コンテキスト クラスは、`Movie` モデルの EF Core 機能 (作成、読み取り、更新、削除) を調整するために必要です。</span><span class="sxs-lookup"><span data-stu-id="20515-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="20515-149">データベース コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生し、データ モデルに含めるエンティティを指定します。</span><span class="sxs-lookup"><span data-stu-id="20515-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="20515-150">*Data* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="20515-150">Create a *Data* folder.</span></span>

<span data-ttu-id="20515-151">次のコードで *Data/MvcMovieContext.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="20515-152">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="20515-153">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="20515-154">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="20515-155">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="20515-155">Register the database context</span></span>

<span data-ttu-id="20515-156">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="20515-157">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20515-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="20515-158">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="20515-159">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="20515-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="20515-160">このセクションでは、DI コンテナーにデータベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="20515-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="20515-161">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="20515-162">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-164">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="20515-165">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="20515-166">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="20515-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="20515-167">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="20515-167">Add a database connection string</span></span>

<span data-ttu-id="20515-168">接続文字列を *appsettings.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-170">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="20515-171">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="20515-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="20515-172">ムービー ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="20515-172">Scaffold movie pages</span></span>

<span data-ttu-id="20515-173">スキャフォールディング ツールを使用し、ムービー モデルの作成、読み取り、更新、削除の (CRUD) ページを生成します。</span><span class="sxs-lookup"><span data-stu-id="20515-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-175">*ソリューション エクスプローラー* で、 **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="20515-177">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="20515-179">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="20515-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="20515-180">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="20515-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="20515-181">**データ コンテキスト クラス:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="20515-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc5.png)

* <span data-ttu-id="20515-183">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="20515-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="20515-184">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="20515-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="20515-185">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-185">Select **Add**</span></span>

<span data-ttu-id="20515-186">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-186">Visual Studio creates:</span></span>

* <span data-ttu-id="20515-187">ムービー コントローラー ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="20515-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="20515-188">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="20515-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="20515-189">このようなファイルの自動作成は、" *スキャフォールディング* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="20515-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="20515-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="20515-191">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="20515-192">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="20515-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="20515-193">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="20515-195">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="20515-196">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="20515-197">データベースが存在しないため、スキャフォールディング ページをまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="20515-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="20515-198">アプリを実行し、 **[Movie App]** リンクをクリックすると、 *[データベースを開けません]* または *[そのようなテーブルはありません:Movie* ] というエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="20515-199">最初の移行</span><span class="sxs-lookup"><span data-stu-id="20515-199">Initial migration</span></span>

<span data-ttu-id="20515-200">EF Core [移行](xref:data/ef-mvc/migrations)機能を使用し、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="20515-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="20515-201">移行は、データ モデルに合わせてデータベースを作成したり、更新したりできる一連のツールです。</span><span class="sxs-lookup"><span data-stu-id="20515-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-203">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="20515-204">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="20515-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="20515-205">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="20515-206">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="20515-207">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="20515-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="20515-208">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="20515-209">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="20515-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="20515-210">`Update-Database`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="20515-211">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="20515-212">データベース更新コマンドにより、次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="20515-213">エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="20515-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="20515-214">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="20515-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="20515-215">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="20515-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="20515-216">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="20515-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-217">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="20515-218">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="20515-219">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="20515-220">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="20515-221">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="20515-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="20515-222">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="20515-223">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます ( *Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="20515-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="20515-224">`ef database update`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="20515-225">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="20515-226">InitialCreate クラス</span><span class="sxs-lookup"><span data-stu-id="20515-226">The InitialCreate class</span></span>

<span data-ttu-id="20515-227">*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="20515-228">`Up` メソッドにより Movie テーブルが作成され、主キーとして `Id` が構成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="20515-229">`Down` メソッドにより、`Up` 移行で行われたスキーマ変更が元に戻ります。</span><span class="sxs-lookup"><span data-stu-id="20515-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="20515-230">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="20515-230">Test the app</span></span>

* <span data-ttu-id="20515-231">アプリを実行し、 **[Movie App]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="20515-232">次のような例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="20515-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-234">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="20515-235">[移行手順](#migration)を実行しなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="20515-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="20515-236">**Create** ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-236">Test the **Create** page.</span></span> <span data-ttu-id="20515-237">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="20515-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="20515-238">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="20515-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="20515-239">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20515-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="20515-240">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="20515-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="20515-241">**[編集]** 、 **[詳細]** 、 **[削除]** の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="20515-242">コントローラーの依存関係挿入</span><span class="sxs-lookup"><span data-stu-id="20515-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-244">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="20515-245">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="20515-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="20515-246">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-247">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="20515-248">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="20515-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="20515-249">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="20515-250">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="20515-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="20515-251">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="20515-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="20515-252">次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="20515-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="20515-253">`IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="20515-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="20515-254">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="20515-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="20515-255">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="20515-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="20515-256">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="20515-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="20515-257">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="20515-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="20515-258">この厳密に型指定された方法では、コンパイル時にコードを確認できます。</span><span class="sxs-lookup"><span data-stu-id="20515-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="20515-259">スキャフォールディング メカニズムでは、`MoviesController` のクラスとビューで、この手法 (つまり、厳密に型指定されたモデルを渡しました) が使用されました。</span><span class="sxs-lookup"><span data-stu-id="20515-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="20515-260">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="20515-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="20515-261">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="20515-262">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="20515-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="20515-263">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="20515-264">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="20515-265">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="20515-266">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="20515-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="20515-267">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="20515-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="20515-268">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="20515-269">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="20515-270">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="20515-271">ビュー ファイルの一番上にある `@model` ステートメントにより、ビューで求められるオブジェクトの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="20515-272">ムービー コントローラーが作成されたとき、次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="20515-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="20515-273">この `@model` ディレクティブにより、コントローラーでビューに渡されたムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="20515-274">`Model` オブジェクトは厳密に型指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="20515-275">たとえば、 *Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="20515-276">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="20515-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="20515-277">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="20515-278">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="20515-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="20515-279">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="20515-280">ムービー コントローラーが作成されたとき、スキャフォールディングにより、 *Index.cshtml* ファイルの一番上に次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="20515-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="20515-281">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="20515-282">たとえば、 *Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="20515-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="20515-283">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="20515-284">これには、コンパイル時にコードを確認できるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="20515-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20515-285">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="20515-285">Additional resources</span></span>

* [<span data-ttu-id="20515-286">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="20515-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="20515-287">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="20515-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="20515-288">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル SQL の使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="20515-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="20515-289">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="20515-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-291">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="20515-292">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="20515-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20515-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="20515-294">*Movie.cs* という名前のファイルを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-295">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20515-296">*Models* フォルダーを右クリックし、 **[追加]**  >  **[新しいクラス]**  >  **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="20515-297">ファイルに *Movie.cs* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="20515-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="20515-298">次のコードで *Movie.cs* ファイルを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="20515-299">`Movie` クラスには、データベースで主キー用に必要となる `Id` フィールドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="20515-300">`ReleaseDate` の <xref:System.ComponentModel.DataAnnotations.DataType> 属性により、データの型 (`Date`) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="20515-301">この属性を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="20515-301">With this attribute:</span></span>

* <span data-ttu-id="20515-302">ユーザーは日付フィールドに時刻の情報を入力する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="20515-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="20515-303">日付のみが表示され、時刻の情報は表示されません。</span><span class="sxs-lookup"><span data-stu-id="20515-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="20515-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) は、後のチュートリアルで説明されます。</span><span class="sxs-lookup"><span data-stu-id="20515-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="20515-305">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="20515-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-307">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="20515-309">PMC で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="20515-310">上記のコマンドによって EF Core SQL Server プロバイダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="20515-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="20515-311">プロバイダー パッケージによって、依存関係として EF Core パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="20515-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="20515-312">追加のパッケージは、このチュートリアルの後半で、スキャフォールディングの手順で自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="20515-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20515-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-314">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="20515-315">**[プロジェクト]** メニューから、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="20515-316">右上の **[検索]** フィールドに「`Microsoft.EntityFrameworkCore.SQLite`」と入力し、 **Return** キーを押して検索します。</span><span class="sxs-lookup"><span data-stu-id="20515-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="20515-317">一致する NuGet パッケージを選択し、 **[パッケージの追加]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![Entity Framework Core NuGet パッケージを追加する](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="20515-319">**[プロジェクトの選択]** ダイアログが、`MvcMovie` プロジェクトが選択された状態で表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="20515-320">**[OK]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-320">Press the **Ok** button.</span></span>

<span data-ttu-id="20515-321">**[ライセンスの同意]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="20515-322">必要に応じてライセンスを確認し、 **[同意する]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="20515-323">上記の手順を繰り返して、次の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="20515-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="20515-324">データベース コンテキスト クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="20515-324">Create a database context class</span></span>

<span data-ttu-id="20515-325">データベース コンテキスト クラスは、`Movie` モデルの EF Core 機能 (作成、読み取り、更新、削除) を調整するために必要です。</span><span class="sxs-lookup"><span data-stu-id="20515-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="20515-326">データベース コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生し、データ モデルに含めるエンティティを指定します。</span><span class="sxs-lookup"><span data-stu-id="20515-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="20515-327">*Data* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="20515-327">Create a *Data* folder.</span></span>

<span data-ttu-id="20515-328">次のコードで *Data/MvcMovieContext.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="20515-329">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="20515-330">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="20515-331">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="20515-332">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="20515-332">Register the database context</span></span>

<span data-ttu-id="20515-333">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="20515-334">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20515-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="20515-335">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="20515-336">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="20515-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="20515-337">このセクションでは、DI コンテナーにデータベース コンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="20515-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="20515-338">*Startup.cs* の先頭に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="20515-339">`Startup.ConfigureServices` で次の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-341">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="20515-342">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="20515-343">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="20515-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="20515-344">データベース接続文字列の追加</span><span class="sxs-lookup"><span data-stu-id="20515-344">Add a database connection string</span></span>

<span data-ttu-id="20515-345">接続文字列を *appsettings.json* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-347">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="20515-348">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="20515-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="20515-349">ムービー ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="20515-349">Scaffold movie pages</span></span>

<span data-ttu-id="20515-350">スキャフォールディング ツールを使用し、ムービー モデルの作成、読み取り、更新、削除の (CRUD) ページを生成します。</span><span class="sxs-lookup"><span data-stu-id="20515-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-352">*ソリューション エクスプローラー* で、 **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="20515-354">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="20515-356">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="20515-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="20515-357">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="20515-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="20515-358">**データ コンテキスト クラス:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="20515-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc3.png)

* <span data-ttu-id="20515-360">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="20515-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="20515-361">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="20515-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="20515-362">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-362">Select **Add**</span></span>

<span data-ttu-id="20515-363">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-363">Visual Studio creates:</span></span>

* <span data-ttu-id="20515-364">ムービー コントローラー ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="20515-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="20515-365">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="20515-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="20515-366">このようなファイルの自動作成は、" *スキャフォールディング* " と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="20515-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="20515-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="20515-368">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="20515-369">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="20515-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="20515-370">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-371">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="20515-372">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="20515-373">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="20515-374">データベースが存在しないため、スキャフォールディング ページをまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="20515-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="20515-375">アプリを実行し、 **[Movie App]** リンクをクリックすると、 *[データベースを開けません]* または *[そのようなテーブルはありません:Movie* ] というエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="20515-376">最初の移行</span><span class="sxs-lookup"><span data-stu-id="20515-376">Initial migration</span></span>

<span data-ttu-id="20515-377">EF Core [移行](xref:data/ef-mvc/migrations)機能を使用し、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="20515-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="20515-378">移行は、データ モデルに合わせてデータベースを作成したり、更新したりできる一連のツールです。</span><span class="sxs-lookup"><span data-stu-id="20515-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-380">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="20515-381">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="20515-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="20515-382">`Add-Migration InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="20515-383">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="20515-384">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="20515-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="20515-385">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="20515-386">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="20515-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="20515-387">`Update-Database`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="20515-388">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="20515-389">データベース更新コマンドにより、次の警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="20515-390">エンティティ型 'Movie' の decimal 列 'Price' に型が指定されていません。</span><span class="sxs-lookup"><span data-stu-id="20515-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="20515-391">これにより、値が既定の有効桁数と小数点以下桁数に収まらない場合、自動的に切り捨てられます。</span><span class="sxs-lookup"><span data-stu-id="20515-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="20515-392">'HasColumnType()' を使用してすべての値に適合する SQL server 列の型を明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="20515-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="20515-393">この警告は無視して構いません。後のチュートリアルで修正されます。</span><span class="sxs-lookup"><span data-stu-id="20515-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-394">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="20515-395">次の .NET Core CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="20515-396">`ef migrations add InitialCreate`: *Migrations/{timestamp}_InitialCreate.cs* 移行ファイルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="20515-397">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="20515-398">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="20515-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="20515-399">これは最初の移行であるため、生成されたクラスには、データベース スキーマを作成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="20515-400">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます ( *Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="20515-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="20515-401">`ef database update`:前のコマンドで作成された最新の移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="20515-402">このコマンドにより *Migrations/{time-stamp}_InitialCreate.cs* ファイルで `Up` メソッドが実行され、データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="20515-403">InitialCreate クラス</span><span class="sxs-lookup"><span data-stu-id="20515-403">The InitialCreate class</span></span>

<span data-ttu-id="20515-404">*Migrations/{timestamp}_InitialCreate.cs* 移行ファイルを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="20515-405">`Up` メソッドにより Movie テーブルが作成され、主キーとして `Id` が構成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="20515-406">`Down` メソッドにより、`Up` 移行で行われたスキーマ変更が元に戻ります。</span><span class="sxs-lookup"><span data-stu-id="20515-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="20515-407">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="20515-407">Test the app</span></span>

* <span data-ttu-id="20515-408">アプリを実行し、 **[Movie App]** リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="20515-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="20515-409">次のような例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="20515-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-411">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="20515-412">[移行手順](#migration)を実行しなかった可能性があります。</span><span class="sxs-lookup"><span data-stu-id="20515-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="20515-413">**Create** ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-413">Test the **Create** page.</span></span> <span data-ttu-id="20515-414">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="20515-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="20515-415">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="20515-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="20515-416">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20515-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="20515-417">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="20515-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="20515-418">**[編集]** 、 **[詳細]** 、 **[削除]** の各ページをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="20515-419">コントローラーの依存関係挿入</span><span class="sxs-lookup"><span data-stu-id="20515-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-421">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="20515-422">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="20515-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="20515-423">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-424">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="20515-425">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="20515-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="20515-426">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="20515-427">開発用に SQLite を、運用環境に SQL Server を使用する</span><span class="sxs-lookup"><span data-stu-id="20515-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="20515-428">SQLite が選択されている場合、テンプレートで生成されたコードは開発の準備ができています。</span><span class="sxs-lookup"><span data-stu-id="20515-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="20515-429">次のコードでは、スタートアップに <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> を挿入する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="20515-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="20515-430">`IWebHostEnvironment` が挿入されているので、`ConfigureServices` では開発用に SQLite を、運用環境に SQL Server を使用できます。</span><span class="sxs-lookup"><span data-stu-id="20515-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="20515-431">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="20515-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="20515-432">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="20515-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="20515-433">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="20515-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="20515-434">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="20515-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="20515-435">この厳密に型指定された方法では、コンパイル時にコードを確認できます。</span><span class="sxs-lookup"><span data-stu-id="20515-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="20515-436">スキャフォールディング メカニズムでは、`MoviesController` のクラスとビューで、この手法 (つまり、厳密に型指定されたモデルを渡しました) が使用されました。</span><span class="sxs-lookup"><span data-stu-id="20515-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="20515-437">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="20515-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="20515-438">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="20515-439">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="20515-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="20515-440">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="20515-441">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="20515-442">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="20515-443">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="20515-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="20515-444">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="20515-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="20515-445">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="20515-446">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="20515-447">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="20515-448">ビュー ファイルの一番上にある `@model` ステートメントにより、ビューで求められるオブジェクトの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="20515-449">ムービー コントローラーが作成されたとき、次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="20515-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="20515-450">この `@model` ディレクティブにより、コントローラーでビューに渡されたムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="20515-451">`Model` オブジェクトは厳密に型指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="20515-452">たとえば、 *Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="20515-453">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="20515-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="20515-454">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="20515-455">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="20515-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="20515-456">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="20515-457">ムービー コントローラーが作成されたとき、スキャフォールディングにより、 *Index.cshtml* ファイルの一番上に次の `@model` ステートメントが含まれました。</span><span class="sxs-lookup"><span data-stu-id="20515-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="20515-458">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="20515-459">たとえば、 *Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="20515-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="20515-460">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="20515-461">これには、コンパイル時にコードを確認できるなどの利点があります。</span><span class="sxs-lookup"><span data-stu-id="20515-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20515-462">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="20515-462">Additional resources</span></span>

* [<span data-ttu-id="20515-463">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="20515-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="20515-464">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="20515-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="20515-465">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル SQL の使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="20515-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="20515-466">データ モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="20515-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-468">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="20515-469">クラスに **Movie** と名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="20515-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-470">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="20515-471">*Movie.cs* という名前の *Models* フォルダーにクラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="20515-472">ムービー モデルのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="20515-472">Scaffold the movie model</span></span>

<span data-ttu-id="20515-473">このセクションでは、ムービー モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="20515-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="20515-474">つまり、スキャフォールディング ツールにより、ムービー モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-476">*ソリューション エクスプローラー* で、 **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![前述の手順を参照](adding-model/_static/add_controller21.png)

<span data-ttu-id="20515-478">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用したビューがある MVC コントローラー]、[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![[スキャフォールディングを追加] ダイアログ](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="20515-480">**[コントローラーの追加]** ダイアログ ボックスを完了します。</span><span class="sxs-lookup"><span data-stu-id="20515-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="20515-481">**モデル クラス:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="20515-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="20515-482">**データ コンテキスト クラス:** **+** アイコンを選択して、既定の **MvcMovie.Models.MvcMovieContext** を追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![[データの追加] コンテキスト](adding-model/_static/dc.png)

* <span data-ttu-id="20515-484">**ビュー:** 各オプションの既定値をオンにします。</span><span class="sxs-lookup"><span data-stu-id="20515-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="20515-485">**コントローラー名:** 既定の *MoviesController* のままにします。</span><span class="sxs-lookup"><span data-stu-id="20515-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="20515-486">**[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-486">Select **Add**</span></span>

![[コントローラーの追加] ダイアログ](adding-model/_static/add_controller2.png)

<span data-ttu-id="20515-488">Visual Studio では、次が作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-488">Visual Studio creates:</span></span>

* <span data-ttu-id="20515-489">Entity Framework Core の [データベース コンテキスト クラス](xref:data/ef-mvc/intro#create-the-database-context)( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="20515-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="20515-490">ムービー コントローラー ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="20515-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="20515-491">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー ファイル ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="20515-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="20515-492">データベース コンテキストと [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (作成、読み取り、更新、および削除) アクション メソッドとビューの自動作成は、 *スキャフォールディング* と言います。</span><span class="sxs-lookup"><span data-stu-id="20515-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="20515-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="20515-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="20515-494">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="20515-495">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="20515-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="20515-496">Linux で、スキャフォールディング ツールのパスをエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="20515-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="20515-497">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="20515-498">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="20515-499">プロジェクト ディレクトリ ( *Program.cs* 、 *Startup.cs* 、および *.csproj* ファイルを含むディレクトリ) でコマンド ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="20515-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="20515-500">スキャフォールディング ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="20515-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="20515-501">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="20515-502">アプリを実行し、 **[MVC Movie]\(MVC ムービー\)** リンクをクリックすると、次のようなエラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20515-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-504">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="20515-505">データベースを作成する必要があり、それには EF Core [移行](xref:data/ef-mvc/migrations)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="20515-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="20515-506">移行では、データ モデルに一致するデータベースを作成し、データ モデルの変更時にデータベース スキーマを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="20515-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="20515-507">最初の移行</span><span class="sxs-lookup"><span data-stu-id="20515-507">Initial migration</span></span>

<span data-ttu-id="20515-508">このセクションでは、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="20515-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="20515-509">初期移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="20515-509">Add an initial migration.</span></span>
* <span data-ttu-id="20515-510">初期移行でデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="20515-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="20515-512">**[ツール]** メニューで、 **[NuGet パッケージ マネージャー]** > **[パッケージ マネージャー コンソール]** (PMC) の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="20515-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC メニュー](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="20515-514">PMC で、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="20515-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="20515-515">`Add-Migration` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="20515-516">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="20515-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="20515-517">`Initial` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="20515-518">任意の名前を使用できますが、慣例により、移行を説明する名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="20515-519">詳細については、「<xref:data/ef-mvc/migrations>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="20515-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="20515-520">`Update-Database` コマンドは、データベースを作成する、 *Migrations/{time-stamp}_InitialCreate.cs* ファイルの `Up` メソッドを実行します。</span><span class="sxs-lookup"><span data-stu-id="20515-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-521">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="20515-522">`ef migrations add InitialCreate` コマンドによって最初のデータベース スキーマを作成するコードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="20515-523">データベース スキーマは、`MvcMovieContext` クラスで指定されたモデルに基づきます ( *Data/MvcMovieContext.cs* ファイル内)。</span><span class="sxs-lookup"><span data-stu-id="20515-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="20515-524">`InitialCreate` 引数は、移行の名前です。</span><span class="sxs-lookup"><span data-stu-id="20515-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="20515-525">任意の名前を使用できますが、慣例により、移行を説明する名前が選択されます。</span><span class="sxs-lookup"><span data-stu-id="20515-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="20515-526">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="20515-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="20515-527">ASP.NET Core には、[依存関係挿入 (DI)](xref:fundamentals/dependency-injection) が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="20515-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="20515-528">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に DI に登録されます。</span><span class="sxs-lookup"><span data-stu-id="20515-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="20515-529">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="20515-530">DB コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="20515-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="20515-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20515-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="20515-532">スキャフォールディング ツールが自動的に DB コンテキストを作成し、それを DI コンテナーに登録します。</span><span class="sxs-lookup"><span data-stu-id="20515-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="20515-533">次の `Startup.ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="20515-534">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="20515-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="20515-535">`MvcMovieContext` では、`Movie` モデルのために EF Core 機能 (作成、読み取り、更新、削除など) が調整されます。</span><span class="sxs-lookup"><span data-stu-id="20515-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="20515-536">データ コンテキスト (`MvcMovieContext`) は [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="20515-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="20515-537">データ コンテキストによって、データ モデルに含めるエンティティが指定されます:</span><span class="sxs-lookup"><span data-stu-id="20515-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="20515-538">上記のコードによって、エンティティ セットの [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="20515-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="20515-539">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="20515-540">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="20515-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="20515-541">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="20515-542">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="20515-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="20515-543">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="20515-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="20515-544">DB コンテキストを作成し、それを DI コンテナーに登録しました。</span><span class="sxs-lookup"><span data-stu-id="20515-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="20515-545">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="20515-545">Test the app</span></span>

* <span data-ttu-id="20515-546">アプリを実行し、ブラウザーで URL に `/Movies` を追加します ( `http://localhost:port/movies` )。</span><span class="sxs-lookup"><span data-stu-id="20515-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="20515-547">次のようなデータベースの例外が表示された場合:</span><span class="sxs-lookup"><span data-stu-id="20515-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="20515-548">[移行手順](#pmc)を失敗しました。</span><span class="sxs-lookup"><span data-stu-id="20515-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="20515-549">**[作成]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-549">Test the **Create** link.</span></span> <span data-ttu-id="20515-550">データを入力して送信します。</span><span class="sxs-lookup"><span data-stu-id="20515-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="20515-551">`Price` フィールドに小数点のコンマを入力できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="20515-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="20515-552">小数点にコンマ (",") を使う英語以外のロケール、および英語 (米国) 以外の日付形式で、[jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20515-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="20515-553">グローバル化の手順については、[この GitHub の記事](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="20515-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="20515-554">**[編集]** 、 **[詳細]** 、および **[削除]** の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="20515-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="20515-555">次の `Startup` クラスを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="20515-556">上のコードで強調表示されている部分は、[依存性の注入](xref:fundamentals/dependency-injection)コンテナーに追加されているムービー データベース コンテキストを示します。</span><span class="sxs-lookup"><span data-stu-id="20515-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="20515-557">`services.AddDbContext<MvcMovieContext>(options =>` では、使用するデータベースと接続文字列を指定します。</span><span class="sxs-lookup"><span data-stu-id="20515-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="20515-558">`=>` は[ラムダ演算子](/dotnet/articles/csharp/language-reference/operators/lambda-operator)です。</span><span class="sxs-lookup"><span data-stu-id="20515-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="20515-559">*Controllers/MoviesController.cs* ファイルを開いて、コンストラクターを調べます。</span><span class="sxs-lookup"><span data-stu-id="20515-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="20515-560">コンストラクターでは、[依存性の注入](xref:fundamentals/dependency-injection)を使ってデータベース コンテキスト (`MvcMovieContext`) がコントローラーに挿入されています。</span><span class="sxs-lookup"><span data-stu-id="20515-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="20515-561">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="20515-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="20515-562">厳密に型指定されたモデルと @model キーワード</span><span class="sxs-lookup"><span data-stu-id="20515-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="20515-563">コントローラーで `ViewData` ディクショナリを使ってビューにデータまたはオブジェクトを渡す方法を前に示しました。</span><span class="sxs-lookup"><span data-stu-id="20515-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="20515-564">`ViewData` ディクショナリは動的オブジェクトであり、ビューに情報を渡すための便利な遅延バインディングの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="20515-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="20515-565">MVC にも、厳密に型指定されたモデル オブジェクトをビューに渡す機能があります。</span><span class="sxs-lookup"><span data-stu-id="20515-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="20515-566">この厳密に型指定された方法を使うと、コンパイル時のコードのチェックが向上します。</span><span class="sxs-lookup"><span data-stu-id="20515-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="20515-567">スキャフォールディング メカニズムは、メソッドとビューを作成するときに、`MoviesController` クラスとビューでこの方法 (つまり、厳密に型指定されたモデルを渡すこと) を使いました。</span><span class="sxs-lookup"><span data-stu-id="20515-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="20515-568">*Controllers/MoviesController.cs* ファイルで生成された `Details` メソッドを調べてください。</span><span class="sxs-lookup"><span data-stu-id="20515-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="20515-569">通常、`id` パラメーターはルート データとして渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="20515-570">たとえば、`https://localhost:5001/movies/details/1` は次のように設定します。</span><span class="sxs-lookup"><span data-stu-id="20515-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="20515-571">コントローラーを `movies` コントローラーに (最初の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="20515-572">アクションを `details` に (2 番目の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="20515-573">ID を 1 に (最後の URL セグメント)。</span><span class="sxs-lookup"><span data-stu-id="20515-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="20515-574">次のようにクエリ文字列で `id` を渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="20515-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="20515-575">ID 値が指定されない場合、`id` パラメーターは [null 許容型](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) として定義されます。</span><span class="sxs-lookup"><span data-stu-id="20515-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="20515-576">ルート データまたはクエリ文字列の値と一致するムービー エンティティを選択するため、[ラムダ式](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions)が `FirstOrDefaultAsync` に渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="20515-577">ムービーが見つかった場合、`Movie` モデルのインスタンスが `Details` ビューに渡されます。</span><span class="sxs-lookup"><span data-stu-id="20515-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="20515-578">*Views/Movies/Details.cshtml* ファイルの内容を確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="20515-579">ビュー ファイルの先頭に `@model` ステートメントを含めることで、ビューが期待するオブジェクトの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="20515-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="20515-580">ムービー コントローラーを作成したとき、 *Details.cshtml* ファイルの先頭に次の `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="20515-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="20515-581">この `@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="20515-582">たとえば、 *Details.cshtml* ビューでは、コードで厳密に型指定された `Model` オブジェクトを使って、`DisplayNameFor` および `DisplayFor` HTML ヘルパーに各ムービー フィールドを渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="20515-583">`Create` および `Edit` のメソッドとビューも、`Movie` モデル オブジェクトを渡します。</span><span class="sxs-lookup"><span data-stu-id="20515-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="20515-584">Movies コントローラーの *Index.cshtml* ビューと `Index` メソッドを確認してください。</span><span class="sxs-lookup"><span data-stu-id="20515-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="20515-585">コードで `View` メソッドを呼び出すときの `List` オブジェクトの作成方法に注意してください。</span><span class="sxs-lookup"><span data-stu-id="20515-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="20515-586">コードでは、この `Movies` リストを `Index` アクション メソッドからビューに渡しています。</span><span class="sxs-lookup"><span data-stu-id="20515-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="20515-587">ムービー コントローラーを作成したとき、スキャフォールディングによって *Index.cshtml* ファイルの先頭に `@model` ステートメントが自動的に追加されています。</span><span class="sxs-lookup"><span data-stu-id="20515-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="20515-588">`@model` ディレクティブにより、厳密に型指定された `Model` オブジェクトを使って、コントローラーがビューに渡したムービーのリストにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="20515-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="20515-589">たとえば、 *Index.cshtml* ビューのコードでは、`foreach` ステートメントを使って厳密に型指定された `Model` オブジェクトのムービーをループ処理しています。</span><span class="sxs-lookup"><span data-stu-id="20515-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="20515-590">`Model` オブジェクトは厳密に型指定されているので (`IEnumerable<Movie>` オブジェクトとして)、ループ内の各項目は `Movie` として型指定されます。</span><span class="sxs-lookup"><span data-stu-id="20515-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="20515-591">それ以外の利点としては、コンパイル時にコードのチェックが行われます。</span><span class="sxs-lookup"><span data-stu-id="20515-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20515-592">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="20515-592">Additional resources</span></span>

* [<span data-ttu-id="20515-593">タグ ヘルパー</span><span class="sxs-lookup"><span data-stu-id="20515-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="20515-594">グローバライズとローカライズ</span><span class="sxs-lookup"><span data-stu-id="20515-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="20515-595">[前のチュートリアル ビューの追加](adding-view.md)
> [次のチュートリアル データベースの使用](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="20515-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
